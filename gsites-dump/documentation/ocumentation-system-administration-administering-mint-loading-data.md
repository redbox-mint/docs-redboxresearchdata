[[_TOC_]]



## []()Introduction

In order to load data into Mint you'll need to undertake what we call a "harvest". To run a harvest you use the '`tf_harvest`' script in the '`server`' directory. The [control scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) page has some broad information about these scripts, but for the purposes of this page, we'll assume that you are on Unix/Linux running the '`tf_harvest.sh`' script: 
 `cd server`
 `./tf_harvest.sh`  
Using the script in this way (without a parameter) lists of the of the available data sources the system is configured for. At the time of writing, this is the two lists below, but whatever local data sources you want to add to the system will appear as well. 
## []()Static Data
 
These data sources are static, in that they are unchanging and the same for everyone. They are not assigned Handles, and do not have rendered templates. They are used in support of jQuery autocomplete features for ReDBox form controls. 
 * *Language*: This is the set of ISO 639-2 languages (485 records)
 * *ANZSRC_FOR*: The ANZSRC codes for Fields of Research (1417 records)
* *ANZSRC_SEO*: The ANZSRC codes for Socio-Economic Objectives (989 records)
  
## []()Sample Data
 
This data is sample data that was used during development. Some (public data) is appropriate for use at the University of Newcastle (UoN), others are just fabricate people and groups. Most of this data is configured to seek access to a Handle server per object, and to generate OAI_DC and RIF-CS templates. It will need replacing with your own institution's 'real' data, and there are [notes on adding additional data](documentation-system-administration-administering-mint-loading-data-adding-a-new-column) as well. 
 * *Parties_People*: 7 Fake people. Handles are created and RIF-CS 'party' templates are rendered.
 * *Parties_Groups*: 5 Fake University business units. Handles are created and RIF-CS 'party' templates are rendered.
 * *Funding Bodies*: A selected sample of 36 project funding bodies from UoN's data. No Handles or templates.
 * *Activities_**: 3 different data sources for project data (filtered for UoN). Handles are created and RIF-CS 'activity' templates are rendered. ~397 records

  
## []()Harvest Configuration
 
Each of these harvests are described in a harvest configuration, stored in the '`home/harvest`' folder. In this folder you'll find two types of files: 
 * `.json` - these are the configuration files for the various defined harvests.
 * `.py` - these are rules files for the harvests, they dictate how to index that data.
  
To try this out, run: 
 `./tf_harvest.sh Languages`  
This will harvest the ISO 639-2 language codes into the system. Give them a few minutes to process (you can monitor the logs) and they will become visible in the web interface.
## []()Business rules

A common question asked about harvesting is "what happens if the data file changes?". This is usually applicable to the harvesting of parties, services and activities as they are (generally) provided by an HR or research management system. In the design of Mint and the CSV harvester, it was assumed that a central system would produce a CSV of current information (regarding researchers, for example) and place that in a folder accessible by Mint. 


It's helpful if we take a look at the sample Parties_People.json harvest configuration as a guide:


*** Configuration****** Description*** "harvester": {This starts the harvester configuration     "type": "csv",Sets the harvester as a CSV harvest      "csv": {Starts the CSV harvester configuration         "fileLocation": "${fascinator.home}/data/People_Sample.csv",Sets the location of the data file         "idColumn": "ID",Indicates which column can be used as a unique identifier for the record. If this isn't provided, the row number is used and this is a very bad idea for ongoing data ingests.         "recordIDPrefix": "redbox-mint.googlecode.com/parties/people/",Sets a prefix for the identifier. In this case, a value in the idColumn of 735 would result in the identifier being redbox-mint.googlecode.com/parties/people/735         "headerRow": true,Indicates that the first row contains column names and not data         "delimiter": ",",This CSV is comma separated         "ignoredFields": [],Allows you to ignore fields that aren't of interest to your ingest         "includedFields" : ["ID", "Given_Name", "Other_Names", "Family_Name", "Pref_Name", "Honorific", "Email", "Job_Title", "GroupID_1", "GroupID_2", "GroupID_3", "ANZSRC_FOR_1", "ANZSRC_FOR_2", "ANZSRC_FOR_3", "URI", "NLA_Party_Identifier", "ResearcherID", "openID", "Personal_URI", "Personal_Homepage", "Staff_Profile_Homepage", "Description"]Sets the fields that need to be ingested

Notes: 

* An associated rules file Parties_People.py is used to handle the mapping of the fields to the Mint data model. If you add new fields you'll need to modify the rules file
* The metadata stored by Mint from a CSV harvest can be reviewed by going to "Attachments" and clicking on "metadata.json" (see image below for screenshot). The metadata ingested from the harvest is located in the "data" section.
![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1348180458106/documentation/system-administration/administering-mint/loading-data/Screen%20Shot%202012-09-21%20at%208.33.55%20AM.png)



The following sub-sections describe how CSV-based harvests respond to changes in configuration and the contents of the CSV file.
### []()CSV Filename

Once the harvest has been configured, it's important to note that the CSV's filename is critical - it is used as a datasource name, much like the table name in a database.


 1. Mint uses the CSV's filename and the record's value in idColumn and recordIDPrefix to generate a unique identifier

  1. For example, the Mint Storage ID for [Alberto Zweinstein](http://demo.redboxresearchdata.com.au/mint/Parties_People/detail/d082b0890570265c99b52f360a674112/)is d082b0890570265c99b52f360a674112
 1. If you change the filename after ingesting data, the subsequently harvested records will be treated as NEW records

  1. This means that you will get 2 records for one person and you definitely don't want this.

### []()Adding new records to the CSV

If a new record is needed (e.g. if a new researcher joins the Uni), it's really a case of adding a row to the CSV. It's presumed that, in most cases, this will be provided in the ongoing update of the CSV from the central system.

1. In this case, provided the value in the idColumn is unique, a new record will be added to Mint. 
1. If the idColumn contains a non-unique value, an existing record will be altered with the new researcher's details - you probably didn't want this to happen

### []()Updating a record

Updating a record is easy - just alter the value in the CSV and reharvest the CSV. Mint will actually update every field in the CSV - this makes for rapid harvests (as no checking is done) but does mean that fields are "updated" even if they haven't changed.
### []()Deleting a record

You ***cannot*** delete a record in Mint via the CSV harvest. This rule is implemented for the following reasons:

* In some situations, the data feed from the central system is not reliable and records go "missing"

 * The HR system or research management system may not publish information about staff that have left
* Mint often creates additional metadata as part of the curation model (see [Curating Linked Data](documentation-system-overview-curating-linked-data)) and this would be lost if you delete the record.

 * For example, if the person has an NLA Identifier and links to a ReDBox record
* There's a hidden benefit - differential harvests, as described in "Planning around your CSV"

As Mint is in charge of publishing records to organisations (such as the NLA and ANDS), it isn't appropriate for these records to just disappear. Once they're in Mint they need to be treated with curatorial gloves. Whilst you can delete a record in Mint by logging in as Admin, this is strongly discouraged and you must make sure that you aren't breaking any relationships with other objects (in Mint or ReDBox) or feeds to other organisations.
### []()Planning around your CSV

The information in the previous sub-sections should help you in your planning - here are a few focus points:

1. Arrange for the central system to provide a CSV file with a pre-determined name on an on-going basis

 1. Ensure that the column names match those in the sample files or undertake some work to modify the associated rules.py file
 1. Have this uploaded to a directory accessible by Mint
 1. Never change the CSV's filename
1. Understand what happens when the CSV file changes:

 1. A new entry (one with an ID not currently in Mint) will create a new record (object) in Mint
 1. An existing record (one with an ID already in Mint) will cause that record's information to be updated
 1. A record that had been in a prior version of the CSV but is missing from the current version will not affect anything in Mint. No deletion will occur

With those business rules, it is possible to consider a differential harvest. This means that, instead of the CSV containing a list of every researcher being harvested each time, you could have the central system:

1. Produce a full listing for the first harvest
1. Only output a CSV of new or changed records in subsequent harvests

As long as the filename remains the same, this means that each subsequent harvest only needs to deal with new/updated records and this will mean faster processing. Most central systems utilise a database that has a method for tracking when a record was last modified.


Remember - your CSV harvest will not cause a Mint record to be deleted.

***


![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BPage+listing'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Subpage+Listing%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D297'dim'%5C%3D10'%3D10'%3D500'%3D297'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=lxEPbO98PQKCTIgDHMqrwU0hJD8)