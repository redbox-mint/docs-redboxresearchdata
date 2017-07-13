This page focuses on configuration of the Curation Manager and its various related system components. It forms only one part of the broader [documentation on curation in ReDBox and Mint](documentation-system-overview-curating-linked-data). 
 There is a focus on explanation for much of this document, so that anyone interested in the details can follow a thread of how the system starts and how the various components interrelate to each other. Some considerable parts of this won't ever need to be changed by implementers; and I've tried to briefly summarise which sections you might care about in ***bold, italic text*** at the top of each section. 
#### **[]()The Message Queue
 
***You won't typically ever change these values.*** 
The core of the Curation Manager's work occurs inside the Fascinator's [Transaction Manager Queue Consumer](https://dev.redboxresearchdata.com.au/jenkins/view/Clean%20builds/job/The%20Fascinator%20-%20Clean%20Build/javadoc/com/googlecode/fascinator/messaging/TransactionManagerQueueConsumer.html). The technical details underpinning this are [covered elsewhere](documentation-system-overview-curating-linked-data-curation-implementation), but what we are concerned about here is ensuring that the Queue Consumer does come online when the system starts, and that it loads up our Curation Manager to govern it. We do this in the central '[system-config.json](documentation-system-administration-general-administration-configuration-files-system-config-json)' file: 
 `"messaging": {`
 `    ...`
 `    "toolChainQueue": "**transactionManager**",`
 `    "threads": [`
 `        {`
 `            "id": "**transactionManager**",`
 `            "description": "Transaction Manager Queue for complicated tool chains.",`
 `            "priority": "5",`
 `            "config": {`
 `                "name": "**transactionManager**",`
 `                "transactionManagerPlugin": "**curation-redbox**"`
 `            }`
 `        },`
 `        ...`
 `    ]`
 `},`  
The most important sections above are highlighted in red, and we clarify their purpose below: 
 * '`toolChainQueue`' tells the core parts of The Fascinator platform that if they ever want to send an object into the tool chain, they should send it to the queue named '`transactionManager`'. By doing this we have quite trivially replaced a typical Fascinator tool chain with our own implementation.
 * '`name`` is the configuration for this particular thread, defining which queue it is listening on. For obvious reasons we want it to align to the '`toolChainQueue`' above, so that messages are actually received here. This value also is used in logging, so you will find a '`transactionManager.log`' in [your logging directory](documentation-system-administration-general-administration-system-layout) replacing all of the typical tool chain logs as well.
 * '`id`' happens to look like the queue name used above, but this is a coincidence. In this case we are talking about an identifier used by the system to find this message queue out of the Java Service Loader at boot time... don't change it :)
 * '`transactionManagerPlugin`' tells our queue consumer which particular plugin implementation we want to use as the 'brains' of our tool chain. This is another identifier that lines up with the two '`Curation``Manager`` objects inside ReDBox and Mint, so it will differ between the systems. ie. '`curation-redbox`' versus '`curation-mint`'.
  
#### **[]()The Curation Manager
 
***This is the first point of changes IF you are customising your installation in a fairly standard way. For example, removing VITAL integration, changing form data that defines relationships or adding a confirmation step to the curation process.*** 
Once the Curation Manager is online it needs to read its own configuration as well, and this is found under the '`curation`' node: 
 `"curation": {`
 `    "curationEmailAddress": "${admin.email}",`
 `    "curationRequiresConfirmation": false,`
 `    "pidProperty": "vitalHandle",`
 `    "mintBroker": "${mint.amq.broker}",`
 
 `    "relations": {`
 `        ...`
 `    },`
 `    "reverseMappings": {`
 `        ...`
 `    }`
 `},`  
The first three fields are common to both ReDBox and Mint: 
 * '`curationEmailAddress`': This is required, although it should trickle through from the [control script](documentation-system-administration-general-administration-configuration-files-control-scripts). You can however set this to a separate value if you want a different contact point for curation. This email address will be contacted if errors occur, or the next value below is set.
 * '`curationRequiresConfirmation`': Setting this flag to '`true`' (default is '`false`') will send an email to the above address requesting confirmation from a staff member before an object is ever curated. How this fits into the workflow-like process is [documented elsewhere](documentation-system-overview-curating-linked-data-curation-data-model).
 * '`pidProperty`': The is a metadata property set against the object where the `CurationManager` should expect to find a persistent identifier after curation has occurred. This needs to align with whatever curation Transformer is in use (see 'Data Source Configuration' below). In that case of ReDBox this [by default](documentation-system-administration-administering-redbox-vital-integration) '`vitalHandle`', or on Mint (which [directly accesses the Handle network](documentation-system-administration-administering-mint-handle-server)) it is '`handle`'. This is where you expect to add your own local value, such as a DOI implementation to pair with your Transformer.
  
And the last three fields are specific to ReDBox: 
 * '`mintBroker`': ReDBox needs to know how to find the Mint server's AMQ broker to send messages to it. This should flow through from the [control script](documentation-system-administration-general-administration-configuration-files-control-scripts).
 * '`relations`': This node tells the Curation Manager how to build the relationships it needs to curate, based on the data observed in the curated form data from your data entry staff. There is a lot of configuration here, and it is covered in more detail below under 'ReDBox Form Data Relations'.
 * '`reverseMappings`': This is fairly trivial, but allows for customisations. Whenever you specify a relationship ReDBox will notify the other object of its relationship should be back to this object. eg. A related Party record listed as '`hasCollector`' needs to have a relationship to this Collection of '`isCollectorOf`'. The default values are probably ok, and they are taken from the [ANDS Controlled Vocabulary](http://services.ands.org.au/documentation/rifcs/1.2.0/schema/vocabularies.html), but if you'd like to alter the exact mappings, or perhaps you are using a different vocabulary for some reason, here is where you do it.
  
After this, each data source has its own configuration, indicating to the Curation Manager how it likes to be transformer and curated; leading into the next section... 
#### **[]()Data Source Configuration
 
***Changes to these files would be most likely if you are altering the behaviour of your system and/or adding your own Curation Transformer plugin (such as DOI for example).*** 
Inside the JSON configuration for a particular data source you'll find more configuration that the Curation Manager will be looking for. Most sections of this site assume your configuration will occur in Mint, but you should be aware that ReDBox also has such a file, it is just used by the system rather than an administrator doing command line ingests. 
Let's look at the ReDBox example first, since there is only one there: 
 `"transformer": {`
 `    "curation": ["vital"],`
 `    "metadata": ["jsonVelocity"]`
 `},`
 `"curation": {`
 `    "neverPublish": false,`
 `    "alreadyCurated": false`
 `},`  
The '`transformer`' node is pretty simple, although it is a departure from a [traditional Fascinator tool chain](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain). We basically just provide two lists of configured transformers (they have to present in the [central configuration](documentation-system-administration-general-administration-configuration-files-system-config-json) as well) that the Curation Manager run based on the situation; '`metadata`' Transformers will run under most normal circumstances (ie. during reindex/reharvest events, or following ingest), whilst '`curation`' transformers will only run in response to a curation or publication request. Our current implementations in ReDBox and Mint never run more than one '`curation`' Transformer, but this design allows for multiple if a use case appears. 
So for the above example, you can see that most normal activity in the system will cause ReDBox objects to pass through the [JSON Velocity Transformer](documentation-system-administration-general-administration-metadata-templates) ('`jsonVelocity`'), whilst the [VITAL Transformer](documentation-system-administration-administering-redbox-vital-integration) ('`vital`') is executed if the object is ever curated and/or published. 
The final top-level node in this file is '`curation`' with two boolean flags: 
 * '`neverPublish`': If this flag is set the Curation Manager should never set the metadata property '`published`' that the various indexing rules files look for to set a published flag in the Solr index. **ONLY** if this flag is found to be set in the Solr Index will the object be publically exposed on the 'published' portal (intended for use by ANDS harvesters).
 * '`alreadyCurated`': This flag is used to indicate that this object does not need to go through a curation process. This is useful in contexts where local identifiers, or external identifiers you have integrated into your system are going to be used to create the curated relationships in your system.
  
One example that uses both flags above would be for Mint Activities. Out-of-the-box we provide the ARCS and NHRMC Activities from the publicly available spreadsheets, but we also know that ANDS will be providing the RIF-CS Activity records in RDA under well defined URIs we can predict (they are a static prefix on the ID provided by ARCS and NHMRC). So to accommodate this we want to tell the Curation Manager to use the identifier we already have from ingest (the harvester adds the prefix for us); we also want to tell it that no curation Transformer is required and, finally, that when it comes time to publish there is no need to send these objects to ANDS. So the configuration looks something like this: 
 `"transformer": {`
 `    "curation": [],`
 `    "metadata": []`
 `},`
 `"curation": {`
 `    "neverPublish": true,`
 `    "alreadyCurated": true`
 `},`  
You'll note as well, that since we aren't sending the object to ANDS, we don't even bother allocating a '`metadata`' Transformer either, because we don't need to render any RIF-CS. There are also of course more interesting examples from Mint as well, if we were to look at Parties People: 
 `"transformer": {`
 `    "curation": ["handle"],`
 `    "metadata": ["ingest-relations", "jsonVelocity"]`
 `},`
 `"curation": {`
 `    "neverPublish": false,`
 `    "alreadyCurated": false`
 `},`  
So in this example we've removed both of the '`curation`' flags to ensure that the Curation Manager does indeed take action for these records. We've also added the '`handle`' Transformer for curation, since Mint [uses the Handle network directly](documentation-system-administration-administering-mint-handle-server), rather than via VITAL as ReDBox does. And finally, we have the brand new '`ingest-relations`' Transformer [that is part of Mint](documentation-system-administration-administering-mint-ingested-relationships); its role is to create relationships between People and Groups at ingest time (although you should be able to configure it to do more than that if you desire). 
#### **[]()ReDBox Form Data Relations
 
***This section is something you definitely want to take a look at if you are changing your form data.*** 
Earlier we skimmed over this part of the configuration, and it is now time to go back and see what this section looks like (and what it does). Below is shown one of the more complicated examples in isolation. You can ignore the top level '`relations`' node, it is just shown for the sake of completeness: 
 `"relations": {`
 `    "dc:relation.vivo:Dataset.0.dc:identifier": {`
 `        "path": ["dc:relation", "vivo:Dataset"],`
 `        "identifier": ["dc:identifier"],`
 `        "relationship": ["vivo:Relationship", "rdf:PlainLiteral"],`
 `        "excludeCondition": {`
 `            "path": ["redbox:publish"],`
 `            "value": ""`
 `        },`
 `        "system": "redbox",`
 `        "optional": true`
 `    },`
 `    ...`
 `},`  
Each object under '`relations`' defines a single form field that might contain relationship data. The key for each object is the base field name [used in our documentation](http://code.google.com/p/redbox-mint/wiki/FormFieldsCompleteList) and throughout the codebase to refer to the field without referencing specific instances. For example '[dc:relation.vivo:Dataset.0.dc:identifier](http://code.google.com/p/redbox-mint/wiki/FormFieldsCompleteList#dc:relation.vivo:Dataset)' (note the zero) above refers to the base field, whilst '`dc:relation.vivo:Dataset.1.dc:identifier`' (note the one) would be referring to the first piece of data contained therein. Keys should **always** be base fields, as they are used as part of the de-duplication process and individually numbered fields may change order (so '1' could be '2' the next time we see it) over the lifetime of an object if curation staff are moving data around. 
Once we've got a key as a base field however the actual configuration for that object is very concerned with individual instances, in particular with keeping them separate from one another. We'll demonstrate how in a minute, but as a broad overview this configuration above is saying that for any **related dataset** ('`dc:relation`' > '`vivo:Dataset`') we should publish the **provided identifier** ('`dc:identifier`') to ANDS **only if requested** ('`redbox:publish`') and we should use the **type of relationship** ('`vivo:Relationship`' > '`rdf:PlainLiteral`') also provided in the form data. Because we don't want to be looking at the incorrect subfield in relation to the wrong identifier, this object (as stated before) is very concerned about keeping individual instances separate from one another. ie. You don't want to accidentally check '`*.2.edbox:publish`' when mapping '`*.1.dc:identifier`'. 
To achieve this our configuration is basically structure to indicate how the Curation Manager should traverse a hierarchy of JSON nodes when reading the form data. Here's some sample form data so this all makes sense: 
 `"dc:relation.vivo:Dataset.1.dc:identifier": "http://hdl.handle.net/10880/redbox:3",`
 `"dc:relation.vivo:Dataset.1.vivo:Relationship.rdf:PlainLiteral": "hasPart",`
 `"dc:relation.vivo:Dataset.1.vivo:Relationship.skos:prefLabel": "Has part:",`
 `"dc:relation.vivo:Dataset.1.dc:title": "Study on Random Stuff",`
 `"dc:relation.vivo:Dataset.1.skos:note": "This smaller study... blah, blah, blah",`
 `"dc:relation.vivo:Dataset.1.redbox:origin": "on",`
 `"dc:relation.vivo:Dataset.1.redbox:publish": "on",`
 
 `"dc:relation.vivo:Dataset.2.dc:identifier": "http://hdl.handle.net/10880/redbox:4",`
 `"dc:relation.vivo:Dataset.2.vivo:Relationship.rdf:PlainLiteral": "hasAssociationWith",`
 `"dc:relation.vivo:Dataset.2.vivo:Relationship.skos:prefLabel": "Has association with:",`
 `"dc:relation.vivo:Dataset.2.dc:title": "Another study in ReDBox",`
 `"dc:relation.vivo:Dataset.2.skos:note": "This other study we can't publish has...",`
 `"dc:relation.vivo:Dataset.2.redbox:origin": "on",`
 `"dc:relation.vivo:Dataset.2.redbox:publish": "",`  
So the curation manager will: 
 1. Access the '`path`' configuration and traverse the JSON hierarchy to '`dc:relation`' > '`vivo:Dataset`'.
 1. Here it finds an array/list with two objects inside it, so it performs the rest of these steps on BOTH independent of one another.
 1. Access the '`identifier`' configuration and look in the '`dc:identifier`' field to find a Handle.
 1. Access the '`relationship`' configuration and look in '`vivo:Relationship`' > '`rdf:PlainLiteral`' field to find HOW these records are related. In this case object 1 is indicating that another ReDBox Collection is consider to be part of this one ('`hasPart`'). Object 2 has an undefined relationship: '`hasAssociationWith`'.
 1. Access the '`excludeCondition`' > '`path`' configuration and look in '`redbox:publish`' to decide if this relationship should be excluded or included. In this case object 1 has the value 'on' and Object 2 has nothing.
 1. Finally, access the '`excludeCondition`' > '`value`' configuration and compare it to what we found in step 5. In this case we are excluding the relationship if the value matches nothing (an empty String). So object 2 will be excluded, whilst object 1 will proceed with curation.
  
This relation also has some additional operational configuration telling that the '`system`' this object is found in is ReDBox (the default is Mint, so you only need to define this when ReDBox wants to link to its own records). And we also have an '`optional`' flag set, which is also not normal. If this wasn't set the main object would halt its own publication process until the object on the other side of the relationship had cleared curation. By setting this flag we are telling ReDBox that it shouldn't wait around on other ReDBox records; although we do expect it to wait on Mint records to clear curation. 
Keep in mind that the example above is quite a complicated example; MOST records are not like this, in fact most records just have the first three basic values of '`path`', '`identifier`' and '`relationship`' and rely on sensible default values to handle the rest. Below I've added another example, which is much simpler, but it does demonstrate to key points: 
 `"relations": {`
 `    ...`
 `    "locrel:prc.foaf:Person.dc:identifier": {`
 `        "path": ["locrel:prc", "foaf:Person"],`
 `        "identifier": ["dc:identifier"],`
 `        "relationship": "hasAssociationWith",`
 `        "description": "Primary Contact"`
 `    },`
 `    ...`
 `},`  
 1. The '`relationship`' configuration does not have to provide a JSON path, it can provide a static relationship. The difference is the lack of square brackets around '`hasAssociationWith`'. In this case we have a very generic relationship (see the next point), but other fields (like creator) will define more specific ones like '`hasCollector`'.
 1. The '`description`' configuration is optional, and allows you to provide a value for the RIF-CS '`<description/>`' node that is an optional child of a '`<relatedObject/>`'. This allows you to provide more clarity to generic relationships if the [ANDS vocabulary](http://services.ands.org.au/documentation/rifcs/1.2.0/schema/vocabularies.html) doesn't have exactly what you are looking for.
  
And that should be it. I hope your brain is intact... this is a particularly difficult topic to explain, but we've endeavoured to make the system as configurable as possible so that implementers can get a lot of fine-grained control without needing to hire a Java developer.