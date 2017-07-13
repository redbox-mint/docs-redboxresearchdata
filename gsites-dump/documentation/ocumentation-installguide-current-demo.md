For the purposes of this guide I'm installing on windows, but you can install on any system/OS with access to the appropriate Java VM (as per [requirements](documentation-installguide), you will need Java 1.6 or greater on your system). 
### []()0. Read the doco
 
Read through this installation documentation. We always recommend a complete read through of any of our doco before starting a step-by-step follow along. 
### []()1. Download
 
Download the version of [Mint](http://dev.redboxresearchdata.com.au/nexus/service/local/artifact/maven/redirect?r=releases&g=com.googlecode.redbox-mint&a=mint-distro&v=LATEST&c=build&e=tar.gz) and [ReDBox](http://dev.redboxresearchdata.com.au/nexus/service/local/artifact/maven/redirect?r=releases&g=com.googlecode.redbox-mint&a=redbox-distro&v=LATEST&c=build&e=tar.gz) you'd like to install (v1.8 at time of writing) and copy them to wherever you would like your servers installed. I've copied them to '`c:\tools\tf2`' for the sake of this demo ('`tf2`' being 'The Fascinator 2'... the platform ReDBox and Mint are built inside of) but you can put them anywhere really. 
 The files are compressed [tarballs](http://en.wikipedia.org/wiki/Tar_%28file_format%29), which will be familiar to the more technically inclined readers, but if you are unfamiliar it is simply a different form for packaging to the more typical ZIP files found in Windows land. Most standard decompression tools will handle them just fine, although you may need to extract them twice; once to turn the '`.tar.gz`' files into '`.tar`' files, and then again to unpack the tarball. After this you should have folders somewhat like below and can throw away the tarballs: 
 `c:\tools\tf2\mint`
 `c:\tools\tf2\redbox`  
From here your servers are almost ready to go, we are simply going to give them some basic config to get them over the line. 
### []()2. Configuration
 
For windows, we simply want to alter the '`tf_env.bat`' script for each system. These set the environment for our startup scripts, and there are corresponding shells scripts beside them ('`tf_env.sh`') for other OS's. So edit these files: 
 `c:\tools\tf2\mint\server\tf_env.bat`
 `c:\tools\tf2\redbox\server\tf_env.bat`  
Here is a sample for how I changed my system (Mint, but ReDBox is the same aside from the path)... the mail server information is not strictly required for testing, but I want my server to be able to send emails. If you don't set this up the server will still function ok, but you'll find some error messages in log files whenever it tries to send you an email. Change the other settings (like port numbers) if you feel like it, but they are perfectly fine as is for a basic demo.
  
 `set PROJECT_HOME=c:/tools/tf2/mint`
 `set SMTP_HOST=smtp.cqu.edu.au`
 `set ADMIN_EMAIL=greg.pendlebury@gmail.com`  
Notice the forward slashes in my path, as opposed to the typical backslashes windows users would use. These aren't mandatory, but they are a really good idea. 
Finally, you can optionally index [Geonames data](documentation-system-administration-administering-mint-geonamesdata) into your Mint server if you'd like to use this in the demo system. It will function perfectly fine with an empty index... you just can't use the feature of course.
 If you are looking for background information on the server's setup, consider reading the [General Administration](documentation-system-administration-general-administration), [Mint Administration](documentation-system-administration-administering-mint) and [ReDBox Administration](documentation-system-administration-administering-redbox) sections. These will give you an overview of how the system is laid out and how to set it up for your environment. The points above are more streamlined, pointing at exactly what is required for starting the server as a demo. 
### []()3. Running the Systems
 
You are now ready to start your systems. If you are using a linux/unix or mac OS the controls scripts look fairly standard: 
 `cd /opt/mint/server`
 `./tf.sh start`  
I show this because the windows build (at the moment) isn't really setup to run as a background process. The Windows start script (which needs to be run from a command prompt) will not return to the terminal upon completion, and you should press CTRL-C to stop the server when you want it to finish. Depending on your version of Windows, just closing the terminal may not send a graceful shutdown signal to the server before turning off... this may not be a big deal to you though on a demo system; your call. If you'd like to run the server as a background process on Windows just ask on the mailing list. Several users have run Fascinator platform applications this way before... it's just not particularly common. 
 `cd \tools\tf2\``mint\server`
 `tf_start.bat`  
Also, if it is not obvious from the above notes you are going to need two terminals, one for Mint, and one for ReDBox. You can check [your system logs](documentation-system-administration-general-administration-system-layout) if things didn't appear to start correctly... but the demo is pretty basic without complications like external connections that typically cause these problems. 
The systems should now be online and accessible in your web browser: 
* [http://localhost:9001/mint/](http://localhost:9001/mint/)
 * [http://localhost:9000/redbox/](http://localhost:9000/redbox/)
 
 
### []()4. Admin Password
 
Change the 'admin' user's password. The default password is 'admin' on Mint, or 'rbadmin' on ReDBox. This should be changed as soon as the system is online if you intend to expose you system to others... but this is a demo; again, your call. 
### []()5. Loading Data
 
Your servers are online, but they are empty. For ReDBox you are going to be populating this yourself, but Mint will need some sample data loaded for a demo. Take a look at the [documentation on loading data](documentation-system-administration-administering-mint-loading-data). Below I've listed the commands for all the data I loaded into my laptop: 
 `cd \tools\tf2\mint\server`
 `tf_harvest.bat ``Activities_NHMRC_2010`
 `tf_harvest.bat ANZSRC_FOR`
 `tf_harvest.bat ANZSRC_SEO`
 `tf_harvest.bat Funding_Bodies`
 `tf_harvest.bat Languages`
 `tf_harvest.bat Parties_Groups`
 `tf_harvest.bat Parties_People`  
Those are most of the files available in the demo... not all, but you don't need to run them all, this is just a first basic sample that will allow you to use ReDBox as it was intended... enjoy!