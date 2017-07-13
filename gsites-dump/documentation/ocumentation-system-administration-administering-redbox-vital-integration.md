#### **[]()Older Version
 
Prior to version 1.2, the VITAL Transformer was actually a Subscriber plugin. [Legacy documentation has been archived for this](documentation-system-administration-administering-redbox-vital-integration-legacyvitalintegration). 
#### **[]()Current Version
 
 
This page details configuration for VITAL integration with ReDBox, along with background information on the design. All of this work hinges on the use of the VITAL Transformer plugin, [documented elsewhere](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/vital-fedora-transformer).
Be aware that in v1.3, this Transformer was actually split into two, depending on the version of Fedora (v2.x or v3.x) that underpins your VITAL installation. There is a [separate page](documentation-system-administration-administering-redbox-vital-integration-fedora2-vs-fedora3) detailing this, given that both versions are very (very, very) similar, this page does not deal with this distinction.
 
## []()Configuration
 
The overall configuration for the [VITAL Transformer](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/vital-fedora-transformer) can be quite complicated, but most of the important details are already in place. Basically you should just need to edit you [system-config.json](documentation-system-administration-general-administration-configuration-files-system-config-json) and add the details for your VITAL server and administrative email address: 
 `    "transformerDefaults": {
         "vital": {
             "id": "vital",
             "tempDir": "${java.io.tmpdir}/rbvital",
             "foxmlTemplate": "${fascinator.home}/vital/foxml_template.xml",
             "waitConditions": {
                 "property": "published"
             },
             "failure": {
                 "emailQueue": "emailnotification",
                 "emailAddress": ["${admin.email}"],
                 "emailSubject": "VITAL Integration Failed",
                 "emailTemplate": "This is an automated message from ReDBox.\n\nThe record titled: '[[TITLE]]' has had an error occur during transmission to VITAL.\n\nThe record's detail page is here: ${server.url.base}default/detail/[[OID]]\n\nError details are below:\n[[MESSAGE]]\n\n====================\n\n[[ERROR]]"
             },
             "server": {
                 "url": "http://localhost:8888/fedora/",
                 "username": "fedoraAdmin",
                 "password": "fedoraAdmin",
                 "namespace": "redbox",
                 "message": "Datastream update from ReDBox '[[OID]]'",
                 "timeout": "15"
             },
 ``            ...`
 `        }`
 `    }`
  
You can tweak any of the other values you like by preference, but the keys to getting the system functioning are under '`server`' (knowing where your VITAL server is and how to connect) and '`failure > emailAddress`' (knowing who to notify when things don't work). 
## []()Disabling
 
Disabling VITAL integration is simple, just delete the '`vital`' node in the above configuration for '`transformerDefaults`'. 
You probably also want to remove the housekeeping job: 
 `        "houseKeeping": {`
 `            "config": {`
 `                ...`
 `                "jobs": [`
 `                    {`
 `                        "name": "vital-handles",`
 `                        "type": "external",`
 `                        "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/vital.script",`
 `                        "timing": "0 0/15 * * * ?"`
 `                    }`
 `                ]`
 `            }`
 `        },`
  
becomes: 
 `        "houseKeeping": {`
 `            "config": {`
 `                ...`
 `                "jobs": [`
 `                ]`
 `            }`
 `        },`
  
Finally, we also want to make sure that specific data sources are not going to try and use the VITAL Transformer during the [curation process](documentation-system-overview-curating-linked-data). So for each data source needs to be modified as well:
            "transformer": {
                "curation": ["vital"],
                "metadata": ["jsonVelocity"]
            },

You need to remove 'vital', like so:
            "transformer": {
                "curation": [],
                "metadata": ["jsonVelocity"]
            },

Although from a practical stand-point you do need to curate somehow, so you'd more likely be replacing it with something like [Handle](documentation-system-administration-administering-mint-handle-server):
            "transformer": {
                "curation": ["handle"],
                "metadata": ["jsonVelocity"]
            },
Completely converting ReDBox to use Handle doesn't have full documentation at this stage, although there are [some rough notes available](http://code.google.com/p/redbox-mint/wiki/DevServerUpgradev12) for when the process was tested on the development server from QCIF.
 
## []()Background
 
Following the initial implementation of the VITAL Subscriber (now Transformer of course) we were asked to find a way to have the VITAL handles appear inside ReDBox as identifiers in our datastreams... and subsequently, these are the datastreams that we send to VITAL and will ultimately be harvested to national systems. 
#### **[]()The chicken or the egg?
 
So the reasonably small problem this presents is that we want to have a handle generated by VITAL inside the datastreams we send to VITAL (that's our chicken). And VITAL handles are generated after the background indexer notices the objects we put into Fedora (our egg). 
To solve the problem we first need to create a 'fake' egg... ie. we need to put something into VITAL that triggers handle creation without sending our datastreams across, and more importantly, without exposing any new items to OAI harvesters. Then our 'fake' egg (the new handle) can be used to create the chicken (our completed datastreams) to lay a real egg (our datastreams in VITAL, exposed via OAI-PMH). Now having stretched that tenuous analogy well beyond usefulness I promise never to bring it up again. 
#### **[]()Wait Conditions
 
The solution requires a few things to occur at indeterminate times, but in sequence, so our overall system has to become a little more complicated. To facilitate this I've added the idea of 'wait conditions' into the VITAL Transformer. The way the system is configured at this point: 
 1. The ReDBox object first enters the Transformer once the dataset is in the 'Published' workflow stage.
 1. An object is created in Fedora in an inactive state ('I'). No datastreams are sent.
 1. The newly created object's PID is stored in ReDBox as part of the object.
 1. A message is sent to the indexer to notify that this object has undergone a change.
 1. Because the '`vitalHandle`' property is not set (our wait condition), the Subscriber finishes.
  
#### **[]()Indexing
 
The indexing rules for datasets have been modified to add fields to the Solr index if either the '`vitalPid`' or '`vitalHandle`' properties are set. This simply lets us query the whole system quickly to determine what state any given object (or group of objects) is in. 
 1. The indexer receives the notification and re-indexes the dataset. The VITAL PID will now be in the index document, but no VITAL handle.
  
 `# VITAL integration`
 `vitalPid = self.params["vitalPid"]`
 `if vitalPid is not None:`
 `    self.utils.add(self.index, "vitalPid", vitalPid)`
 `vitalHandle = self.params["vitalHandle"]`
 `if vitalHandle is not None:`
 `    self.utils.add(self.index, "vitalHandle", vitalHandle)`
  
#### **[]()House Keeping
 
The Fascinator's housekeeper can have scheduled tasks execute with a [cron-like syntax](http://www.quartz-scheduler.org/docs/tutorials/crontrigger.html). We have a new script set up in the system now to execute every 15 minutes. 
 `"houseKeeping": {`
 `    "config": {`
 `        ...`
 `        "jobs": [`
 `            {`
 `                "name": "vital-handles",`
 `                "type": "external",`
 `                "url": "${server.url.base}default/hkjobs/vital.script",`
 `                "timing": "0 0/15 * * * ?"`
 `            }`
 `        ]`
 `    }`
 `},`
  
 1. The house keeping job will first do a Solr query for any datasets that have a VITAL PID set, but no VITAL handle.
 1. For each document found, a request will be sent to Fedora to retrieve the DC datastream.
 1. If the DC datastream doesn't have a 'dc:identifier' field containing a handle, the object is ignored for this execution of the script. The VITAL background indexer has not yet found this record.
 1. Once the handle has been created by VITAL, ReDBox will store this handle in the object as the property '`vitalHandle`'.
 1. The object will be sent back to our Transformer (JsonVelocityTransformer) responsible for creating datastreams. The templates have been modified so that if the handle is known it will be used during the creation of the datastreams.
 1. A new message will be sent to the indexer and the handle will now be indexed along with the PID. This doesn't matter for VITAL integration, but will stop the House Keeping script from finding this dataset again.
 1. Finally we send a message back to the [Curation Manager](documentation-system-overview-curating-linked-data-curation-data-model), indicating that the it should take another look at our object.
  
#### **[]()Activate and Submit
 
So now the circle is complete. The Subscriber is executing over the dataset again, and the wait condition has now been met (the '`vitalHandle`' property exists). 
 1. The first time this ever occurs for a given dataset we will activate the object in VITAL.
 1. All of the relevant datastreams will be ingested into VITAL.