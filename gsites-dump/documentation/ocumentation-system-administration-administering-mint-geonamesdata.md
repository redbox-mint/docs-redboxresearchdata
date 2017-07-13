Integrating Geonames data into your Mint installation is reasonably simple, but can be a little time consuming. The Solr index is simply too large to distribute reasonably over the Internet at this stage, so at least the first time you do this, it will have to be built locally for your institution. 
#### **[]()First time build
 
If you don't have a copy of a Geonames index sitting around to copy in place, you are going to have to build it. Start by downloading and unpacking the [Geonames data dump](http://download.geonames.org/export/dump/allCountries.zip) (around 200mb). On *nix you can do this from the command line: 
 `wget http://download.geonames.org/export/dump/allCountries.zip`
 `unzip ``allCountries.zip`  
This will leave you with about 900mb of data in a text file (allCountries.txt) to ingest. You'll need to run the harvest script in the 'server' folder to ingest this: 
 `./geo_harvest.sh allCountries.txt`  
**PLEASE NOTE:** At time of release (v1.1) *nix based systems don't having the correct logging configuration for this script. It will pour enormous amounts of data into 'main.log' (~1.5gb). This will be resolved ASAP, but in the interim it is recommended that you a) run this script whilst the server is offline, and b) backup and restore you 'main.log' before and after. The log entries themselves are not significant and do not need to be kept.
*During the v1.2 testing this problem has been improved somewhat. The excessive logging is still occurring, but it is confined to the correct log file and is trivial to remove afterwords. It is worth noting however some testing was performed on running this script with the server online. It is perfectly viable, but [for now](http://code.google.com/p/redbox-mint/issues/detail?id=48) will require a server reboot upon completion to find the Solr index changes.*
If you want to watch the log for only important rows whilst the harvest is ongoing, try something like this:
        tail -f geo_harvest.out | grep read
        2012-04-13 12:35:28,790 INFO   Harvester            12:35:28: Rows read: 60000
        2012-04-13 12:35:44,105 INFO   Harvester            12:35:44: Rows read: 80000 
#### **[]()System Re-Build
 
 
If you are just rebuilding a system, or bringing a second server online, you can always copy the data from your first server to save running the ingest script. You should copy this folder **when the server is offline**: 
 `home/geonames/solr/data`  
#### **[]()Disabling Geonames
 
If this whole thing is of no use to you, it's very simple to remove. Simply remove the Jetty context configuration for Geonames from your deployed server: 
 `server/jetty/contexts/geonames.xml`  
Just delete that whole file, or at least move it out of the directory. Without that file, Jetty will not try to start the WAR file. 
You'll also want to delete the WAR file itself: 
 `server/webapps/geonames.war`  
Of course, the ReDBox server may still be expecting Geonames to exist if you have geo-spatial form fields in your workflow. You will need to remove those too. An easier alternative is to simply not ingest any data. Geonames will happily run with an empty index, but it will consume some memory on your server... your choice.