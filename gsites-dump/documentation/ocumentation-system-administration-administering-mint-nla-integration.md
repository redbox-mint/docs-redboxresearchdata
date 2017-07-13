[[_TOC_]]



## []()Introduction
Mint provides integration with the National Library of Australia's [People Australia (Trove)](http://trove.nla.gov.au/people) system. This provides globally unique identifiers to people and groups.


This document provides a technically oriented overview of how Mint interacts with the NLA. Whilst it will mention curation, please refer to [Curating Linked Data](documentation-system-overview-curating-linked-data) for a more in-depth overview of the topic. A [high-level diagram](http://www.redboxresearchdata.com.au/documentation/system-overview/curating-linked-data/mint-nla.pdf?attredirects=0&d=1) is provided on that page that is a handy reference for those wanting to determine information flows.

## []()Providing the NLA with party records

The NLA utilises the [Encoded Archival Context for Corporate Bodies, Persons, and Families (EAC-CPF)](http://eac.staatsbibliothek-berlin.de/) standard for describing parties. Records are provided from Mint to the NLA via the [OAI-PMH protocol](http://www.openarchives.org/pmh/). Providing the demonstration Mint system has data in the correct state, a sample of this feed can be seen at [http://demo.redboxresearchdata.com.au/mint/NLA_Harvest/feed/oai?verb=ListRecords&metadataPrefix=eac-cpf](http://demo.redboxresearchdata.com.au/mint/NLA_Harvest/feed/oai?verb=ListRecords&metadataPrefix=eac-cpf).


Once the NLA has harvested the OAI-PMH feed, the NLA's system will attempt an automatic match to determine if the record already exists in Trove. If not, the Trove Identity Manager (TIM) is used by an operator who will either locate an existing NLA record or create a new NLA record (with a new ID). 


NOTE: It is important to be aware that the Mint system has no useful method for determining when the record has been harvested. Furthermore, the NLA Trove system does not message Mint in any way to advise that an NLA ID has been matched or created. In order to determine the NLA ID for a record, Mint must poll the NLA's SRU interface to discover once an ID is available. Essentially, this is a very asynchronous process with an indeterminate timespan.
### []()Configuring Mint

NLA integration is configured in Mint via the system-config,json file. The "curation" section is the section you'll need:





            "curation": {
                "curationEmailAddress": "${admin.email}",
                "curationRequiresConfirmation": false,
                "pidProperty": "handle",
                "nlaIntegration": {
                    "enabled": true,
                    "pidProperty": "nlaPid",
                    "agencyCode": "TO-DO",
                    "agencyName": "The University of Examples, Australia",
                    "includeTest": {
                        "repository.name": "People"
                    }
                }
            },
        
***Key******Values******Description***enabledtrue | false Enables NLA integrationpidPropertynlaPid (default)A metadata element used to hold the NLA's Party ID
(shouldn't need to be changed)
nlaPid is set on an object's TF-OBJ-METAagencyCodeStringThis is the NUC/ISIL code for the institutionagencyNameStringA text descriptor for the institution

### []()Providing records to the NLA

The flow of events within a Mint system is described in the sub-sections below.
#### **[]()1. The record is curated

Party records are made available to the NLA for harvest upon curation within the ReDBox-Mint system. Mint also provides a "Curate now" button to the admin user to allow records to be made available to the NLA without being triggered via ReDBox curation. 
#### **[]()2. The NLA harvest the OAI-PMH feed from Mint


The Mint system contains an EAC-CPF template for Party-People records under [home/templates/people/eac-cpf.vm](https://github.com/redbox-mint/mint/blob/master/config/src/main/config/home/templates/people/eac-cpf.vm). This is a basic velocity template that can be extended as needed. The reasoning behind providing a basic template is based in every site needing to make their own determination regarding privacy etc. The configuration of the OAI feed is located in the "oai-pmh" section of the "portal" configuration in system-config.json. For the most part, you're not likely to need to edit this.


Key to this process is the identifiers being exchanged. The basic EAC-CPF template places the person's "pid" (found in the TF-OBJ-META for the object/record) in the following code:
 

        <cpfDescription> 
                `<identity>` 
                    `<entityId>$``pid``</entityId>`

The value of the $pid is the identifier used for the person, based on local configuration. For example, if a site is using Handles then the pid will be the Handle created for the person during curation.


In the <control> element of the EAC-CPF template another identifier is provided:
 

        <control> 
                `<recordId>$``oid``</recordId>`
This is purely the record identifier managed internally to Mint. The value of $oid is based on the value of the objectId element (found in the TF-OBJ-META for the object/record).


For some background regarding identifiers in the system, please refer to [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/object-life-cycle#TOC-Identifiers](https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/object-life-cycle#TOC-Identifiers)


Feeds to the NLA are provided via the NLA_Harvest view in Mint. This view is defined with the following query:

        repository_type:"Parties" AND repository_name:"People" AND ready_for_nla:"ready"
You can see that ready_for_nla provides a flag to include a record in the NLA Harvest. This value is located in the TF-OBJ-META for the object and is set by the Mint curation manager. For those really wanting to get into the technical detail, the key code pertaining to Mint curation is located in "[The Mint - Plugin - Transaction - ](goog_1005982116)[Curation](https://github.com/redbox-mint/mint/tree/master/plugins/transaction/curation)" plugin.


Once a record is visible in the NLA_Harvest view, it is available to the NLA Harvester. The timing for harvests by the NLA will depend on the configuration that the Mint site has requested of the NLA as well as any backlog processing occurring at the NLA. This may cause some lag before the record is harvested and 
#### **[]()3. NLA matching 

This is completely out of Mint's domain. A record may be automatically matched or need a manual resolution via TIM.
#### **[]()4. Mint picks up the new NLA ID

Mint has to poll the NLA to determine if a match (auto or manual) has been made. This is performed via a houseKeeping (Mint's internal cron-like system) script: [portal/default/mint/scripts/hkjobs/nla.py](https://github.com/redbox-mint/mint/tree/master/config/src/main/config/portal/default/mint/scripts/hkjobs). Essentially, this script queries the NLA's [SRU](http://www.loc.gov/standards/sru/) interface to determine if the Mint record has been allocated to an NLA record. 
To start with, the nla.py script queries the Mint's (SOLR) index to find records marked as ready_for_nla and not carrying an nlaPid. For each record returned, a query is made to the NLA, looking something like:
[`http://www-test.nla.gov.au/apps/srw/search/peopleaustralia?version=1.1&operation=searchRetrieve&recordSchema=urn:isbn:1-931666-33-4&recordPackaging=xml&query=rec.identifier%3D%22e0987ca265084b88243a3196dbbfb61b%22`](http://www-test.nla.gov.au/apps/srw/search/peopleaustralia?version=1.1&operation=searchRetrieve&recordSchema=urn:isbn:1-931666-33-4&recordPackaging=xml&query=rec.identifier%3D%22e0987ca265084b88243a3196dbbfb61b%22)

At the end of the query you'll notice that the rec.identifier provided is an oid, as provided as a RecordID in the EAC metadata. For those wishing to go deeper, the nla.py utilises the [SRUClient plugin](https://github.com/redbox-mint/mint/tree/master/plugins/sru/sruclient) in Mint.
If the NLA returns a match, the nla.py script will add the NLA ID to the Mint record's metadata and signal to the curation system that the record has been successfully curated.
[](goog_890760385)
[](http://www-test.nla.gov.au/apps/srw/search/peopleaustralia?version=1.1&operation=searchRetrieve&recordSchema=urn:isbn:1-931666-33-4&recordPackaging=xml&query=rec.identifier%3D%22e0987ca265084b88243a3196dbbfb61b%22)

## []()Resources

For further details regarding the NLA's Party Infrastructure Project, please refer to [https://wiki.nla.gov.au/display/ARDCPIP/ARDC+Party+Infrastructure+Project+Home](https://wiki.nla.gov.au/display/ARDCPIP/ARDC+Party+Infrastructure+Project+Home)


For information regarding how to request and test an NLA feed, please refer to the following:

* [http://metadata-stores.blogspot.com.au/2012/12/how-to-set-up-your-nla-trove-account.html](http://metadata-stores.blogspot.com.au/2012/12/how-to-set-up-your-nla-trove-account.html)
* [http://metadata-stores.blogspot.com.au/2012/08/testing-your-nlatrove-party.html](http://metadata-stores.blogspot.com.au/2012/08/testing-your-nlatrove-party.html)

For details regarding Mint and network configuration to allow NLA harvest/queries, please refer to the following mailing list posts:

* [https://groups.google.com/d/topic/redbox-dev/odS2GDPG5Sg/discussion](https://groups.google.com/d/topic/redbox-dev/odS2GDPG5Sg/discussion)
* [https://groups.google.com/d/topic/redbox-dev/BLQSzYBB5Zw/discussion](https://groups.google.com/d/topic/redbox-dev/BLQSzYBB5Zw/discussion)