This page deals with where (and how) OAI-PMH Harvesters interact with Mint and/or ReDBox. 
#### **[]()The Basics
 
The most basic information you'll need is the base URL, which can be found under '`feed/oai`' in any given portal. For example: 
        http://redbox.adfi.usq.edu.au/mint/Parties_Groups/feed/oai 
From there obviously you'll need to do all the fun OAI-PMH 'stuff' to get it to work, eg: 
        http://redbox.adfi.usq.edu.au/mint/Parties_Groups/feed/oai?verb=ListRecords&metadataPrefix=rif 
#### **[]()What does that URL mean?
 
 * At the beginning of the URL we obviously have the domain name... presumably that needs no explaining.
 * Then we have the context path '`mint`'. In Java servlet terms, the '`mint`' context path matches the Mint WAR file we've deployed to run the system (ie. our application).
 * Next we have the 'portal', a specific subset of data defined inside the application by administrators. In this case we are using the '`Parties_Groups`' portal from the Mint.
 * Finally we have the resource from our [web portal](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/portal/jython-and-velocity), in this case it is the '`oai`' resource in the '`feed`' folder.
  
#### **[]()Where does the data come from?
 
There are two significant aspects of the system that are relevant here: 
 1. Which objects are put into the feed?
 1. Where do we decide what data to send in relation to each object?
  
In answer to the first question, basically we send out any object that is publicly available (ie. has 'guest' access), and within the date range requested by the OAI-PMH request. In Mint 'guest' access is given to all resources at this stage (it is a lookup system), but in ReDBox only objects that have reached the last stage of the workflow ('published') are given 'guest' access. The date range is determined by looking at the '`last_modified`' field in the Solr index, which is reset each time the object gets indexed: 
 `self.utils.add(self.index, "last_modified", time.strftime("%Y-%m-%dT%H:%M:%SZ", time.gmtime()))`  
For the second question, we refer back to the [original OAI-PMH documentation](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/oaipmh) for The Fascinator platform. We are using the approach of pre-rendered templates (templates have [separate doco](documentation-system-administration-general-administration-metadata-templates)), so each metadata prefix (eg. '`rif`' for RIF-CS) corresponds to a payload inside the object (like '`rif.xml`'). There is also the issue of identifiers for ReDBox because we want our templates to contain the VITAL handles we got during [VITAL integration](documentation-system-administration-administering-redbox-vital-integration), and they need to match the IDs the OAI feed is sending out. To that end we make sure that we set the 'oai_identifier' during the index process: 
 `        vitalHandle = self.params["vitalHandle"]`
 `        if vitalHandle is not None:`
 `            self.utils.add(self.index, "vitalHandle", vitalHandle)`
 `            self.utils.add(self.index, "oai_identifier", vitalHandle)`  
and this matches the handle from the template: 
 `    #set ($handle = $util.getMetadata($object, "vitalHandle"))`
 `    #if ($handle != "")`
 `        <rif:key>$handle</rif:key>`
 `    #else`
 `        <rif:key>$urlBase/detail/$oid</rif:key>`
 `    #end`