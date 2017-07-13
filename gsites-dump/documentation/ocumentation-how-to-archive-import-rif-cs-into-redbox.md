[[_TOC_]]


## []()Status

This document has been SUPERSEDED by [Loading data into ReDBox via Alerts](documentation-system-administration-administering-redbox-configuring-alerts)
## []()Introduction

In some cases you may have collection descriptions in RIF-CS format that you'd like to "drag back" into ReDBox. One reason that this may be a requirement for you is that your organisation manually added/created RIF-CS collections to Research Data Australia.


This How-to describes how to load the collection metadata into ReDBox. Importantly, this how-to focusses on a one-off process and assumes that you won't need to do this on an ongoing basis. If you need to have a permanent RIF-CS import, you can use this process or you may wish to consider the OAI-PMH harvester plugin. This document won't really help you with this last option.


There are a few considerations that you'll need to concern yourself with when planning an import:

* This how-to shows you how to import the record into the review stage - the record will need to be reviewed and, potentially, supplemented with additional metadata as ReDBox can manage a large set of information than RIF-CS;
* RIF-CS content can vary quite significantly so you will need to map your metadata
* Related Objects are tricky. You will have to analyse your ReDBox and Mint configuration closely if you want relationships set up properly. An alternative option is to re-link objects once the record is imported into ReDBox.

The system described here can be adapted to Mint for importing Service, Activity and Party records. However, be mindful that these records often have a source of truth (such as an HR system) and it may be better to add identifiers to those records rather than via an import. Naturally, the number of records that need importing will help you determine how much time you should spend trying to automate a process.


One final note, this How-to is all about IMPORTING and not UPDATING. If you want to have another system update an existing ReDBox record then you're up for a lot more work.
## []()Background reading


* [Loading data](documentation-system-administration-loading-data): discusses how to load data into ReDBox and Mint

 * Especially the ReDBox info for [Loading data](documentation-system-administration-administering-redbox-archive-loading-data)
* [Ingested Relationships](documentation-system-administration-administering-mint-ingested-relationships) - may be of use if you want to really get into linked data relationships, importing and curation


## []()Key files and folders


* home/alerts
* portal/default/redbox/scripts/hkjobs/alerts.py

## []()Procedure

Before you start this procedure, please make sure you have ReDBox running.
### []()1. Obtain the RIF-CS metadata

You'll need to get the metadata either via the ANDS ORCA system or from an OAI-PMH feed. The first option is easiest: (*thanks to Grant from Flinders for providing the details below*)



Assumptions:
* You have Data Source Admin rights to the ANDS Collection registry with AAF authorisation.
* ANDS Online Services (ie. ANDS Collections Registry) Release 8.1
* Firefox 3.6.23 web browser under Windows 7 Enterprise SP1.

Procedure:

1. Use your web browser to navigate to [https://researchdata.ands.org.au/registry/login.php](https://researchdata.ands.org.au/registry/login.php)
1. Login using AAF; Select your home institution then click Select; Type your username and password. You should now be logged into ANDS Online Services.
1. In the left pane, click "Export from Data Source".
1. In the first "Data Source Export Tool" form, complete appropriately for your institution (eg. see screen shot) then click "Export Data Source".
1. In the second "Data Source Export Tool" form, click "Download as XML"; save the file to your local workstation.
1. In the left pane, click "Logout".


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1346024467646/config/pagetemplates/how-to/orcaR8.1dsexport_c.png)
](config-pagetemplates-how-to-orcaR8-1dsexport_c-png?attredirects=0)

### []()2. Break up the RIF-CS file

The downloaded file will contain several collection records so you'll need to break it up into 1 collection per file. The python script below should do this for you (but you'll want to check it first). You'll also notice that the file will break out the different RIF-CS object types - this is handy if you have a mix in the XML file. Don't forget that you only want to import COLLECTIONS into ReDBox.


A utility script for splitting up the file is provided at https://github.com/redbox-mint-contrib/config-samples/blob/master/util/rifsplit.py: 

* run it using "python rifsplit.py <xml file>". 
* I will assume you have python 2.7 (ish) installed and have read the code.

Once you've run the script across your XML export you should have a series of files in the "output/" directory. Each filename is prefixed with the object type and you only need those with "collection_".

### []()3. Configure the alerts system

ReDBox contains an alerts system for handling incoming metadata that's not coming from something like the web forms or another harvest plugin. In a nutshell:

1. you place a file in the alerts folder - this is in home/alerts/

 1. There (should be) four subfolders under this:

  1. config/ : holds the configuration for the import
  1. failed/ : holds any files that failed to import plus an associated error file
  1. success/ : holds all files that were successfully imported
  1. processed: contains the JSON version of the imported metadata, as mapped by alerts.py and the config file
1. the housekeeping job runs the alerts.py script - this is found in portal/default/redbox/scripts/hkjobs

 1. alerts.py uses the file extension
1. alerts.py uses an XPath mapping file to map the incoming XML with the fields used by ReDBox

IMPORTANT NOTE: The filename used for the import file is IMPORTANT and must be UNIQUE. This means that if you (lazily) try to use "import.xml" for every file you import, it will work once and then just disappoint you on a repeating basis. Why? Well, the filename is used to create a identifier in ReDBox and you'l also be unable to keep a record of processed files if they all have the same name.


The alerts system is configured in the home/system-config.json file. The following two subsections are of interest here:




        "houseKeeping": {
                    "config": {
                        "quartzConfig": "${fascinator.home}/quartz.properties",
                        "desktop": true,
                        "frequency": "120",
                        "jobs": [
                            {
                                "name": "alerts-poll",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/alerts.script",
                                "timing": "1 * * * * ?"
                            }
                        ]
                    }
                },
        ...
        "alerts": {
                "path": "${fascinator.home}/alerts",
                "xmlMaps": {
                    "xml": "${fascinator.home}/alerts/config/basicXmlMap.json",
                    "rif": "${fascinator.home}/alerts/config/rifXmlMap.json"
                }
            },


The houseKeeping section configures the house keeping jobs - basically it wakes up and certain times and runs a set of tasks. In my config, I have the alerts-poll running every 1 minute - this is useful for development but very silly in production.


The alerts system config points to a directory in which to find incoming alerts (imports) and maps file extensions for XML files. Why do this? Well, XML files can use different schemas etc. Whilst we could write a script that digs into the file and handles the schemas, this is far more complex than just saying "If you give me a .rif I'll handle it one way and a .xml another". This also means that imports coming from different systems using different schemas, just need a different extension, something added to xmlMaps and handling code in alerts.py.


The config above points to rifXmlMap.json for a mapping file. A sample of this file is given below:

        {

            "comment": "This is a sample RIF-CS mapping. It is important to check this against your RIF-CS as there can be some differences across feeds etc",

            "mappings": {

                "//rif:registryObjects/rif:registryObject/rif:collection/rif:name[@type='primary']/rif:namePart": ["title", "dc:title", "redbox:submissionProcess.dc:title"],

                "//rif:registryObjects/rif:registryObject/rif:collection/rif:description[@type='full']": ["description", "dc:description", "redbox:submissionProcess.dc:description"],

                "//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='local']": "dc:subject.vivo:keyword.0.rdf:PlainLiteral",

                "//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='anzsrc-for']": "dc:subject.anzsrc:for.0.rdf:resource",

                "//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='anzsrc-seo']": "dc:subject.anzsrc:seo.0.rdf:resource"

            },

            "exceptions": {

                "output": "skos:note.0.dc:description",

                "fields": {

                    "dc:subject.anzsrc:for.0.rdf:resource": "Cannot resolve ANZSRC (FOR) Codes",

                    "dc:subject.anzsrc:seo.0.rdf:resource": "Cannot resolve ANZSRC (SEO) Codes"

                }

            },

            "defaultNamespace": {"rif": "http://ands.org.au/standards/rif-cs/registryObjects"},

        }
        
There are a few sections to this file:

* the mappings dictionary provides a set of XPath (keys) matched to the ReDBox properties (values)
* the exceptions section defines which fields, when matched, shouldn't actually be used as a property. Instead, it gets shunted to a notes field to help the reviewer. This is useful for issues around linked data in which your import may not be able to properly link to the real identifiers.
* the defaultNamespace is needed if your XML uses a default namespace (http://www.w3.org/TR/REC-xml-names/#defaulting) - the RIF-CS files we've seen often have this. In this element we allocate the prefix to the namespace.

Once you have configured the system and are ready to go, you need to add your collection files to home/alerts and wait for housekeeping to pick them up. Once the ingest has been run, you'll see results in the failed, processed and success subfolders as well as information in the log file.
### []()Notes


* If you take a look at the default/hkjobs/alerts.script (may have .py and not .script) file (and you should do this) you'll see that it can also handle .csv imports.
* Don't forget the log files, especially home/logs/main.log, as you can see very quickly if something is not working.
* There are a number of XPath tools you can use for checking your syntax - I use XPath Checker, a Firefox add on (http://code.google.com/p/xpathchecker/).

### []()Finishing up

Once your record has been ingested (and you've checked the log to make sure there were no errors) you should be able to login to your ReDBox system and see the newly imported record in the ALERTS section. If the record looks OK, you can check the green tick to accept the record into the normal workflow.


From here you can pass the work onto those who review metadata.