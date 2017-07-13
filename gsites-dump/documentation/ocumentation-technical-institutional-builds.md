[[_TOC_]]



## []()Introduction

Please also refer to [Create your own Institutional Build](documentation-how-to-institutional-builds) for a handy HOW-TO. A [Background](documentation-technical-institutional-builds-background) document is also available for those interested in why we're using this approach.


Creating your own institutional build is typically the first step involved in moving from a testing/demonstration install to something suitable for your institution to move to production with.


The documentation on the ReDBox site covers the types of curation available and the business drivers/implications inherent in them. This document is focused on the more practical issues of "How do I do this, once I've made a decision?"


There are two main sections, one for build Mint and another for ReDBox. For most institutions you would keep these fairly similar, but for the sake of demonstration we've made different choices for each here.


The sample commands used here are from my Windows laptop, but they are fairly obvious to convert to `*`nix if desired. We are also going to represent the fictitious 'University of Examples' from 'examples.edu.au'.


## []()1.0 Mint

Our Mint server will be a basic example; very much a copy of the 'local' curation demo, just tailored to our local needs, so we also cover some system fundamentals here. Because Mint houses our Party records we also chose to configure integration with the National Library of Australia.
### []()1.1 Maven Project

Let's start by exporting the 'dev-local' institutional build to use as a baseline. If I was keeping the build in the public subversion repo I could just do a copy, but I suspect most institutions will choose to host their build in private so that they can commit sensitive details as part of the configuration (reduces post-install steps). In that light I'll export from the public repo onto a path that I assume is setup in your institutional source control.




        cd \code\tf2\mint-builds
        mkdir new-local
        cd new-local
        svn export https://redbox-mint.googlecode.com/svn/mint/builds/dev-local/trunk svn


You'll notice that only a few files have been exported in total. Institutional builds vary in size, but they are typically quite small:




        A    svn
        A    svn\deploy.server
        A    svn\setup.groovy
        A    svn\src
        A    svn\src\main
        A    svn\src\main\config
        A    svn\src\main\config\solr
        A    svn\src\main\config\portal
        A    svn\src\main\config\server
        A    svn\src\main\config\server\tf_env.bat
        A    svn\src\main\config\server\tf_env.sh
        A    svn\src\main\config\home
        A    svn\src\main\config\home\system-config.json
        A    svn\src\main\config\home\harvest
        A    svn\src\main\config\home\harvest\Parties_People.json
        A    svn\src\main\config\home\harvest\Services.json
        A    svn\src\main\config\home\harvest\Parties_Groups.json
        A    svn\package.xml
        A    svn\pom.xml
        Exported revision 1223.


Typically I'd import this as a project in my IDE of choice. Netbeans handle Maven projects just fine out-of-the-box, and Eclipse does as well with a plugin, but given the simplicity of the files listed above you could also edit them directly using a standard text editor just fine.


First stop is to edit `svn\pom.xml`, and alter some of the values to give it a new name and home:


            <groupId>au.edu.examples.redbox-mint</groupId>
            <artifactId>mint-install</artifactId>
            <version>0.1-SNAPSHOT</version>
        ...
            <name>University of Examples - Mint Installer</name>
        ...
            <properties>
        ...
                <server.url.base>http://research.examples.edu.au/mint/</server.url.base>
                <smtp.host>smtp.examples.edu.au</smtp.host>
                <admin.email>admin@research.examples.edu.au</admin.email>
        ...
            <profiles>
                <profile>
                    <id>build-package</id>
                    <properties>
                        <server.url.base>http://research.examples.edu.au/mint/</server.url.base>
                    </properties>
        ...




**An important note about the above config**: Notice that we didn't touch the server's port number (9001), but we removed the port number from the server URL. We do this because we are going to run Apache in from of Jetty on our server, [http://code.google.com/p/redbox-mint/wiki/RawServerBuildNotes#Install_Apache in a fairly common setup]. If you don't want that, you would leave the port number in like so:



        <server.url.base>http://research.examples.edu.au:${server.port}/mint/</server.url.base>


If you wanted you could use the port number in the first '`server.url.base`' property (gets used when you are building/running the project as a developer), but remove it from the one listed under the '`build-package`' profile (gets used when building tarballs for distribution).


Right at the end you'll also find a section related to your development infrastructure. You can actually delete them altogether if you like since they are primarily available in the core for distributing Maven artifacts.


I'm going pretend the eResearch team at the University of Examples has their own subversion repo they will integrate with:


            <scm>
                <url>https://research.examples.edu.au/svn/mint/build/trunk</url>
                <connection>scm:svn:http://research.examples.edu.au/svn/mint/build/trunk</connection>
                <developerConnection>scm:svn:https://research.examples.edu.au/svn/mint/build/trunk</developerConnection>
            </scm>


At this point it is worth building your project to ensure the POM still validates. It will deploy a server as well, but we haven't configured it yet.


        cd \code\tf2\mint-builds\new-local\svn
        mvn clean install


You can also package up your build in a tarball. But don't do this in the directory structure of a server you have already started, since it just ZIPs up the file structure of the deployed server around it. Typically I do this against a clean checkout on a new path like 'mint-installer' to avoid confusion/mistakes :



        mvn -P build-package clean install


The resulting tarball is under `/svn/target`:

        /code/tf2/mint-builds/new-local/svn/target/mint-install-0.1-SNAPSHOT-build.tar.gz


### []()1.2 Curation Configuration

Generically, there are three parts to configuring curation, and then anything that is specific to that curation plugin as well:

1.   Make sure you have the plugin.
1.   Configure the plugin at system startup.
1.   Configure the data source to use the plugin for curation.



Because we copied the 'dev-local' build and it is a very simple curation method we don't really need to do much, but let's touch on what is involved in setting them up, and you can see that they are present in your build.
#### **[]()1.2.1 Plugin Dependency

To get a hold of the plugin it just needs to be listed as a dependency in your POM. You can this section:


                <!-- Mint Curation Plugin -->
                <dependency>
                    <groupId>com.googlecode.redbox-mint</groupId>
                    <artifactId>plugin-transformer-local</artifactId>
                    <version>${mint.version}</version>
                </dependency>


Combined with the scripts that Maven runs during the build you should then find this JAR file deployed somewhere like this:

        C:\code\tf2\mint-builds\new-local\server\lib\plugin-transformer-local-1.5.jar


Please refer to the [Plugin Directory](documentation-system-administration-plugin-directory) for a list of available plugins.
#### **[]()1.2.2 System Configuration

You '`system-config.json`' file is found in the build at `/svn/src/main/config/home/system-config.json`. After building the Maven project it will be deployed to `/home/system-config.json`, so keep that in mind when you are editing it. Only files under `/svn/src/main/config/` are under source control, so never forget to update these, but the deployed files are the ones actually being used by your server, so live edits can save time if using these... just don't forget to migrate them back.


The first minor point is common to both ReDBox and Mint, as well as all curation methods. It is a common point where the persistent identifier for records should be stored. In this case we have request that Object should have a property called '`localPid`':


            "curation": {
                ...
                "pidProperty": "localPid",


The 'real' configuration for our plugin is found under '`transformerDefaults`', along with any other transformers turned on in this system (they don't all relate to curation). In this instance we are looking for the '`local`' value in the '`id`' field. We've also re-use '`local`' as the label, but this could be anything. The label gets used in the data source configuration later to point at this Transformer, so if you change it here, you need to change it there as well. The '`id`' value relates to the ID of the plugin as it identifies itself via the Plugin API, so you can't really change that.




            "transformerDefaults": {
                ...
                "local": {
                    "id": "local",
                    "useIncrements": false
                },


With this setup you could start the system already and see something like this in 'main.log':




        ...     INFO   MessagingServices    Starting message queue services...
        ...     INFO   MessagingServices    Creating new AMQ Producer for broker: 'tcp://localhost:9201'
        ...     INFO   MessagingServices    Opening new AMQ Session for broker: 'tcp://localhost:9201'
        ...     INFO   BrokerMonitor        Starting callback timer. Timeout = 10s
        ...     DEBUG  DerbyAccessControl   Derby security database online!
        ...     INFO   ManagerQueueConsumer Transformer warmed: 'ingest-relations'
        ...     INFO   ManagerQueueConsumer Transformer warmed: 'local'
        ...     INFO   ManagerQueueConsumer Transformer warmed: 'jsonVelocity'
        ...     WARN   DerbyAccessControl   Using previously specified data directory: 'c:\code\tf2\mint-builds\dev-local\home/database-service', provided value has been ignored: 'c:\code\tf2\mint-builds\dev-local\home/database-service'
        ...     DEBUG  DerbyAccessControl   Derby security database online!
        ...     INFO   ManagerQueueConsumer Starting transactionManager...
        ...     INFO   scriberQueueConsumer Starting subscriber...
        ...     INFO   MessageBroker        All Message Queues started successfully
        ...     INFO   NotificationConsumer Starting emailnotification...


This is the line we are looking for:

        Transformer warmed: 'local'


Common things that can go wrong here:

*   If you missed the dependency and the JAR is missing from your classpath it will complain about '`local`' being invalid or missing.
*   If you forget the config altogether it won't even try to start, so you won't see anything.
*   Or if your configuration is invalid, or for most other problems that cause startup errors, you would see an error here instead. You'll have to look at the error to try and work out what is going on.



Finally, underneath the '`curation`' section once again you have some configuration required for integrating with the NLA (assuming you leave '`enabled`' as true). The ISIL used in '`agencyCode`' here is fake of course, and these values are only ever used in building EAC-CPF to send to NLA.


            "curation": {
                ...
                "nlaIntegration": {
                    "enabled": true,
                    ...
                    "agencyCode": "AU-ANL:UEX",
                    "agencyName": "The University of Examples, Australia",
                    ...
                }
            },


#### **[]()1.2.3 Data Source Configuration

Under `/home/harvest` in any installation you'll find all of your harvest configuration files; the '`.json`' files, one per data source. The institutional build only needs to carry a copy of any config file it wants to overwrite... typically data sources that require curation:

*   Parties_Groups.json
*   Parties_People.json
*   Services.json

Plus of course any local data sources. In each of these config files we are primarily concerned about two sections. The first simply directs the Curation Manager to use our plugin when the time for curation comes around:


            "transformer": {
                "curation": ["local"],
                ...
            },


And the second is used to provide any overriding configuration Transformers should use when processing records from this data source. Not all Transformers require per-data source config, but in this case we just want to provide a template for persistent ID templates:


            "transformerOverrides": {
                "local": {
                    "template": "${server.url.base}published/detail/[[OID]]"
                },
                ...
            },


### []()1.3 Institutional Configuration

The first stop here is at the top of your '`system-config.json`' again:


            "redbox.identity": {
                "institution": "University of Examples",
                "RIF-CS Group": "The University of Examples, Australia"
            },


These values are used in a few locations throughout the system (such as building RIF-CS XML) where it needs to know the name of your institution. You may notice throughout the rest of the configuration file that many of the Strings required for more practical purposes are instead filled with placeholders like these:




        "urlBase": "${server.url.base}"
        "email": "${admin.email}"


These values will be substituted at runtime for System properties of the same name. So we need to go have a look at our startup scripts to see where those properties are set. The script of particular interest is '`tf_env.bat`' or '`tf_env.sh`' depending on your platform, but it is found under `/server` (the deployed copy) and `/svn/src/main/config/server` (under version control). The version controlled copy is actually slightly different, but we'll get to that...


So to step backwards from what we already know we would start at the bottom of the file where all the parameters for Java are being put together. Things like this:

        -Dsmtp.host="%SMTP_HOST%" -Dadmin.email="%ADMIN_EMAIL%"


At this point we are looking at variables from the batch file or shell script, so we need to scroll and look for where they are set elsewhere in the script. And here is where you will notice a difference. The deployed files look something like this:


        set SMTP_HOST=smtp.examples.edu.au
        set ADMIN_EMAIL=admin@research.examples.edu.au


... so it already knows our details. That's because it got them from the Strings we put into our Maven POM earlier. If you look at the scripts under version control you'll see the different we mentioned:


        set SMTP_HOST=${smtp.host}
        set ADMIN_EMAIL=${admin.email}


These placeholders were replaced (Maven calls it filtering) during the build process by Maven. If you are looking to do something similar in other files, check this part of your POM for how it is done:


        <!-- Control Scripts -->
        <execution>
            <id>copy-scripts</id>
            <phase>process-resources</phase>
            <goals>
                <goal>copy-resources</goal>
            </goals>
            <configuration>
                <outputDirectory>${dir.server}</outputDirectory>
                <includeEmptyDirs>true</includeEmptyDirs>
                <overwrite>true</overwrite>
                <resources>
                    <!-- Resolve some paths in our control scripts -->
                    <resource>
                        <directory>src/main/config/server</directory>
                        <filtering>true</filtering>
                        <includes>
                            <include>**/*.bat</include>
                            <include>**/*.sh</include>
                        </includes>
                    </resource>
                </resources>
            </configuration>
        </execution>


In particular: `<filtering>true</filtering>`.


This process is relevant to institutional builds if you plan on using them only for developers, since the developers will keep values in their POM, but they become set in stone once the tarballs have been built and distributed to servers. There you will need to do your edits at the top of your 'tf_env.sh' file instead.


For the adventurous you could deploy Maven on the servers as well if desired and investigate using build profile triggers to set these properties for each server in your POM (ie. if on the prod server, then this is my URL, email address etc).


### []()1.4 Minty Fresh

And that's it. We didn't really change much, as was mentioned at the beginning, most of this was exploring how the build we copied already works.


Your server should be good to go now. You want to harvest some data as per normal to test out your configuration, and once ReDBox is online we need to test curation as well.


## []()2.0 ReDBox

The ReDBox examples has been deliberately chosen as something more complicated. It is a setup that may appeal to anyone involved in the ARROW project, using VITAL as an institutional repository. In this scenario it is reasonable to expect that research outputs would be published via the same public interface as publication outputs, and ReDBox can be setup to accommodate this.


In this example I have Fedora running on my laptop to simulate the VITAL activity for development purposes. I'm using version 2.2.4, but ReDBox has been tested and work with any version up to 3.5 (although all v3.x support uses a different build, they are almost identical).


2.1 Maven Project
Just like above, we've used a dev build that is close to what we want:


        cd \code\tf2\redbox-builds
        mkdir new-vital
        cd new-vital
        svn export https://redbox-mint.googlecode.com/svn/redbox/builds/dev-vital2/trunk svn


Make similar edits to the POM, as per above... we haven't come across anything particularly new yet. The system can be built to test the POM is valid as before: 


        cd \code\tf2\redbox-builds\new-vital\svn
        mvn clean install


### []()2.2 Curation Configuration

Similar to the flow from the last build we looked through we need to grab a plugin to do curation:


                <!-- Vital (Fedora v2.x) Curation Plugin -->
                <dependency>
                    <groupId>com.googlecode.redbox-mint</groupId>
                    <artifactId>redbox-vital-transformer-fedora2</artifactId>
                    <version>${redbox.version}</version>
                </dependency>


And modify the system configuration regarding curation. I didn't actually change any of these, but some of them (particularly the fedora connection details) are going to need to match whatever your institutions details are:


            "curation": {
                ...
                "pidProperty": "vitalHandle",
                "pidType": "handle",
                ...


            "transformerDefaults": {
                "vital": {
                    ...
                    "foxmlTemplate": "${fascinator.home}/vital/foxml_template_fedora2.xml",
                    ...
                    "server": {
                        "url": "http://localhost:8888/fedora/",
                        "username": "fedoraAdmin",
                        "password": "fedoraAdmin",
                        "namespace": "redbox",
                        ...
                        "publishedDomain": "hdl.handle.net"
                    },


            "portal": {
                ...
                "houseKeeping": {
                    "config": {
                        ...
                        "jobs": [
                            {
                                "name": "vital-handles",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/vital.script",
                                "timing": "0 0/15 * * * ?"


Things to be aware of here:

*   The FOXML template can be used to modify the initial object injected into Fedora. By default it's a bare minimum stub record that is set to Inactive. This just serves the purpose of tricking VITAL into creating a Handle for us.
*   The Fedora connection details are a start, and the system will establish a test connection on startup, but sometimes your Fedora security can bite you later. In particular check the [Fedora doco](http://fedora-commons.org/download/2.2/userdocs/server/security/AuthorizationXACML.htm#DEFAULT) on '`deny-apim-if-not-localhost.xml`'.
*   If your Handle/VITAL server is configured to use a different domain name when publishing Handles don't forget to modify this value.
*   The housekeeping script is used to follow up on the first stub sent to VITAL. It will keep checking back for the record until VITAL arranges a Handle for it, then continue the curation process. Make sure you update the namespace value in config to match your install or the script won't be able to find your record in VITAL.



Beyond this, we don't need to alter anything in our data source config (the '`dataset.json`' workflow). Since ReDBox only has one data source, there's no need for overriding config per data source, it is all in the system config:


            "transformer": {
                "curation": ["vital"],
                "metadata": ["jsonVelocity"]
            },
            "curation": {
                "neverPublish": false,
                "alreadyCurated": false
            },


Back in the system config, I found a bug in the v1.5 config for the vital build whilst writing this document. You can fix it at the same time:


            "proxy-urls": {
                ...
                "Services": "${mint.proxy.url}/Services/opensearch/lookup",
                ...
            },


### []()2.3 Institutional Configuration

Now, if the excitement isn't too much for you, you just need to change the institutional String values we looked at earlier in Mint. There is only one additional value for ReDBox:


            "redbox.identity": {
                "institution": "University of Examples",
                "RIF-CS Group": "The University of Examples, Australia",
                "internalGrantString": "urn:uoe:internal"
            },


The '`internalGrantString`' value is used as a fake ID inside the forms when an internal grant is entered. Other parts of the system look for this value, although if you investigating [other ways of integrating local Activity data](http://www.redboxresearchdata.com.au/documentation/system-administration/administering-mint/loading-data/loading-activity-data) this may be redundant... your call.


## []()3.0 The Big Picture

To finish everything off we are just go to test the curation process to ensure both systems are working nicely and talking to each other. Presumably you harvested the data into your Mint server as per normal? If not you need to do that first.


During the ReDBox data entry I'm only generating a basic record, but linking it to specific Mint entries for the purpose of demonstrating curation:

*   I'm adding 'Cosmo Costanza' as the creator. A Mint entry from the sample data.
*   I'm linking the Service record for ReDBox's OAI-PMH interface as 'Is available through:'.
*   For Data Affiliation I've linked the 'Advanced steam engine labs' Group.
*  I linked to Grant ID 510778.



So now we have linked two Party records (a Person and a Group), a Service record and an Activity record. It is time to fill in the rest of the mandatories for fake data and push the Collection through the workflow stages until it publishes. Make sure you have '`transactionManager.log`' tailing for both systems to see what the Curation Managers are doing. The follow steps are what should be expected behaviour with this setup:


### []()3.1 ReDBox Curation via VITAL

First the ReDBox Collection is going to try to get a persistent identifier from the Curation Manager. We've configured it to use our VITAL plugin, so it will generate a new stub record in Fedora. The log should show something like this:


        ... DEBUG  VitalTransformer     Received OID 'fde64984b5cf5eb03b722b7c391c8db8'
        ... INFO   VitalTransformer     New VITAL PID: 'uuid:2'
        ... DEBUG  VitalTransformer     New VITAL object created: 'uuid:2'
        ... INFO   VitalTransformer     No wait conditions have been met, processing halted


And you should receive an email that looks like this:


        This is an automated message from the ReDBox Curation Manager.


        NOTICE: The system has received a 'curation-confirm' event, but the record does not appear to be curated. If your system is configured for VITAL integration this should clear by itself soon.


        You can find this object here:
        http://localhost:9000/redbox/default/detail/fde64984b5cf5eb03b722b7c391c8db8


From here, on a server, you'd just wait. The VITAL background indexer will eventually notice the new stub record and generate a Handle for it. In my case I'm running just Fedora on my laptop, so I'm going to fake VITAL's activity by editing the record in Fedora. Logging into the Fedora admin client and editing the '`uuid:2`' object's DC datastream to add a Handle like so:

          <dc:identifier>http://hdl.handle.net/1234/1234</dc:identifier>


Meanwhile, ReDBox's housekeeping script has been puttering along, checking Fedora every 15 minutes to see if the Handle is ready. You can see it in the log like so:


        ... DEBUG  namicPageServiceImpl VITAL housekeeping executing
        ... DEBUG  Indexer              Adding security to Solr request: SearchRequest[query: vitalPid:redbox* AND NOT pidProperty:http*, params: {facet=[false], rows=[100]}]


Notice the line has '`vitalPid:redbox*`'... something is wrong there, you may remember above I said "_Make sure you update the namespace value in config to match your install or the script won't be able to find your record in VITAL._"... well I ~~forgot that~~ err... didn't do that on purpose to demonstrate what happens. A quick config change ('`uuid`', a reboot, and all is good). If you don't feel like waiting around for 15 minutes to run housekeeping you can always call it from the web interface directly, using the URL from your config file:

*  http://localhost:9000/redbox/default/hkjobs/vital.script



A successful execution looks something like this whilst still waiting, then the second execution show what happens after I add the Handle:


        ... DEBUG  namicPageServiceImpl VITAL housekeeping executing
        <SNIP>
        ... DEBUG  namicPageServiceImpl URL: 'http://localhost:8888/fedora/get/uuid:2/DC'
        ... DEBUG  namicPageServiceImpl Object 'fde64984b5cf5eb03b722b7c391c8db8', in VITAL 'uuid:2' has no handle yet






        ... DEBUG  namicPageServiceImpl VITAL housekeeping executing
        <SNIP>
        ... DEBUG  namicPageServiceImpl URL: 'http://localhost:8888/fedora/get/uuid:2/DC'
        ... DEBUG  namicPageServiceImpl Processing: 'uuid:2' <= 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  namicPageServiceImpl Handle: 'http://hdl.handle.net/1234/1234'


As soon as the housekeeping script can see a Handle the curation process resumes. ReDBox's Curation Manager looks through the form data an notices that you have linked 4 external records that are going to need to be curated before publication can proceed, so it will notify Mint:


        ... DEBUG  CurationManager      Checking Children of 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  CurationManager       * Needs curation 'redbox-mint.googlecode.com/parties/people/1237'
        ... DEBUG  CurationManager       * Needs curation 'redbox-mint.googlecode.com/parties/group/19'
        ... DEBUG  CurationManager       * Needs curation 'http://purl.org/au-research/grants/nhmrc/510778'
        ... DEBUG  CurationManager       * Needs curation 'redbox-mint.googlecode.com/services/2'
        ... INFO   ManagerQueueConsumer Outgoing message using broker: 'tcp://localhost:9201'


The last line is repeated a few times; that address is the Mint server's AMQ broker where ReDBox can talk to Mint's Curation Manager. You will in fact see a lot of log activity for a brief period whilst Mint and ReDBox are chatting away to each other. The steps below walk through this carefully, but much of this activity will have completed within seconds.


**NOTE**: I slightly doctored the above log because once again Services was missing some config from that build (it was noted above). After fixing it in ReDBox's config file and re-saving the Collection from the ReDBox form it separately curated the Service. I've just put them together above, because that's what *should* happen. If you needed to do the same thing it would look something like this:


        ... DEBUG  CurationManager      Checking Children of 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  CurationManager       * Needs curation 'redbox-mint.googlecode.com/parties/people/1237'
        ... DEBUG  CurationManager       * Already curated 'redbox-mint.googlecode.com/parties/group/19'
        ... DEBUG  CurationManager       * Already curated 'http://purl.org/au-research/grants/nhmrc/510778'
        ... DEBUG  CurationManager       * Needs curation 'redbox-mint.googlecode.com/services/2'


The next section delves into what Mint is doing with this info:
### []()3.2 Mint Curation (excluding People)

Mint should have automatically curated the Group, Activity and Service we linked, since our curation plugin is fairly simple (it just builds a URI based on a template). You will probably have received emails like this:


        This is an automated message from the Mint Curation Manager.


        This email is confirming that the object linked below has completed curation.


        You can find this object here:
        http://localhost:9001/mint/default/detail/3c6152c965bd4bd98e592dc0dde9daa7


The People record is dealt with separately below because the NLA integration is another complicating factor. But ignoring the People record's email you may have noticed that you curated 4 Mint records, even though you only linked to 3 of them. This is because the Group record brought a buddy along for the ride. When the Mint Curation Manager was processing the 'Advanced steam engine labs' Group it noticed that is had a stored relationship to the 'University of Examples' Group that needed to go into the RIF-CS as well, so it curated that as well; just as ReDBox and Mint talk to each other to resolve 'child' curation events, Mint can talk to itself in the same manner.


These 4 records complete curation and notify the Curation Manager that they are ready to go. Mint know's that ReDBox is waiting to hear back about 3 of those records (ReDBox isn't going to care about the second-hand relationship to 'University of Examples') so it tells ReDBox that they are ready and also that they have new identifiers that are now suitable for external publication. ReDBox is going to keep note of those, since it will need them when it comes time to generate the RIF-CS entry for the Collection (both ReDBox and Mint are rigorous in ensuring that only these curated IDs are used as RIF-CS keys).


ReDBox isn't ready to publish yet though, since it is still waiting to hear back from Mint about Cosmo Costanza's Party record. It is going to be a while... see below.


### []()3.3 Mint Curation (People)

Because we have National Library integration enabled (and because we've never sent Cosmo to the NLA before, it would only take a second if we had), Mint is going to have to stop here and generate an EAC-CPF record for Cosmo. The Curation Manager is aware of this, so AFTER it completes local curation it is going to generate a timestamp for the EAC-CPF's date of creation (forms the start of the maintenance history) and set an object property that the indexer is looking for. This will expose the record to the 'NLA_Harvest' portal.


All of that will have occurred straight away, and you should have received an email like this:


        This is an automated message from the Mint Curation Manager.


        This email is confirming that the object linked below has completed curation and is ready to be harvested by the National Library. NOTE: This object is not ready for publication until after the NLA has harvested it.


        You can find this object here:
        http://localhost:9001/mint/default/detail/a17784182ccc2459492950e2f28f7d77


The NLA needs to harvest your Mint server for Party records periodically and you will most likely need to log into the Trove Identity Manager (TIM) to create a new national level ID for Cosmo. This work is beyond the scope of this document, but the end result is a new national record that has an NLA Identifier, as well as our curated identifier. We have a housekeeping script in Mint that is going to search the NLA every 15 minutes looking for records using our curated ID (I say 'records', but logically of course there should only ever be one result). Once it finds the new record in existence it will grab the NLA ID and store it locally. ANDS prefers to use NLA IDs for People now, if they are available, so we are going to be using this in our RIF-CS.


The housekeeping script looks something like this whilst it is waiting (I snipped a few debug lines):


        ... DEBUG  namicPageServiceImpl NLA housekeeping executing
        ... DEBUG  SRUResponse          SRU Search found 0 results(s)
        ... DEBUG  namicPageServiceImpl Object 'a17784182ccc2459492950e2f28f7d77' does not yet have a national Identity in NLA


It is unlikely you are going to be connecting development machines to the NLA however, but you can always fudge your dev data. Let's exercise some 1337 haxor skills and... edit a text file. Cosmo's Object ID is 'a17784182ccc2459492950e2f28f7d77' (it's the last part of the URL) and it can be found in storage under a path something like this:

        /storage/{some_long_hash}/a1/77/84/a17784182ccc2459492950e2f28f7d77


One of the payloads inside is '`TF-OBJ-META`' which holds key/value pairs containing the Object's properties. We are going to give Cosmo an identity crisis and make him think that he is really 'Harvey Johnston'. I have no idea who Harvey is, but I found him some sample data, and he exists in the National Library's records, so if we change this:

        localPid=http\://localhost\:9001/mint/published/detail/a17784182ccc2459492950e2f28f7d77


to this (one of Harvey's other IDs that the NLA knows about):

        localPid=02abace9-0d5e-4271-96ee-d676c2f45aa2


and 'Reindex' Cosmo (and admin action on the details screen of a record) it will update the Solr index the housekeeping script is looking at, and make the search return an ID. It isn't Cosmo's ID, it's Harvey's, but we don't care, we just want dev data to test everything is plumbed up nicely.


You can wait the 15 minutes as usual or hit the housekeeping script in your browser:

* http://localhost:9001/mint/default/hkjobs/nla.script





        ... DEBUG  namicPageServiceImpl NLA housekeeping executing
        ... DEBUG  SRUResponse          SRU Search found 1 results(s)
        ... DEBUG  namicPageServiceImpl Object 'a17784182ccc2459492950e2f28f7d77' has a new national Identity in NLA (http://nla.gov.au/nla.party-492067)


At this point your log files are probably going to go bat-shit insane with activity. The starting point is in the Mint log (look for it by timestamp is usually easiest if you are just scrolling) and it will look like this:


        {
            "oid": "a17784182ccc2459492950e2f28f7d77",
            "task": "curation-confirm"
        }


So this is the housekeeping script telling the Curation Manager that Cosmo is ready to go. The Curation Manager then notices that Cosmo is a member of 3 Groups (I picked this sample data on purpose): 'School of Fine Arts', 'School of Woodwork' and 'Grant proposal writing CRC', so it will ask them to curate as well. 'Grant proposal writing CRC' is easy, since it is simply linked to the 'University of Examples' and it curated earlier, but the other two are part of the 'Faculty of Arts', and the Curation Manager hasn't seen that one before, so it needs to go curate. By now we are reaching the tail end, since it is also pointing at the 'University of Examples' (seen that).


An older Mint system wouldn't see quite the same level of activity, since all of the main Group records and prominent researchers will have been curated quite early, but there will still be some chatter as the Curation Manager makes sure they are all building their links using the correct curated IDs and RIF-CS gets updated to take in account and new relationships. When all the Mint activity dies down and Cosmo and all of his dependent records are ready to go Mint will finally send a message back to ReDBox with the last data it was waiting on:


        {
            "task": "curation-pending",
            "oid": "fde64984b5cf5eb03b722b7c391c8db8",
            "originId": "redbox-mint.googlecode.com/parties/people/1237",
            "originOid": "a17784182ccc2459492950e2f28f7d77",
            "curatedPid": "http://nla.gov.au/nla.party-492067"
        }


Basically these messages are just saying: "That guy you asked me about (redbox-mint.googlecode.com/parties/people/1237)..." _That's Cosmo's INTERNAL ID ReDBox had from the forms_ "... is ready to go, and his proper ID is now http://nla.gov.au/nla.party-492067".


### []()3.4 Everything Published

Since this is the last information that ReDBox was waiting on it is time to finally publish all of the data we just curated. The linked data network is complete without any broken links and we can send them all to RDA without any worries. The logging gets excessive here because they all need to be spoken to again as ReDBox sends the word out. Remember how many times above we ended up pointing at 'University of Examples'? Well it is going to receive a similar number of publish messages as well, although that is fine it will action the first and ignore all the rest. The messages also flow in the other direction, since over time the network of relations can get quite complicated. So 'children' also notify 'parents' on publish event (for example, when a new researchers goes out and the RIF-CS for a Faculty gets a new 'hasMember' relation it will let all older members know it is publishing again, so that they may update if desired).


The only 'real' work that goes on in this step is in ReDBox because we have the VITAL plugin turned on. I has been waiting to turn that Fedora stub record into something more fully fleshed out, and now it can:




        ... DEBUG  CurationManager      Publishing 'fde64984b5cf5eb03b722b7c391c8db8'
        ... INFO   CurationManager      Publication flag set 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  CurationManager      Publishing Children of 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  CurationManager       * Publishing 'redbox-mint.googlecode.com/parties/people/1237'
        ... DEBUG  CurationManager       * Publishing 'redbox-mint.googlecode.com/parties/group/19'
        ... DEBUG  CurationManager       * Publishing 'http://purl.org/au-research/grants/nhmrc/510778'
        ... DEBUG  CurationManager       * Publishing 'redbox-mint.googlecode.com/services/2'
        ... DEBUG  VitalTransformer     Received OID 'fde64984b5cf5eb03b722b7c391c8db8'
        ... DEBUG  VitalTransformer     Existing VITAL object: 'uuid:2'
        ... INFO   VitalTransformer     Wait condition 'published' found.
        ... INFO   VitalTransformer     Activating object in fedora: 'fde64984b5cf5eb03b722b7c391c8db8'
        ... INFO   VitalTransformer     Processing PID to send to VITAL: 'marc.xml'
        ... INFO   VitalTransformer     Creating new datastream: 'MARC'
        ... DEBUG  VitalTransformer     LABEL: 'MARC', STATUS: 'A', GROUP: 'X'
        ... INFO   VitalTransformer     Processing PID to send to VITAL: 'oai_dc.xml'
        ... INFO   VitalTransformer     Updating existing datastream: 'DC'
        ... DEBUG  VitalTransformer     LABEL: 'Dublin Core Metadata', STATUS: 'A', GROUP: 'X'
        ... INFO   VitalTransformer     Processing PID to send to VITAL: 'rif.xml'
        ... INFO   VitalTransformer     Creating new datastream: 'RIF'
        ... DEBUG  VitalTransformer     LABEL: 'RIF-CS Metadata', STATUS: 'A', GROUP: 'X'
        ... INFO   VitalTransformer     Processing PID to send to VITAL: '.tfpackage'
        ... INFO   VitalTransformer     Creating new datastream: 'REDBOX'
        ... DEBUG  VitalTransformer     LABEL: 'ReDBox Form Data', STATUS: 'I', GROUP: 'M'


So when the dust settles you can look in the 'published' portal on both systems and observer that by clicking 'Publish' on one Collection record we have curated and published 9 RIF-CS records in total. A fully fleshed out Collection with related data elements and multiple creators across multiple faculties could potentially get much larger... and the logs are like War & Peace. Although on that note, you can tune the logging levels to remove 'DEBUG' and they become a lot smaller. I like the extra data when problems occur though.


## []()4.0 Managing Multiple Environments



Something that you may need to consider is managing your institutional build in development, testing and production environments. Ideally, the aim here is to keep settings as consistent as possible but there are some settings that inevitably must change from environment to environment.


We utilised, a profile earlier to build a tarball package but they are also ideal candidates for managing environmental differences. 


        <profile>
 `<id>qa</id>`
 `<properties>`
 `<redbox.location>/opt/redbox</redbox.location>`
 `       <server.url.base>http://redboxresearchdata.com.au/redbox/</server.url.base>`
 `</properties>`
        </profile>


## []()5.0 Using an Artifact Repository



As we are using Maven, to configure our Institutional Build. The ability to use an artifact repository such as Sonatype Nexus to store and manage our builds is available.


To do this, simply add your artifact repository details to the repositoryManagement section and set the username and password for them in your Maven settings.xml file.




        <distributionManagement>
 `<snapshotRepository>`
 `<id>redbox-nexus-snapshots</id>`
 `<name>Redbox Nexus Snapshots</name>`
 `<url>http://dev.redboxresearchdata.com.au/nexus/content/repositories/snapshots/</url>`
 `</snapshotRepository>`
 `<repository>`
 `<id>redbox-nexus-releases</id>`
 `<name>Redbox Nexus Releases</name>`
 `<url>http://dev.redboxresearchdata.com.au/nexus/content/repositories/releases/</url>`
 `</repository>`
        </distributionManagement>