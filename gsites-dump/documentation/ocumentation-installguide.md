## []()Introduction

ReDBox and Mint releases are packaged up and made available for download via [our deployment repository](http://dev.redboxresearchdata.com.au/nexus/content/repositories/releases/com/googlecode/redbox-mint/). Installing both systems is straight-forward and the following sections and links will guide you through the process. 
## []()System Requirements

Both the ReDBox and Mint systems have the following prerequisites:
* Operating system: any modern general-purpose operating system will work

 * We've tried a number: Ubuntu, RedHat, Solaris, Windows etc
 * Our user community let us know which systems they're using: see [this thread for details](https://groups.google.com/d/topic/redbox-dev/vJHNaWzRASo/discussion).
* Sun's [Java JVM 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)
* Storage space: this really depends on how much data you're storing

 * 10Gb is probably a good start
 * Mint will need more storage space as it holds the Geonames data
* RAM (memory): 1Gb each (at least) for ReDBox and Mint

 * [Smaller memory allocations are possible](documentation-system-administration-general-administration-configuring-ram), especially for development/testing systems

You can run Mint and ReDBox on the same server - just make sure you have the memory capacity.
### []()Server software

The installations for Mint and ReDBox include the [Jetty](http://jetty.codehaus.org/jetty/) server. This is a robust server that should suit most requirements. The documentation provided via this site is based on the default Jetty configuration provided with Mint and ReDBox. Currently, no work is being undertaken to test either ReDBox or Mint with other container servers (such as [Apache Tomcat](http://tomcat.apache.org/)).
In some cases you may wish to use Apache's HTTP server as the public-facing web server for ReDBox and Mint. In this case we suggest using [mod_proxy](http://httpd.apache.org/docs/2.2/mod/mod_proxy.html) to provide a reverse proxy between the users and the Jetty servers running ReDBox and Mint. 
## []()Installing

As of the v1.8 release, there is now one build of ReDBox and Mint that include all the supported plugins. This means installing the application can be as simple as downloading the packaged applications and configuring the application to your needs. A key part of the installation process is considering what your [curation options](documentation-system-overview-curating-linked-data) are, as this will decide which plugins need to be configured with your server(s). The initial install puts this question aside for a moment and uses the [Local Curation plugin](documentation-system-administration-integration-local-curation) to get things up and running quickly. This is ideal for a demonstration/test environment where you want to take a look at the system and how it works, but is not generally advised for production systems. Please do not put real data into the initial install with an expectation of smoothly migrating to something else... it is technically possible, but was not designed for this and will cause you more annoyance then convenience at a later date.

In general, we also suggest that installing Mint first is the best option. As mentioned in the [System Overview](documentation-system-overview), Mint provides ReDBox with a variety of look-up services.

There are currently 3 methods to install ReDBox and Mint

* [Using prepackaged tarballs (Linux or Windows)
](documentation-installguide-current-demo)
* Installing by RPM (ReDHat-based linux distributions)
* [Installing using Puppet (ReDHat-based linux distributions)](https://github.com/redbox-mint-contrib/puppet-redbox/blob/master/README.md)

## []()Developer installation
For the developer install instructions, please refer to [Developer Installation Guide](documentation-installguide-developer-installation-guide).