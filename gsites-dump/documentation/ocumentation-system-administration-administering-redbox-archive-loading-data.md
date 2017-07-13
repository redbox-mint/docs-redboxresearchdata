This document has been SUPERSEDED by [Loading data into ReDBox via Alerts](documentation-system-administration-administering-redbox-configuring-alerts) but may be useful to those using the "old" alerts system. The new alerts system will be available in Version 1.6.

Since the early days of ReDBox it was possible to automatically ingest data in CSV files via an alert system. With the release of v1.3 this alert system has been expanded to include XML, and refined to be more precise and configurable.

#### **[]()Basic Configuration
The starting point is the [system configuration](documentation-system-administration-general-administration-configuration-files) file, which defines a housekeeping task to execute on a regular basis. In the default configuration shown below this is every fifteen minutes starting on the hour:

        "houseKeeping": {
            "config": {
                ...
                "jobs": [
                    ...
                    {
                        "name": "alerts-poll",
                        "type": "external",
                        "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/alerts.py",
                        "timing": "0 0/15 * * * ?"
                    }
                ]
            }
        },
The URL indicated above is where the real work occurs, and it exists along with other scripts in the web portal, allowing complete access to the application stack. It will also consults its own documentation as well:

        "alerts": {
            "path": "${fascinator.home}/alerts",
            "xmlMaps": {
                "xml": "${fascinator.home}/alerts/config/basicXmlMap.json",
                "rif": "${fascinator.home}/alerts/config/rifXmlMap.json"
            }
        },
First, it is looking at which file path to monitor for incoming content. Once a file appears on this path the primary deciding factor is going to be the file extension. At the moment we are only looking for (or caring about) CSV files (which route to legacy logic) or XML files, which are discussed in more detail below. You can see from the configuration above though that we have already earmarked this for RIF-CS ingest simply by depositing you XML files with a '.rif' extension for clarity.

#### **[]()XML Maps
In the config found above the important information here is the 'xml' key pointing at a JSON file on disk. The makeup of this file decides how the XML being ingested is going to map into ReDBox data fields. Here is a sample (the file is quite long):

        "//my:Coverage/my:TimePeriod": "dc:coverage.redbox:timePeriod",
        "//my:Coverage/my:GeospatialLocations/my:GeospatialLocation": {
            "my:GeospatialLocationType": "dc:coverage.vivo:GeographicLocation.0.dc:type",
            "my:GeospatialLocationValue": "dc:coverage.vivo:GeographicLocation.0.gn:name",
            "": "dc:coverage.vivo:GeographicLocation.0.geo:long",
            "": "dc:coverage.vivo:GeographicLocation.0.geo:lat",
            "": "dc:coverage.vivo:GeographicLocation.0.dc:identifier"
        },
And before discussing this, I'll also drop in an sample of the relevant XML nodes this config is trying to ingest:

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

#### **[]()Identifiers and Duplication
This is mainly a note about something to be aware of. Object identifiers inside the system are going to be defined by the name of the file (in the case of CSV, in combination with the row number). Since the alert system won't allow you to overwrite curated data in the system you will need to use unique names each time, presumably something like '{identifier}.xml'.