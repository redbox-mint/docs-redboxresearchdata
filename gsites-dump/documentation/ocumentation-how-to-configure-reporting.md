[[_TOC_]]


## []()Status

This document is in DRAFT.
## []()Introduction

Reporting is a feature that has been available since ReDBox 1.6. It provides the ability to create simple queries and export data as CSVs for use in your preferred [Business Intelligence Tool](http://en.wikipedia.org/wiki/Business_intelligence_tools) as well as a dashboard that provides charts to provide a snapshot of the system.
## []()Key files and folders


* home/system-config.json

## []()Procedure

1. Enable reports


First step is to Enable reports. What this does is enable the menu items for reporting in the admin menu (admin role required).

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1375681260791/documentation/how-to/configure-reporting/admin%20Menu.png)
](documentation-how-to-configure-reporting-admin%20Menu-png?attredirects=0)In system config simply add:

"redbox-reports": {
        "enabled": "true"
}

### []()2.  Set CSV output fields

Next we need to specify what CSV output fields we wish to have and their corresponding header labels. You can find a relatively comprehensive list of them in the [system-config.json in our local curation demo institutional build](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/src/main/config/home/system-config.json#L833).

Here's some sample configuration below ("field-name" is the solr index property name and "label" is what you would like your CSV header label to be called):


"redbox-reports": {
        "enabled": "true",
        "csv-output-fields": [
            {
                "field-name": "id",
                "label": "ReDBox ID"
            },
            {
                "field-name": "dc_title",
                "label": "Title"
            }
        ]

}

### []()3. Ensure services are configured for Statistical Reports

The next required step is to make sure you have the copied the [services section of the system-config.json](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/src/main/config/home/system-config.json#L632) in the local-curation demo institutional build. Without this, you will not be able to create/save/update reports or perform statistical reports.




### []()4. Configure charts

The final thing to ensure the reporting dashboard works is to add the chart configuration from the [charts section of the system-config.json](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/src/main/config/home/system-config.json#L776). It is possible to create your own custom charts (with some java coding and and understanding of JFreeChart) but it's out of scope for this document. Please post a request to the ReDBox Developers Google Group if this is something you'd like to have in your build.

### []()