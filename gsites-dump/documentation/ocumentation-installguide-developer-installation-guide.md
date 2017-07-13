[[_TOC_]]


## []()Background

The Research Data Box (ReDBox) is a Maven project using various Fascinator libraries as dependencies. The build process has been setup to deploy as ready to operate server with all configuration and workflows in place.


Please note that we use Maven v2 POMs, not v3, and using the v3 Maven client will not be compatible. The latest v2 client recommended is [v2.2.1](http://maven.apache.org/download.html#Maven_2.2.1).


The associated Mint server follows the same pattern as below, just substitute 'mint' for 'redbox'. Mint starts on port 9001 by default.


The process provided here describes how to grab a copy of the ReDBox codebase and build it on your local system. In general, we suggest that sites utilise institutional builds to create locally customised ReDBox and Mint installs, rather than editing the core codebase. For further details, please refer to [Create your own Institutional Build](documentation-how-to-institutional-builds) and [Branding](documentation-system-administration-general-administration-branding).


The ReDBox codebase is manage in the GitHub system. Please refer to the [Development](development) page for more information.
## []()Project Layout

The build process is currently divided into 3 tiers of Maven projects, although one tier is [The Fascinator platform](http://sites.google.com/site/fascinatorhome/) and not really part of the project. The two tiers contained in our project are:

* [ReDBox](https://github.com/redbox-mint/redbox) 'core' - This project deploys The Fascinator platform and provides central configuration. Sub-modules contain code relevant different use cases, but they are not always deployed in all cases. This project is the focus of this document.
* [ReDBox 'code' project](https://github.com/redbox-mint) - A variety of institutional builds are responsible for bring together the 'core' project configuration with local needs and deploying a finished server. These projects are not covered here as [Institutional Builds](documentation-technical-institutional-builds) they have their own documentation.

Deploying the Code
The following commands will checkout the ReDBox source code to the `/opt/redbox/code` directory:


        mkdir /opt/redbox
        cd /opt/redbox
        git clone https://github.com/redbox-mint/redbox.git code


After checkout, the following command will build the project and release SNAPSHOT artifacts into your local Maven repository. If you are doing dev work on your institutional build, this is how you would make changes in the core for local testing.


        cd /opt/redbox/code
        mvn clean install


Note that this step will download required dependencies as necessary. Also if you are behind a proxy, you may want to review the [Maven documentation](http://maven.apache.org/guides/mini/guide-proxies.html) regarding proxies.
### []()Directory structure

The build scripts deploy several directories when triggered by an institutional build (the core won't run these unless you create the 'deploy.server' trigger file yourself for testing):
 * `/opt/redbox/home` - Contains the configuration
 * `/opt/redbox/portal` - Contains the web interface views and templates
 * `/opt/redbox/server` - Contains the web server (Jetty), The Fascinator portal and Solr web applications, plugins and related dependencies
 * `/opt/redbox/solr` - Contains the Solr configuration and indexes
 * `/opt/redbox/storage` - This is created by the file system storage plugin (not deployed after building)


Note that these directories are created adjacent (on the same level) to the source code, `/opt/redbox/code`, so if the code was checked out to `/usr/local/redbox`, then the directories would be created in `/usr/local/`.
## []()Running

Before starting the server, review the [Installation Guide](documentation-installguide) as well, as it contains a variety of post-installation configuration steps.


Use the provided script `/opt/redbox/server/tf.sh` to control the ReDBox server. The script takes one of the following parameters:
 * `start` - Start ReDBox
 * `stop` - Stop ReDBox
 * `restart` - Stop ReDBox if it is running, then start it again.
 * `status` - Check if ReDBox is running or not


For example:
        cd /opt/redbox/server
        ./tf.sh start


This will start the ReDBox server and will be available at http://localhost:9000/redbox/default/home