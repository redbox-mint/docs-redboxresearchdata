**Interested parties** **Primary contact** **Release planned?**  **Action items**

*  



[[_TOC_]]



## []()Overview

Allow an existing ReDBox record to be cloned. This primarily assists in 2 use cases:

1. Easy creation of a similar record based on a different data set (e.g. "Sensor Readings 2010" and " Sensor Readings 2011") that requires only minor edits to the metadata
1. Easy creation of metadata to describe a new version of the underlying data. 

## []()Functional requirements


* Relationship with DOI and correct tracking of data versions:

 * A published dataset shouldn't be changed. 
 * Any changes to the data should be provided as a new version, with a new DOI

  * Data signatures/checksums could help track/alert changes made to the underlying data. See also [Reporting and statistics](feature-planning-reporting-and-statistics) feature
 * The data storage system should create a read-only copy of the data 

  * Possible link to [Data storage request integration](feature-planning-data-storage-request-integration) feature

## []()Design



## []()Resources


* 

*