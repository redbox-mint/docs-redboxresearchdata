Introduction
## []()

If you take a look at the Mint and ReDBox screenshots below you'll notice that Mint and ReDBox detail screens provide access to Datastreams (ReDBox) and Attachments (Mint) - they're the same thing, just using different words because of historical reasons.


[![](http://www.redboxresearchdata.com.au/_/rsrc/1320216908284/documentation/system-overview/ReDBox-DetailScreenanddatastreams.png?height=320&width=400)
 ![](http://www.redboxresearchdata.com.au/_/rsrc/1320216353658/documentation/system-overview/Mint-DetailScreenanddatastreams.png?height=348&width=400)
](http://www.redboxresearchdata.com.au/documentation/system-overview/Mint-DetailScreenanddatastreams.png?attredirects=0)
If we take a look at a person's record in Mint (such as Paul James') we will find the following items:

* `rif.xml`: provides a RIF-CS rendition of the metadata
* `metadata.json`: this is the underlying metadata for Paul's record
* `TF-OBJ-META`: is a system file used by ReDBox/Mint
* `oai_dc.xml`: provides a dublin core rendition of the metadata

These files are used by the ReDBox and Mint systems to provide OAI-PMH feeds of data. Each view can provide an OAI-PMH feed, allowing you to create specific subsets for harvesting. In the case of Mint's Parties-People record, this is available under http://<host>/mint/published/feed/oai. It's important to remember that, depending on your configuration, records in Mint and ReDBox may only be available via OAI-PMH if they've been published/curated. If you see no entries in your feed, try publishing a collection in ReDBox.
The discussion that follows will describe how these metadata records are created and how you can customise them for your local needs.

## []()[]()Transformers and templates

## []()




It's worth being acquainted with the way in which an entry in something such as a CSV file is ingested into Mint and moves through a process that eventually makes it available to OAI-PMH harvesters.

### []()[]()Templates

## []()




First, open up `mint/home/``system_config.json` and seek out the `oai-pmh` parameters:


                "oai-pmh": {
                    "sessionExpiry": "300000",
                    "metadataFormats": {
                        "oai_dc": {
                            "enabledInAllViews": false,
                            "enabledViews": ["published"],
                            "schema": "[http://www.openarchives.org/OAI/2.0/oai_dc.xsd](http://www.openarchives.org/OAI/2.0/oai_dc.xsd)",
                            "metadataNamespace": "[http://www.openarchives.org/OAI/2.0/oai_dc/](http://www.openarchives.org/OAI/2.0/oai_dc/)"
                        },
                        "eac_cpf": {
                            "enabledInAllViews": false,
                            "enabledViews": ["published"],
                            "schema": "urn:isbn:1-931666-33-4 [http://eac.staatsbibliothek-berlin.de/schema/cpf.xsd](http://eac.staatsbibliothek-berlin.de/schema/cpf.xsd)",
                            "metadataNamespace": "urn:isbn:1-931666-33-4"
                        },
                        "rif": {
                            "enabledInAllViews": false,
                            "enabledViews": ["published"],
                            "schema": "[http://ands.org.au/standards/rif-cs/registryObjects](http://ands.org.au/standards/rif-cs/registryObjects)[http://services.ands.org.au/home/orca/schemata/registryObjects.xsd](http://services.ands.org.au/home/orca/schemata/registryObjects.xsd)",
                            "metadataNamespace": "[http://ands.org.au/standards/rif-cs/registryObjects](http://ands.org.au/standards/rif-cs/registryObjects)"
                        }
                    }
                }
 
You can see that there are three metadata formats in use in the config above:

* `oai_dc`: Is the dublin core format required in any OAI-PMH feed service
* `rif`: Is the RIF-CS format for ANDS
* `eac_cpf`: Is a yet-to-be-fully-implemented-in-the-default-setup format that can link party records to the NLA's People Australia

 * As of Nov 2011 we're still working on this
You'll see that each format provides a link to the relevant schema and the relevant namespace. By default, none of the formats are enabled in all views - this means that we are explicitly configuring feeds for specific views. What's more, certain views can have different formats - this could be useful when you need to feed a specific type of metadata (e.g. EAC-CPF) to a specific organisation (e.g. the NLA). 
The `oai-pmh` section indicates how metadata will be shared but we'll step to the start of the process and see how the metadata gets there in the first place.
In another part of `system_config.json `you'll find the transformerDefaults section (below). This tells us that there is a transformer plug-in named `jsonVelocity `- this plugin allows for the creation of one or more renditions of the metadata via the use of Velocity templates. We can see that the `sourcePayload `being transformed is the `metadata.json` file (we mentioned that earlier). 
The `templatesPath `directive lets us know where the Velocity templates will be found. In the case of Mint we'll find it in mint/home/templates. It's worth looking through these files in your installation to see what's there. 


            "transformerDefaults": {
                "ingest-relations": {
                    "id": "ingest-relations"
                },
                "jsonVelocity": {
                    "id" : "jsonVelocity",
                    "sourcePayload" : "metadata.json",
                    "templatesPath" : "${fascinator.home}/templates",
                    "portalId": "default"
                }
            },

### []()[]()Harvesting and transformers

## []()




When we harvest information about people (say, researchers) into Mint, data goes through a transformation step - you're likely to have undertaken this in the [Loading data into Mint](http://www.redboxresearchdata.com.au/documentation/system-administration/administering-mint/loading-data) section. If we have a look at the transformer directives in the `mint/home/harvest/Parties_People.json` harvest configuration file (below) you can see that the Velocity templates in `mint/home/templates/people` will be used by the jsonVelocity transformer - go and have a look there and you should find two files: `oai_dc.vm` and `rif.vm`.

            "transformer": {
                "curation": [],
                "metadata": ["ingest-relations", "jsonVelocity"]
            },
            "transformerOverrides": {
                ...
                "jsonVelocity": {
                    "templatesPath" : "${fascinator.home}/templates/people",
                    "portalId": "Parties_People"
                }
            },



## []()Editing the templates 

## []()



A quick look at the default `mint/home/templates/people` templates will show you that you need to undertake a little customisation. Open up the templates in a text editor and edit at will. If you change the template and flick back to your browser, refresh a record and open the metadata file you'll see (drum-roll) that nothing has changed!
The `jsonVelocity `transformer runs at harvest time and the system does not watch for template changes. This means that any renditions you created before you edited the template will need to be updated. In order to do this you will need to:

1. Login as administrator
1. Switch to the View you wish to use - in the case of this activity it's the "published" view in Mint
1. On the Home page you should see an item in the ACTIONS section titled "Reharvest view" (see screenshot below) - click on this action
1. You'll get a warning message letting you know that large Views will take a while to reharvest. If you're happy with this, get the reharvest started


[![](http://www.redboxresearchdata.com.au/_/rsrc/1320733198820/documentation/system-administration/general-administration/metadata-templates/ReharvestView.png)
](http://www.redboxresearchdata.com.au/documentation/system-administration/general-administration/metadata-templates/ReharvestView.png?attredirects=0)After giving the system some time to reharvest, open up an object in the view and see if the metadata renditions have changed.

### []()[]()Object histories

## []()

If you stay logged in as an administrator and give the reharvest a few moments to run, you can then see this reharvest in the object's history:

1. Open one of the objects in the view you just reharvested
1. In that objects ACTIONS section you'll see the "Object History" item - click on that and you'll see what you've put the object through

 * You may also notice that the ACTIONS section for the object allows you to Reharvest just that specific object. 





## []()Further reading


* [OAI-PMH Harvesting](documentation-system-administration-general-administration-oai-pmh-harvesting)

* [OAI-PMH exports from The Fascinator](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/oaipmh)