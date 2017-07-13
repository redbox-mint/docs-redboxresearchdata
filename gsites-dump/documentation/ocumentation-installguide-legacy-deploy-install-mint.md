**18th January 2012: *This page is deprecated.***

 ***It describes the installation process for Mint versions prior to the release of version 1.4.
 With the release of version 1.4 the installation process for Mint [has been changed considerably](documentation-installguide).***  
This is the installation guide for Mint, for administrators. If you are installing Mint as a developer, try the [installation process](http://code.google.com/p/redbox-mint/wiki/DeveloperInstallationGuide) from the developer's wiki instead. 
### []()0. Read the doco
 
Read through this installation documentation. Some steps require data you may not have, such as Handle integration, or that take a long time, such as Geonames. 
**If you are installing v1.2 or v1.3, it is strongly recommended that you move to v1.3.1. This was a bug fix release for those earlier versions.**

  
### []()1. Download
 
[Download](http://code.google.com/p/redbox-mint/downloads/list) the version of Mint you'd like to install. These ZIPs are bundled outputs of a clean developer installation and contain a ready to run (but unconfigured) server. 
 As of version 1.0.1 each ZIP has been broken into two parts, a 'project' ZIP containing the server mentioned above, and a 'dependencies' ZIP containing all of the external JAR files on which Mint depends. The reason for this is simply file size, since our 'single ZIP' approach started to exceed the size limit that could be uploaded to Google Code. 
### []()2. Unpacking
 
Choose a suitable location to unpack your ZIPs (unzip both in the same spot, they will overlay each other). It will create a folder called 'mint'. We typically install under '/opt', creating the path below (used throughout this guide): 
        /opt/mint 
### []()3. Configuration
 
Here is a checklist of points you need to address from these areas, with some specific links to relevant documentation: 
 * Modify the [controls scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) with your correct file path, server address and port.
 * Either install and configure or disable [Handle integration](documentation-system-administration-administering-mint-handle-server).
 * Ingest or copy [Geonames data](documentation-system-administration-administering-mint-geonamesdata), or again, you can disable.
 If you are looking for background information on the server's setup, consider reading the [General Administration](documentation-system-administration-general-administration) and [Mint Administration](documentation-system-administration-administering-mint) sections. These will give you an overview of how the system is laid out and how to set it up for your environment. The bullet points above are more streamlined, pointing at exactly what is required for starting the server. 
### []()4. Running the System
 
Ensure you know where the logs are, and monitor them during system startup, looking for problems. To start the server, simply execute the control script in the 'server' directory: 
 `cd /opt/mint/server`
 `./tf.sh start` 
Check that the system started correctly via the logs, and ensure it is accessible through your browser. 
### []()5. Admin Password
 
Change the 'admin' user's password. The default password is 'admin'. This should be changed as soon as the system is online.