## []()About this document
 
### []()Purpose
 
This document builds on [the Core Fascinator documentation](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/rebuilding-your-system) regarding the `ReIndexClient`. It provides specific information on how this tool is used in ReDBox and Mint, as well as related information on what you need to do to create a useful system backups in the first place.
  
### []()Document Status
 
In Draft 
### []()Creation Date
 
07 October 2011 
### []()Background Reading
 
The aforementioned [Fascinator documentation](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/rebuilding-your-system) on the `ReIndexClient` is essential to understand the context of this document, as well as good knowledge of the [application's directory layout](documentation-system-administration-general-administration-system-layout).
  
### []()Audience
 
 * Systems Administrators
 * Developers
  
## []()What Should I Backup?
 
The easy answer is 'everything', and it is generally not too bad an idea to take a complete copy of the entire application periodically just in case something happens that this process/procedure does not account for. 
This may not be possible for everyone however, and even if it is you'll be backing up a lot of stuff that may not be particularly important, so it may not be feasible for nightly backups (if you are doing those... no idea how frequently people would want to backup their servers); so this section details the really critical parts of the system with which you can do a complete system rebuild, upgrade and/or migration. 
In order of priority: 
1. Your '`storage`' folder. This is the big one; it holds all of your objects, form data, rendered metadata templates, persistent identifiers and curated relationships. Pretty much everything that represents your system in its current state can be found here. If for some strange reason you can't backup anything else, just backup this folder and you can always rebuild.
 1. Your audit log (or event log) is probably the next most important 'piece' of data (it's really a Solr index), if you ever want to look back through the history of your objects. It is not critical to functionality of the system, but it is always nice to have. The collection of files that make this Solr index are found under the '`solr`' directory, specifically '`/solr/indexes/eventLog/index`'. The [event log's schema](http://code.google.com/p/the-fascinator/source/browse/the-fascinator/trunk/plugins/indexer/solr/src/main/resources/solr/indexes/eventlog/conf/schema.xml) is setup with '`stored="true"`' on very field, so if for some reason we ever need to migrate this to something it is viable. At this stage though the restore procedure just needs those files to be in the same spot on a new server. note1.
 
 1. Your system log files, under '`/home/logs`' might be of interest, but typically there is not a lot here that you'd want unless something goes wrong. The system should be automatically performing log rotations on these and compressing the old logs into an archive sub-directory, so it is generally not a big deal to also include them in a backup for the sake of completeness.
   That's it, everything else can be rebuilt from the data inside of storage.
 
 *note1 **NOTE:** Because pre-v1.1 installs used a much older version of Solr (1.4 vs. 3.3+) it is possible, although untested, that you can't just drop old audit logs in place on newer versions. Having said that only from v1.2 has ReDBox started to submit meaningful ReDBox-specific audit entries to the log. Older logs will just contain the system generated entries from the core Fascinator platform. Because these don't provide a lot of useful information, we didn't even recommend backing up your audit logs on those versions of the product.*
 
 
### []()Security Data


There are three types of security plugins used in Mint/ReDBox. You might want to backup some additional files if you have made significant customisations in this area.

* Authentication Plugin: The default 'Internal' plugin stores usernames and hashed passwords in '`/home/security/users.properties`'. Backing up this file is all you need.

* Roles Plugin: Pairing up usernames with privileged security roles. Just like above, all you need to back is '`/home/security/roles.properties`'.
* Access Control Plugin: Generally you don't need to back this up, since the Mint and ReDBox run security based on data source configuration. The restore process has enough information to rebuild your security model without backing it up. If you have made local customisations and chosen to manually edit security as administrators then you can still backup the Derby database that runs security. It is found here: '`/home/database/tfsecurity`'.


### []()Enqueued Messages
 
The ActiveMQ messaging system that ReDBox and Mint use to send system messages to each other, and internally for their own processes, is backed into a datastore on disk. Obviously it would be bad to destroy/lose this datastore if there are enqueued messages waiting to process. For the most part this isn't an issue though, since enqueued messages indicates heavy load, something that you should only ever see if sysadmins are performing administrations actions like a harvest or system-wide reindex. Just don't backup your system whilst you do this kind of stuff to it... but I'm fairly certain that's obvious.
You can see message activity on the administrative queues in the web portal. Something like this: [/redbox/default/queues](http://redbox-dev.cqu.edu.au/redbox/default/queues) (you need to be logged in as an admin). You want to see '0' for all entries in the 'Size' column.
 
Presumably most backups will be scheduled to occur during off-peak periods, so you may even want to consider scheduling a graceful shutdown of the server beforehand. This would serve the dual purpose of preventing message loss, but also avoid active file locks from the web server in storage conflicting with your backup. This might occur if (for example) you have an ANDS harvester hitting you at night time and touching numerous objects in storage. These sort of considerations are really up to individual institutions to consider, since your backup process may even be careful enough to avoid these file lock issues anyway... I'm by no means a sysadmin.
  
### []()Configuration and Customisations
 
We have deliberately avoided the issue of local customisations and configuration above. It is presupposed that whatever you do to an installation after building it is either: 
1. Very trivial changes that you've documented and perform manually.
 1. OR. In complicated scenarios I would seriously recommend your changes be backed into some version of source control in a way that let's you just apply them as a patch to a new build. Some work scheduled for v1.4 will hopefully make this really easy to do for everyone.
 
  
## []()Restore Tool Configuration
 
This is generally pretty simple; out-of-the-box from v1.2 the default configuration look like this:
        "restoreTool": {
            "harvestRemap": {
                "enabled": false,
                "allowOlder": false,
                "failOnMissing": false
            },
            "migrationScript": "${fascinator.home}/restore/redboxMigration1.2.py"
        }

It assumes that you are rebuilding a system in place (no harvest remap), with the possibility of a version upgrade. The migration script is smart enough to notice when form data is out-of-date and it will try to upgrade it to v1.2. It will also backup your form data inside the object as a separate datastream before making an alterations, and add a line to the object's audit log letting you know it was altered by the upgrade script.
**Migration** - If you'd like to perform a migration to another system, another path on your server, or change the username under which you run the server, you **MUST** enable the '`harvestRemap > ``enabled`' flag to ensure your harvest files are all working nicely.
 
## []()Rebuilding Your System
 
Hopefully, this is actually the easy part. If you've gone to the trouble of ensuring that your backups are performed correctly a system rebuild should be nice and smooth, with little extra overhead beyond a standard installation:
  
1. Build/install your system as per normal, assuming it is a fresh clean install.
 1. Re-apply any local customisations and configuration required.
 1. BEFORE you start that new server you want to copy your backed up '`storage`' folder into place. Forgetting this isn't particularly a deal breaker, you'll just have to turn the system off and delete the new '`storage`' folder the system builds on startup if it doesn't find one.
 1. The same goes for your audit log (if you backed it up) into '`/solr/indexes/eventLog/index`'.
 1. You can restore the system logs if you like, although I would tend to keep them separate for posterity, and start the new system with clean, empty logs. Perhaps put them into a custom folder there labelled as '`oldLogsVXX`' or similar; your choice.
 1. Now start the server. You can browse around in the web interface and it will look empty because it has nothing in the Solr index. In fact you should load at least one page from the web, since the [Tapestry Services](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/portal/java-core#TOC-Tapestry-Services) inside the web portal aren't fully online until the first web page gets loaded. We need the system to be online so the messaging framework and tool chain are all up and running, remembering that [the last job](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/rebuilding-your-system#TOC-3.2-How-it-works) of the `ReIndexClient` is to send messages into the tool chain.
 1. Finally, we running the restore script from the '`server`' folder where all the [control scripts](documentation-system-administration-general-administration-configuration-files-control-scripts) are: 
        ./tf_restore.sh 
   
Presuming nothing explodes and the universe remains on an even keel, you'll find some logging output in '`/home/logs/``reindex.out`'.
 
 ***NOTE:** There were substantial changes to logging with the release of v1.1, and during the [dev server upgrade](http://code.google.com/p/redbox-mint/wiki/DevServerUpgradev11) prior to release I noticed this traffic going to the wrong log ('`main.log`' instead). I didn't observe these problems continue through to v1.2 during local testing, but I also didn't run the restore client on the dev server (just my laptop). If you do see this oddity, just let [the mailing list](http://groups.google.com/group/redbox-repo) know and it will be looked into. Nothing breaks... but it is a bug.*
  
 
## []()Custom Upgrades
 
TBD - This is a placeholder for now, but based on community interest I'd like to add some tips & tricks here for what you can do with an upgrade script.