**These notes are out-of-date with the release of Version 1.1. Please see [current install notes](documentation-system-administration-administering-mint-geonamesdata) unless you are installing and older version of the application.** 
***

 
Integrating Geonames data into your Mint installation is reasonably simple, but can be a little time consuming. The Solr index is simply too large to distribute reasonably over the Internet at this stage, so at least the first time you do this, it will have to be built locally for your institution. It doesn't have to be built on the server however, you could just as easily build this on your local desktop machine and copy the index to the server over your local network. 
#### **[]()First time build
 
Follow the [developer installation](http://code.google.com/p/solr-geonames/wiki/DeveloperInstall) instructions for the Geonames project and keep the resulting '`solr/data`' folder. You will need [Maven](http://maven.apache.org/) installed for the build, and any Subversion client to get the source code. The actual index build process will probably take around an hour. 
#### **[]()Configuring Mint
 
To get Geonames living happily alongside Mint you need two things: 
 1.  
The index data you just built and/or copied to the server. What was '`solr/data`' when you built it needs to be copied to your home directory under: 
        home/geonames/solr/data 
so that Geonames can find it when it starts. 
 1.  
The Geonames WAR file needs to deployed to Jetty on this path: 
 `server/webapps/``geonames.war` 
You can find the WAR file bundled in [this ZIP](http://code.google.com/p/solr-geonames/downloads/detail?name=solr-geonames.zip), just throw away the rest of the package. 
  
Now, the server already knows to start that WAR file as the system comes online, and the [control scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) ensure it knows the path to find its data on. 
#### **[]()Disabling Geonames
 
If this whole thing is of no use to you, it's very simple to remove. Simply remove the Jetty context configuration for Geonames from your deployed server: 
        server/jetty/contexts/geonames.xml 
Just delete that whole file, or at least move it out of the directory. Without that file, Jetty will not try to start the WAR file. 
Of course, the ReDBox server may still be expecting Geonames to exist if you have geo-spatial form fields in your workflow. You will need to remove those too.