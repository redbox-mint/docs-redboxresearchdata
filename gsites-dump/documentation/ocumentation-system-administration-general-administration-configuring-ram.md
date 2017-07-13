In the [control scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) you'll notice both Mint and ReDBox have settings that specify how much RAM to allocate the JVM on startup: 
 `JVM_OPTS="-XX:MaxPermSize=256m -Xmx512m"` 
These values should be modified based on your server's available resources. Below is a [quote from the mailing list](http://groups.google.com/group/redbox-repo/browse_thread/thread/c06dd7d2bdb15b70#) discussing this issue:  **RAM** 
This is the most important consideration. Java tends to be a memory hungry beast, and it's hard to gauge a requirement without considering WHAT you are doing with the system. The size of the dataset, the nature of the datset, the expected activities of users as well as the volume of user traffic all play a part. 
The keys are what total heap space to allocation Java, and how much perm gen space it should have. If either of these areas run out of resources the JVM dies. You can reboot and things are happy at that point, so for picking your specs you are trying to balance avoidance of a crash and availability of resources. On some servers (like dev/test/demo servers) you can save on RAM and cron a reboot nightly for example. 
RAM is allocated at JVM startup like so: -XX:MaxPermSize=512m -Xmx1024m and those two numbers are typically what we are most concerned about. 
Some example ballpark figures from other systems we run on the same codebase (and some other thoughts): 
* We run a Media Repository at USQ with student traffic and fairly intensive video transcoding going on server-side. The above numbers 512m PermGen and 1024m total heap has been sufficient for our production server and it has never run out of RAM. This runs on Red Hat.
 * We ran a demo system on Windows 2008 and the total box only had 2GB so I used -XX:MaxPermSize=256m -Xmx512m and noticed periodic outages (a few times a week). Increasing to -XX:MaxPermSize=364m -Xmx768m made all the troubles go away. This was low traffic, but very intensive processing requirements on the data.
 * Our development machines tend to be more generously resourced (512/1024) and we don't see problems. This is across the board; Mac, various Linux, Solaris, Windows server and desktop.
 
 * ...
  
Given that these numbers represent just the JVM you also need to account for running the OS and anything else on that box. 
... 
...the Solaris JVM implementation has a different internal implementation with regards to multi-threading and memory allocation... that's just the way Solaris is. 
...In general, if deploying on Solaris, you might want to pad some extra RAM onto the JVM to account for the difference.