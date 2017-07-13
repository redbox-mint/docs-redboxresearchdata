[[_TOC_]]


## []()Introduction

ReDBox provides an "alerts" system as a mechanism for other systems to provide records that can be imported into ReDBox. This is a file-based system in which other applications upload a CSV or XML file into a defined alerts directory (path). When a new file is found by the alerts system, it is processed as per the configuration and the various metadata used to create a new ReDBox record.


Version 1.6 of ReDBox sees the introduction of an extended alerts system. The [old alerts system](documentation-system-administration-administering-redbox-archive-loading-data) will still be available as per previous versions but users are recommended to transition to the new system. As the old alerts system may still be in operation, use of the new alerts system will need to be enabled using the procedure described below.


The alerts system differs to the harvest model seen in Mint, which harvests a data source (such as a CSV file), as described in [Loading data into Mint](documentation-system-administration-administering-mint-loading-data). 
### []()Older documentation


* [Import RIF-CS into ReDBox](documentation-how-to-archive-import-rif-cs-into-redbox) relied on a number of patches that are no longer needed under the new alerts system

* The ReDBox [Loading data](documentation-system-administration-administering-redbox-archive-loading-data) documentation describes the old version of the alerts system.


## []()Configuring alerts

Alerts are configured in system-config.json under the new-alerts key. Multiple alerts can be defined, allowing you to point to several directories, perhaps allocated per source system. The alerts system currently supports CSV (comma-separated) files and XML. 


A detailed documented sample configuration is provided at [https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib](https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib) 

### []()Alert files, identifiers and duplication

It is important to plan how alerts will be received into your defined alert path (directory) for handling. You're most likely needing to setup a new network directory for each specific application that will provide metadata. CSV-based alerts can provide multiple record imports in one file whereas XML-based alerts provide a single record for import. 


The filename is important to ReDBox as it is used in generating an internal identifier. This means that files added to the alert path must never reuse a filename. At first glance, this may seem unreasonable but it is important to remember that other systems are depositing files into the alert path and using unique filenames will ensure that an incoming alert doesn't over-write an older alert that is yet to be processed. Possible models for ensuring that this doesn't occur includes:

1. Using a timestamp in the filename: e.g. NewData_20120101120000.csv
1. Using a unique identifier (maybe from the source system): e.g. {identifier}.xml

It's also important to note that an alert will ONLY create a NEW record. If the other system updates a record and sends an alert file through with the same name, it won't be accepted either as a new record or as an update. Whilst, at first glance, it might be appealing to have updates come through from other systems, the record in ReDBox may have undergone reviews and edits and it is important to have this safe from an automated overwrite of the information.
### []()Using alerts to import RIF-CS files

As RIF-CS uses XML, a collection record can be imported via the alerts system. If you'd like to drag back records you've posted to RDA you can export the RIF-CS and break that export into individual collection descriptions. Please refer to [Breaking up a RIF-CS export](documentation-system-administration-administering-redbox-configuring-alerts-breaking-up-a-rif-cs-export) for assistance with this.

### []()XML Maps
Whilst CSV-based alerts can be defined within system-config, XML-based alerts are more involved and utilise a mapping file, provided by the xmlMap configuration item. Sample mapping files can be found in [https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib/test/config](https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib/test/config)


A very basic mapping file would look like:
```



        {
            `"comment"``:` `"This is a sample RIF-CS mapping. It is important to check this against your RIF-CS as there can be some differences across feeds etc"``,`
            `"mappings"``:` `{`
                `"//rif:registryObjects/rif:registryObject/rif:collection/rif:name[@type='primary']/rif:namePart"``:` `[``"title"``,` `"dc:title"``,` `"redbox:submissionProcess.dc:title"``],`
                `"//rif:registryObjects/rif:registryObject/rif:collection/rif:description[@type='full']"``:` `[``"description"``,` `"dc:description"``,` `"redbox:submissionProcess.dc:description"``],`
                `"//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='local']"``:` `"dc:subject.vivo:keyword.0.rdf:PlainLiteral"``,`
                `"//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='anzsrc-for']"``:` `"dc:subject.anzsrc:for.0.rdf:resource"``,`
                `"//rif:registryObjects/rif:registryObject/rif:collection/rif:subject[@type='anzsrc-seo']"``:` `"dc:subject.anzsrc:seo.0.rdf:resource"`
            `},`
            `"exceptions"``:` `{`
                `"output"``:` `"skos:note.0.dc:description"``,`
                `"fields"``:` `{`
                    `"dc:subject.anzsrc:for.0.rdf:resource"``:` `"Cannot resolve ANZSRC (FOR) Codes"``,`
                    `"dc:subject.anzsrc:seo.0.rdf:resource"``:` `"Cannot resolve ANZSRC (SEO) Codes"`
                `}`
            `},`
            `"defaultNamespace"``:` `{``"rif"``:` `"http://ands.org.au/standards/rif-cs/registryObjects"``},`
        }
```



#### **[]()Mappings

The makeup of the mapping file decides how the XML being ingested is going to map into ReDBox data fields. Here is a sample (the file is quite long):

        "//my:Coverage/my:TimePeriod": "dc:coverage.redbox:timePeriod",
        "//my:Coverage/my:GeospatialLocations/my:GeospatialLocation": {
            "my:GeospatialLocationType": "dc:coverage.vivo:GeographicLocation.0.dc:type",
            "my:GeospatialLocationValue": "dc:coverage.vivo:GeographicLocation.0.gn:name",
            "": "dc:coverage.vivo:GeographicLocation.0.geo:long",
            "": "dc:coverage.vivo:GeographicLocation.0.geo:lat",
            "": "dc:coverage.vivo:GeographicLocation.0.dc:identifier"
        },
The following provides a sample of the relevant XML nodes this config is trying to ingest:

        <my:Coverage>
            <my:TimePeriod>21st Century</my:TimePeriod>
            <my:GeospatialLocations>
                <my:GeospatialLocation>
                    <my:GeospatialLocationType>iso31661</my:GeospatialLocationType>
                    <my:GeospatialLocationValue>AU</my:GeospatialLocationValue>
                </my:GeospatialLocation>
                <my:GeospatialLocation>
                    <my:GeospatialLocationType>iso31661</my:GeospatialLocationType>
                    <my:GeospatialLocationValue>GB</my:GeospatialLocationValue>
                </my:GeospatialLocation>
                <my:GeospatialLocation>
                    <my:GeospatialLocationType>iso31661</my:GeospatialLocationType>
                    <my:GeospatialLocationValue>LU</my:GeospatialLocationValue>
                </my:GeospatialLocation>
                <my:GeospatialLocation>
                    <my:GeospatialLocationType>iso31661</my:GeospatialLocationType>
                    <my:GeospatialLocationValue>BW</my:GeospatialLocationValue>
                </my:GeospatialLocation>
            </my:GeospatialLocations>
        </my:Coverage>
The configuration generally comes in the form of key/value pairs, where the key will be an [XPath String](http://www.w3.org/TR/xpath/) identifying which part of the XML you are trying to map, and the key being the ReDBox field to ingest the data into. You'll note of course, that there is a slightly more complicated evolution of this where the XML contains repeatable elements. In this case the key is still XPath, but instead points at the repeatable node, and an the key contains a 'mini' config of key/value pairs mapping sub-elements into ReDBox fields.

At first glance it looks complicated, but it really a simple premise repeated over and over again. The hairiest part is probably going just be putting the XPath together.

#### **[]()Namespaces

Some XML documents utilise "default" namespaces without a prefix. The defaultNamespace configuration allows you to associate a prefix against the default namespace. You can then use this prefix in your XPaths.

        "defaultNamespace"`:` `{``"rif"``:` `"http://ands.org.au/standards/rif-cs/registryObjects"``},`


See also [http://www.edankert.com/defaultnamespaces.html](http://www.edankert.com/defaultnamespaces.html)
#### **[]()Exceptions
Occasionally you are going to run into areas where the data cannot be directly mapped into the field it needs to be in. One example here is the ANZSRC Codes, and you'll see this example in the config:

        "exceptions": {
            "output": "skos:note.0.dc:description",
            "fields": {
                "dc:subject.anzsrc:for.0.rdf:resource": "Cannot resolve ANZSRC (FOR) Codes",
                "dc:subject.anzsrc:seo.0.rdf:resource": "Cannot resolve ANZSRC (SEO) Codes"
            }
        }
Because in the XML we have just a basic ANZSRC code, we won't be able to line this up (trivially) with the full Mint URIs used in the form data:

            <my:FORCodes>
                <my:FORCode>
                    <my:FORCodeValue>1507</my:FORCodeValue>
                </my:FORCode>
                <my:FORCode>
                    <my:FORCodeValue>160104</my:FORCodeValue>
                </my:FORCode>
                <my:FORCode>
                    <my:FORCodeValue>170202</my:FORCodeValue>
                </my:FORCode>
            </my:FORCodes>
Given the potential possible complications involved with completely unknown data being ingested we don't necessarily want to 'fix' the issue right now, we just want to flag the data for attention by a data entry staff member. We do this with the 'exceptions' config you can see above. The intent is to follow the standard process of resolved the XPath and mapping to a field, but then checking if there is an exception for that field and instead redirecting the output to a new field. In the example above, we are redirecting to a notes field and adding a descriptor to the data so that data entry staff can address the problem.
## []()Testing an alert

A testing script has been provided to let you test an alert without loading data into ReDBox. For details, please refer to [https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/home/lib/jython/alertlib/test/README.md](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/home/lib/jython/alertlib/test/README.md)
## []()Scheduling alerts

By default, the alerts system is not setup as a housekeeping job so this will need to be configured if you want the system to pickup alerts automatically. The following code snippet provides an example configuration for system-config.json.




        "houseKeeping": {
 `"config": {`
 `...`
 `"jobs": [`
 `...`
 `{`
 `"name": "alerts-new",`
 `"type": "external",`
 `"url": "http://localhost:${jetty.port}/redbox/default/hkjobs/newalerts.script",`
 `"timing": "0 0/15 * * * ?"`
 `}`
 `]`
 `}`





        }



Notes:

* The timing value uses a [cron-like format](http://www.quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger).
* The url points to a script in portal/default/redbox/scripts/hkjobs/  - the .script extension just maps to .py

## []()Code

The alerts codebase is located in [https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib](https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/home/lib/jython/alertlib)