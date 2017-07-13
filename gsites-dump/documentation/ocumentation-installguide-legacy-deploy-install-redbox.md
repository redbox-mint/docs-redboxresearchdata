**18th January 2012: *This page is deprecated.***

 ***It describes the installation process for ReDBox versions prior to the release of version 1.4.
 With the release of version 1.4 the installation process for ReDBox [has been changed considerably](documentation-installguide).***  
This is the installation guide for ReDBox, for administrators. If you are installing Mint as a developer, try the [installation process](http://code.google.com/p/redbox-mint/wiki/DeveloperInstallationGuide) from the developer's wiki instead. 
### []()0. Read the doco
 
Read through this installation documentation. Ensure that you have the necessary details for VITAL integration. 
**If you are installing v1.2 or v1.3, it is strongly recommended that you move to v1.3.1. This was a bug fix release for those earlier versions.** 
### []()1. Download
 
[Download](http://code.google.com/p/redbox-mint/downloads/list) the version of ReDBox you'd like to install. These ZIPs are bundled outputs of a clean developer installation and contain a ready to run (but unconfigured) server. 
 As of version 1.0.1 each ZIP has been broken into two parts, a 'project' ZIP containing the server mentioned above, and a 'dependencies' ZIP containing all of the external JAR files on which ReDBox depends. The reason for this is simply file size, since our 'single ZIP' approach started to exceed the size limit that could be uploaded to Google Code. In v1.3, a third ZIP for 'fedora' was added to the mix, again, to avoid exceeding Google's upload limit. It just needs to be unpacked in the same location as the other two.
 
### []()2. Unpacking
 
Choose a suitable location to unpack your ZIPs (unzip both in the same spot, they will overlay each other). It will create a folder called 'redbox'. We typically install under '/opt', creating the path below (used throughout this guide): 
        /opt/redbox 
### []()3. Configuration
 
Similar to the [Mint installation](documentation-installguide-installing-mint), you now need to configure your server... although ReDBox is a lot easier: 
 * Modify the [controls scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) with your correct file path, server address and port.
 * Either configure or disable [VITAL integration](documentation-system-administration-administering-redbox-vital-integration).
  If you are looking for background information on the server's setup, consider reading the [General Administration](documentation-system-administration-general-administration) and [ReDBox Administration](documentation-system-administration-administering-redbox) sections. These will give you an overview of how the system is laid out and how to set it up for your environment. The bullet points above are more streamlined, pointing at exactly what is required for starting the server. 
### []()4. Running the System
 
Ensure you know where the logs are, and monitor them during system startup, looking for problems. To start the server, simply execute the control script in the 'server' directory: 
 `cd /opt/redbox/server`
 `./tf.sh start` 
Check that the system started correctly via the logs, and ensure it is accessible through your browser. 
### []()5. Admin Password
 
Change the 'admin' user's password. The default password is 'rbadmin'. This should be changed as soon as the system is online.