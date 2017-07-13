When you look at the base of your Mint and ReDBox installations you will see the following directories:
 
* home
 
 * portal
 
 * server 
 
 * solr
 
 * storage
 
 The sections below provide an overview of the contents of each directory.
 
### []()home
 
The `home `directory contains a range of files that will be very handy for anyone configuring and supporting the system. Importantly, `system-config.json `contains the main configuration for the ReDBox and Mint systems. The details of this file are covered in [Configuration Files](documentation-system-administration-general-administration-configuration-files). 
For trouble-shooting, the `logs `folder contains a range of logs to assist you in tracking system state: 
* `stdout.out` is created at system startup and logs startup progress
 * `main.log` is the primary log for the system
 * `houseKeeping.log` tracks the system's internal house-keeping sub-system
 
 * `harvest.out` is created when you start a harvest
 * `BrokerMonitor.log` tracks the message queues used by the system
 
The `logs/jetty` and `logs/solr` directories contain the logs for the Jetty server and Apache Solr systems respectively. 
The `data` directory contains data files that can be uploaded (ingested) by the system. For Mint, this is a set of source files for known vocabularies or entities.
The `database-service` directory contains [Apache Derby](http://db.apache.org/derby/) database files used internally by the system.
The `harvest `directory provides harvest configuration files and their associated rules files. For details about harvests, refer to [Loading data](documentation-system-administration-loading-data)
The `templates `directory provides a set of metadata templates that are used to transform data within the system to various metadata formats such as [Dublin Core](http://dublincore.org/) and [RIF-CS](http://www.ands.org.au/resource/rif-cs.html). For details of how to edit these files, please see [Metadata Templates](documentation-system-administration-general-administration-metadata-templates).

A variety of other directories may exist under `home`, depending on what plug-ins you have configured. 
### []()portal
 The portal directory contains the files that define the system's look and feel. The [Branding](documentation-system-administration-general-administration-branding) section describes how to edit the portal files to change your system's appearance.
 
### []()server
 The scripts for starting and stopping the system as well as ingesting new data are located in the server directory. Two sets of files are provided to meet the needs of Windows and Unix/Linux users:
 
* Windows scripts:
 
 * `tf_env.bat`
  * `tf_start.bat`
  * `tf_harvest.bat`
 
 * Unix/Linux scripts:
 
 * `tf_env.sh`
 
  * `tf.sh`
 * `tf_harvest.sh`

The` tf_env.*, tf_start.bat `and `tf.sh` scripts are described in [Control Scripts](documentation-system-administration-general-administration-configuration-files-control-scripts). The tf_harvest scripts are described in [Loading data](documentation-system-administration-loading-data). 
There's also a set of sub-directories in the server directory. In general, most people won't need to change these at all unless they're doing development work. 
* jetty: this contains the[ Jetty ](http://jetty.codehaus.org/jetty/)server that runs the ReDBox and Mint systems
 * lib: is the collection of Java libraries used by the system
 * plugins: are Java libraries used by the system
 * webapps: are the web application archives
 
 
### []()solr
 Both ReDBox and Mint utilises the [Apache Solr](http://lucene.apache.org/solr/) search platform. This directory contains the Solr configuration and various Solr indexes. Unless you are looking to actively develop the Mint or ReDBox systems you are unlikely to change the contents of the solr directory.
 
### []()storage
If you are using the file-system storage plugin, the storage directory is the system's internal storage (you could call it a database). You are able to browse the various sub-directories but you shouldn't modify or delete any of the files in these directories.