There are two areas of interest in loading Activity data into Mint: 
 * Loading public ARCS ([NCGP](http://arc.gov.au/ncgp/default.htm)) and [NHMRC](http://www.nhmrc.gov.au/grants) Activities (default).
 * Integrating a local institutional source of Activity records/information (customisation).
  
 


[[_TOC_]]

 
  
## []()CSV Upload fields (proposed)
 
Sept 6, 2012: These fields are to be finalised before implementation is completed. The information in this section is not yet implemented 

  
The documentation below this section describes a CSV with the following fields: 

  
    **Mandatory** ** Field name** **Description** **Note** **Examples** ** Mint Field**    * ID  
  
  
      Submit_Year The year in which the activity proposal was submitted 
  
  
      Start_Year The year in which the activity started 
  
  
      Title The title of the activity 
  
  
      Description A description of the activity 
  
  
      Institution The host organisation for the activity 
  
  
      Investigators A semi-colon (;) separated list of investigators OR just the PI 
  
  
      Discipline A keyword denoting the discipline area for the activity 
  
  
     
  
It is proposed to alter the field set above to better capture local activities via a local activity harvest config (rather than an ARC/NHMRC harvest): 

  
     **Mandatory** ** Field name** **Description** **Note** **Examples** ** Mint Field**    * ID  
  
  
      Name The name of the activity           Type As per http://ands.org.au/guides/cpguide/cpgactivity.html#sourcing           Existence_Start The year in which the activity started 
  
  
      Existence_End             Description A description of the activity 
  
  
      Primary_Investigator The name of the PI 
  
  
      Website A website for the activity           ANZSRC_FOR_1 The 2, 4 or 6 digit Field of Research code as designated by the Australian Bureau of Statistics under ANZSRC (2008)           ANZSRC_FOR_2             ANZSRC_FOR_3             
## []()Loading Public Activity Data
 
A default installation ships with sample activity data, but in order to go to production you are going to want to update this to include Activity records more relevant to your institution. The sample data comes from the [NCGP spreadsheets](http://arc.gov.au/general/searchable_data.htm) and [NHRMC spreadsheets](http://www.nhmrc.gov.au/grants/research-funding-statistics-and-data/funding-datasets), which are then manually massaged in Excel to prepare the datasets for machine ingest. 
The complete, ready to ingest CSVs (around 37,000 Activity records for all institutions) are available in their [own repository](https://github.com/redbox-mint/data). These files would normally be filtered to your local institution to create a production dataset. It is possible to ingest the full dataset if desired, but it is going to make your system a lot larger than typical, having an impact on backup/restore times with a larger storage layer in terms of items stored. The disk space impact is minimal. The sample data shipped by default is filtered to University of Newcastle (~900 Activities). 
All NCGP and NHMRC Activities are already available in Research Data Australia. ANDS pre-loads these Activity records, so Mint will never need to curate them or expose them via the OAI-PMH harvester. 
## []()Integrating Local Activity Data
 
If you would like to generate RIF-CS Activity records for other project/grants at your institution it is fairly simple to bring a new data source online. Flinders University first tried this on early versions of Mint (v1.2/v1.3) and the current documentation below is a distillation of their efforts drawn from the mailing list: [Thread 1](https://groups.google.com/d/topic/redbox-repo/NpwMwtfy9_Q/discussion), [Thread 2](https://groups.google.com/d/topic/redbox-repo/GpxciYq-SbA/discussion). 
The details below are loosely in order of things you need to do, but there are also just some points of consideration. They have been extracted from the above threads to make life easier: 
#### **[]()1) Data Format
 
For the sake of simplicity we are going to mimic the data format of the existing public Activities. Customisations are very possible if you want to send additional data to RDA, but the following columns are recommended as a baseline, since they will let us reuse existing code with minimal effort: 
 `ID, Submit Year, Start Year, Title, Description, Institution, Investigators, Discipline`  
Again, for the sake of simplicity, drop your data in a CSV file alongside the existing Activity data in `/home/data`. 
#### **[]()2) Curation Choices
 
Because the public Activities are already present in RDA they never required curation, and as such sections of their configuration will need to be altered to accommodate. Before you start it is worth giving some thought to the broad intentions you have here, particularly if your institution has a non-trivial curation environment. As a rough guide, looking at how you are curating your Service records is probably I good fit, since both Parties and Collections have some potential complexities that are unique to just those types of RIF-CS records. 
#### **[]()3) Data Source Configuration
 
The starting point for adding a new data source is the harvest configuration file, found in `/home/harvest`. We are going to copy an existing configuration file ('`Activities_``NHMRC_2010.json`') to act as a starting point for our new data source. The example from the linked thread is a good name: '`Activities_Other_Projects.json`'. 
Now we are going to edit the config file to make some alterations related to our curation choice, along with other minor tweaks. In this case, we are using the 'Local' curation plugin, the very simple implementation found in the downloadable demonstration install. Altered and new values are highlighted in red: 
 `{`
 `    "harvester": {`
 `        "type": "csv",`
 `        "csv": {`
 `            "fileLocation": "${fascinator.home}/data/Activities_Other_Projects.csv",`
 `            "idColumn": "ID",`
 `            "recordIDPrefix": "redbox-mint.googlecode.com/activities/",`
 `            ...<snip>...`
 `        }`
 `    },`
 
 `    "transformer": {`
 `        "curation": ["local"],`
 `        "metadata": ["jsonVelocity"]`
 `    },`
 `    "curation": {`
 `        "neverPublish": false,`
 `        "alreadyCurated": false`
 `    },`
 
 `    "transformerOverrides": {`
 `        "local": {`
 `            "template": "${server.url.base}published/detail/[[OID]]"`
 `        },`
 `        "jsonVelocity": {`
 `            "templatesPath" : "${fascinator.home}/templates/activities",`
 `            "portalId": "Activities"`
 `        }`
 `    },`
 
 `    "indexer": {`
 `        "script": {`
 `            "type": "python",`
 `            "rules": "Activities.py"`
 `        },`
 `        "params": {`
 `            "repository.name": "Local Research Projects",`
 `            "repository.type": "Research Activities"`
 `        }`
 `    }`
 `}`  
There is one point worth noting in there as well that was a shortcut for us. Under '`transformerOverrides`' there was already a config section for '`jsonVelocity`' that wasn't being used. This was there because, once upon a time (ie. before RDA held copies of all the public Activity records), we generated RIF-CS and published them to RDA. This no longer occurs, but we can reuse the config (and the templates, but we'll get to those later) that is left over from that time. 
#### **[]()4) Indexing Logic
 
Another line in the configuration file we didn't change points at a Jython rules file in the same directory ('`indexer`' > '`script`' > '`rules`'). The file holds the logic that decides how the system is going to build a Solr index entry for each record from the data source. You could create a new rules file and point at that if you desire, but in our case it is fairly trivial to alter the existing file to account for both use cases, so we will just alter '`Activities.py`'. 
Since this was first done on the mailing list, a couple of changes in curation have removed one of the suggested alterations to this file, so all that remains is to add a few lines to the end of the '`__basicData()`' method related to publication. We pinched them from another file (Parties Groups). 
 `    def __basicData(self):`
 `        self.utils.add(self.index, "repository_name", self.params["repository.name"])`
 `        self.utils.add(self.index, "repository_type", self.params["repository.type"])`
 `        # Persistent Identifiers`
 `        pidProperty = self.config.getString(None, ["curation", "pidProperty"])`
 `        if pidProperty is None:`
 `            self.log.error("No configuration found for persistent IDs!")`
 `        else:`
 `            pid = self.params[pidProperty]`
 `            if pid is not None:`
 `                self.utils.add(self.index, "known_ids", pid)`
 `                self.utils.add(self.index, "pidProperty", pid)`
 `                self.utils.add(self.index, "oai_identifier", pid)`
 `        self.utils.add(self.index, "oai_set", "Activities")`
 `        # Publication`
 `        published = self.params["published"]`
 `        if published is not None:`
 `            self.utils.add(self.index, "published", "true")` 
#### **[]()5) RIF-CS Template
 
At this point you should be able run the harvest below and see the records in your system. There are just a couple of other configuration point that are relevant to your Activity records. The first is how RIF-CS entires are generated to be sent of RDA. Take a look in '`/home/templates/activities/rif.vm`', since this file is already pointed at in our configuration above. 
By default, you shouldn't really need to do anything to this file since it is already up-to-date. It can handle curation and finding the configured persistent identifier according to your system wide config. If however you want to enrich this template to send more detailed Activity records to RDA this is the place to do it. You may be interested in adding a block like this (which I stole from Parties Groups again) to add any related objects that the curation process is aware of: 
 `            ### Relations`
 `            #set($valueList = $item.getJsonSimpleList("relationships"))`
 `            #if($valueList)`
 `                #foreach($value in $valueList)`
 `                    #if ($value.getBoolean(false, "isCurated"))`
 `                        #set($key = $value.getString("empty", "curatedPid"))`
 `                        #set($rel = $value.getString("hasAssociationWith", "relationship"))`
 `                        #if ($key != "empty")`
 `            <relatedObject>`
 `                <key>$util.encodeXml($key)</key>`
 `                <relation type="$util.encodeXml($rel)"/>`
 `            </relatedObject>`
 `                        #end`
 `                    #end`
 `                #end`
 `            #end`  
If you have added any additional columns to your originally ingested data you are going to need to add them into this template as well to make the most use of them. 
#### **[]()6) Portal Visibility
 
This should already be taken care of, but if you are trying to modify your setup to do something different there are only two areas you want to be aware of: 
* The '`"repository.type": "Research Activities"`' line in you config file is being looking for by the Activities portal. This portal (or 'view' as it gets called sometimes in doco) defines what ReDBox will see when it does a lookup against Mint for Activity data. So unless you want to modify the portal's configuration as well, you are going to need to keep that value as is.
 * The Published Objects portal is exposes objects to the ANDS harvester, which is why we needed to add those three lines to the indexing rules file in order to set the "`published`" flag. The data to make that decision is provided by the Curation Manager, we were just joining the dots.
 
 
### []()Harvesting Your Activities
 
Assuming the values from the above example where used, your new data source should show up with all of the other data sources that the command line harvest tool can see. The examples below come from my Windows laptop, but they work the same under *nix, just substitute an appropriate path and '`tf_harvest.bat`' for '`./tf_harvest.sh`': 
        c:\code\tf2\mint-builds\dev-local\server>tf_harvest.bat
 `Usage: tf_harvest.bat jsonFile`
 `Where jsonFile is a JSON configuration file`
 `If jsonFile is not an absolute path, the file is assumed to be in:`
 `    c:/code/tf2/mint-builds/dev-local/home\harvest`
 `Available files:`
 `    Activities_NGCP_Completed`
 `    Activities_NGCP_New_Ongoing`
 `    Activities_NHMRC_2010`
 `    Activities_Other_Projects`
 `    ANZSRC_FOR`
 `    ANZSRC_SEO`
 `    Funding_Bodies`
 `    Languages`
 `    Parties_Groups`
 `    Parties_People`
 `    RDA_Parties`
 `    Services`
 `c:\code\tf2\mint-builds\dev-local\server>tf_harvest.bat Activities_Other_Project`
 `s`
 `-XX:MaxPermSize=256m -Xmx512m -Dsolr.solr.home="c:/code/tf2/mint-builds/dev-loca`
 `l/solr" -Dgeonames.solr.home="c:/code/tf2/mint-builds/dev-local/home/geonames/so`
 `lr" -Dhttp.proxyHost= -Dhttp.proxyPort= -Dhttp.nonProxyHosts="localhost" -Djetty`
 `.port=9001 -Djetty.logs=c:/code/tf2/mint-builds/dev-local/home\logs\jetty -Djett`
 `y.home=c:/code/tf2/mint-builds/dev-local/server/jetty -Dfascinator.home="c:/code`
 `/tf2/mint-builds/dev-local/home" -Dportal.home="c:/code/tf2/mint-builds/dev-loca`
 `l/portal" -Dstorage.home="c:/code/tf2/mint-builds/dev-local/storage" -Dserver.ur`
 `l.base="http://138.77.204.185:9001/mint/" -Damq.port=9201 -Damq.stomp.port=9202`
 ` -Dsmtp.host="localhost" -Dadmin.email="admin@localhost" -Dredbox.version="1.5"`
 
 `c:\code\tf2\mint-builds\dev-local\server>` 
If you want to follow the flow of the harvest process, the starting point would be the harvest client, which logs into a file called '`harvest.out`'. The harvest client will then send messages into the Transaction Manager ('`transactionManager.log`') where it will be indexed and the RIF-CS templates are rendered. The Transaction Manager also contains our Curation Manager plugin, so when you are publishing that is where you want to look as well. Finally, if you care about the momentary lag between system activity and records appearing in the Solr index you can monitor this in '`solrwrapper.log`'. You can see from the extract below, there was a 22 second window between our records indexing and being visible in the web portal: 
 `09:56:45,594 solrwrapper DEBUG  WrapperQueueConsumer === New buffer starting: 1338249405594`
 `09:57:07,897 solrwrapper DEBUG  WrapperQueueConsumer === Flushing old buffer: 22s`
 `09:57:07,897 solrwrapper DEBUG  WrapperQueueConsumer === Submitting buffer: 16 documents`
 `09:57:09,600 solrwrapper INFO   WrapperQueueConsumer Running forced commit!`