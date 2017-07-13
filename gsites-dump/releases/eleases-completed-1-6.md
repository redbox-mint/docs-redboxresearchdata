[[_TOC_]]



## []()Key Dates

* RC1 to be released March 12
* Full release April 2

## []()RC1 Release

ReDBox 1.6 Release Candidate 1 (RC1) will be released via the normal deployment channels as well as [http://demo.redboxresearchdata.com.au/redbox](http://demo.redboxresearchdata.com.au/redbox) and [http://demo.redboxresearchdata.com.au/mint](http://demo.redboxresearchdata.com.au/mint)
## []()Release notes

### []()Important notice

The reporting functionality in 1.6 relies on the Event Log index - you'll find this in solr/indexes/eventlog/. Previously, you could migrate ReDBox/Mint by just moving the storage/ directory around. However, if you don't move the Event Log for ReDBox and Mint you will lose the publication tracking dates and reporting will no longer reflect published items correctly.


The reporting tools require configuration. For those using an institutional build, please refer to https://github.com/redbox-mint/redbox-build-dev-local/blob/redbox-local-curation-demo-1.6/src/main/config/home/system-config.json for configuration elements. It is suggested the you diff the two files and bring over the reporting config.


Documentation will be made available as soon as time permits.
### []()Review workflow


* The review workflow has not been significantly changed in 1.6 as we didn't want to interrupt any deployment planning for the ANDS Metadata Stores work.
* Three new items are available:

 * The Description tab now has a section for providing links to technical metadata.

  * At present this metadata does not appear in the RIF-CS template. Work is being undertaken to test a RIF-CS 1.4 template.
 * The Attachments section now provides an "Ethics" entry in the Type drop-down. 
 * The Submit tab allows you to enter Embargo information

  * A record with the "Embargo this record" checkbox selected cannot be progressed to the Published stage until the checkbox has been de-selected by a reviewer (manual release)

   * Deselecting the embargo will allow the record to progress. The embargo date and note will be kept.
  * A list of all records under embargo (and at the Final Review stage) is available in the Home screen, under the Alerts list

   * The embargo date and notes will be kept after selecting the embargo checkbox.
* See also the DOI Minting section

### []()Data management planning toolkit (DMPT) - BETA


* Key DMPT features:

 * Versioned PDF renditions of the plan are created at key points
 * Plans can be shared with other users (view only, not edit)
 * The user can create a Collection description using the metadata in a plan
 * New collections are created under a self-submission workflow

  * The links over to the Review workflow once submitted by the user.
  * NOTE: This work is being finalised and the RC1 version is incomplete.
* The DMPT makes use of the new workflow and form sub-system. This makes configuration of the forms much easier across areas such as the workflow steps, form fields (widgets) and label/help text.
* A new view named "Researcher Dashboard" is available as a researcher entry point. This is accessible via the Views menu.

 * The view displays the user's plans and any self-submitted datasets
 * A new user/role has been added for testing: researcher (password: researcher)
* Full documentation to follow. Key areas in the deployment include:

 * home/language-files
 * home/form-configuration

### []()Admin reports


* The Admin menu now features access to the Reports Dashboard and Reporting tools
* The Reports Dashboard provides 4 graphs with an overview of records in the system. 

 * The date range and report name can be changed
 * The underlying data can be exported as a comma-separated (CSV) file
 * A printable layout is also available
* The Reports tool provides a number of features:

 * Add, edit, duplicate and delete reports
 * "Record" reports provide a list of records within a date range and matching criteria.

  * A preview (subset) of results are displayed and can be downloaded as CSV
 * Statistical reports provide record counts from ReDBox and Mint across a date range

  * Results can be downloaded as a CSV
* The reporting tool has some minor issues in Internet Explorer 8. Please use a more modern version (IE 9) or Firefox/Chrome/Safari.

 * No work will be undertaken to support IE 8

### []()DOI Minting


* Collections in ReDBox can be allocated a DOI via the review forms (under the citation tab)
* Configuration for the DOI service can be found under the "andsDoi" section in system-config.json
* Your organisation will need to approach ANDS for access to their DOI service - please refer to [http://www.ands.org.au/guides/doi.html](http://www.ands.org.au/guides/doi.html) and [http://www.ands.org.au/services/cite-my-data.html](http://www.ands.org.au/services/cite-my-data.html) for further details

### []()CSV Harvester


* The CSV Harvester (mainly used in Mint) allows for multi-value fields and regex filtering