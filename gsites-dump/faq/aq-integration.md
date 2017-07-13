[[_TOC_]]



## []()Q: Are Mint and ReDBox closely tied to each other? Can they work independently? I'd heard that they're co-dependent.

**A: **We work to make sure ReDBox and Mint are decoupled - it's good practice. Mint and ReDBox run as two separate systems and interaction between the two are via standard web-based or messaging calls. This means that neither really care what's on the other end, just that they get what they've asked for. It's also important to remember that both systems use a plug-in based architecture that lets you add and remove features as you need as well as create custom plug-ins for your organisation.


Whilst you could run one without the other, you are likely to need to do some development to get it working at the same level as the (awesome) Mint-ReDBox combo. In essence:

* Mint is a name authority/vocabulary service that provides interfaces for looking up things such as people and subject codes as well as publishing this information
* ReDBox provides a data entry form and workflow system for creating and publishing collection metadata

To lookup a person's identifier when creating a collection record, ReDBox queries Mint using a URL-based query (such as [http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith](http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith)). This means that you can use Mint from other software that needs to look up identifiers - handy!


All this seems easy enough but it's when they work together to publish correctly linked information that you start to realise how much the ReDBox-Mint combo is doing for you. When you publish a record, ReDBox and Mint do a lot of curation work to make sure that you don't end up sending out broken links. The details around this are covered in "How does curation work?".


We've put some considerable thought and effort into getting this to work and its very easy to get ReDBox and Mint up and running. Why not start by giving them a spin and seeing if you can avoid extra development?


See also: 

* [System Overview](documentation-system-overview)
* [Curation FAQ](faq-curation)



### []()Q: So, could you just install Mint and not ReDBox?

You could do this if you really did just need a name authority/vocab service. You can upload the data into Mint and have your various tools (such as those built under ANDS AP and DC projects) use Mint to determine identifiers for people, activity information etc. Naturally, your replacement for ReDBox (if you wanted one) would publish collection record metadata. We'd love to see some institutional repositories working with Mint to get author identification across data and publication holdings...


One thing to consider though, is the curation model. If you still wanted Mint to undertake the role of publishing this information, you may need to kick off the curation process from another source. It's really important to remember that Mint is quite configurable so you may choose a different curation process (you may get NLA IDs another way and just add them to the Mint party record) so you may not need to do much curation at all. 
### []()Q: How about ReDBox without Mint? (e.g. run our own name authority software)

This would be a bit more work. In a nutshell you'd have to provide a query interface in your name authority system that allows ReDBox to lookup things such as parties, services, activities, FORs etc. This shouldn't be overly difficult but if you want to interact with the world in the way Mint does, you'll need to consider how you will:

* Publish party records (as RIF-CS or to the National Library of Australia)
* Publish RIF-CS records for services and activities

In some cases these really are non-trivial so a handy suggestion is to use Mint to relay between your name authority system and ReDBox and use Mint to send out metadata to the rest of the metadata interchange ecosystem. Mint just needs CSV files every now and then to stay up to date - it's reasonably easy to get going.

## []()Q: What happens when I want to update my data in Mint?
Most institutions are populating Mint Parties from other systems in the organisation (such as HR). Mint's [data loading processes](documentation-system-administration-administering-mint-loading-data) were designed with the assumption in mind that periodic updates would be required to synchronise Mint's data with this external source. Simply re-run the ingest tool with updated data and it will update the records you have in Mint.


Please refer to [Loading data into Mint](documentation-system-administration-administering-mint-loading-data)

### []()Q: How does it match up the old data and the new?
The uniqueness test for Parties takes into account the name of the CSV file used during ingest, and the ID column used in this file. Without an ID the system will fall back to a row number... but this is fraught with problems in the longer term. The best advice we can give is to use a CSV file name with meaning (eg. `hr_parties_people.csv`) and make sure you have a decent identifier for each record that will be the same next time you upload (a payroll number or some other HR ID string is typical).

Changing either the filename or the value of a person's ID will cause Mint to consider them a new person, thus generating a new Mint record.

### []()Q: What about extra data in the system, like my curated identifiers?
The ingest process only overwrites the core metadata payload for a Party. Additional information, such as Handles and National Library Identifiers are stored as object properties that are not touched by the ingest process.


## []()Q: Can I import collections records automatically into ReDBox from other systems?

You sure can! Check out [Loading data into ReDBox via Alerts](documentation-system-administration-administering-redbox-configuring-alerts) about loading metadata from and XML document. You can also import CSV files in the same manner you do with Mint.
 
This can be handy in a number of situations:

* Your researchers run their own repository system - it could just flick you records and save re-entry
* Your research storage system detects something is about to be published

## []()Q: Can I import RIF-CS directly into ReDBox or Mint?

We have done work around this:

* You can harvest XML (such as RIF-CS) into ReDBox: [Loading data into ReDBox via Alerts](documentation-system-administration-administering-redbox-configuring-alerts)

 * The problem is that there can be a lot of variance across RIF-CS record. A sample config and rules file are available for customising to harvest RDA Collection records into ReDBox
 * See  [http://code.google.com/p/redbox-mint/source/browse/#svn%2Fredbox%2Ftrunk%2Fconfig%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fharvest](http://code.google.com/p/redbox-mint/source/browse/#svn%2Fredbox%2Ftrunk%2Fconfig%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fharvest)
* [Harvesting RDA Parties into Mint](http://code.google.com/p/redbox-mint/wiki/DevServerRDAExperiment)

## []()Q: What formats or protocols can be used to import records into ReDBox?

ReDBox and Mint has harvesters (plugins that read in data) for CSV and XML files via the filesystem. There's also and OAI-PMH harvester that can be configured to align feed data into the correct Mint/ReDBox metadata.


The system is based on a plug-in model so you could add a new Harvester to get data by querying a database (for example). This can get a bit technical but if that doesn't worry you, check out:

* Available plugins: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins).
* How the underlying platform works:  [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain/basics](https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain/basics)

 * This is useful if you'd like to integrate a messaging service established at your organisation.