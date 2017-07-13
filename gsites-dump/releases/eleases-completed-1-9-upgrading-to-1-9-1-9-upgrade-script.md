This page is to walk through the execution of the upgrade script, explaining what it does at each step and providing information (links to other documentation) that describes it in more detail.
## []()Purpose

The server file, tf_upgrade.sh is an upgrade configuration script, run on the command-line, which runs through some basic Q and A for the user to answer about specific configurations for redbox or mint. 
As the script steps through prompts for the user, it backs up the current configuration files affected by the script to another directory (e.g, <fascinator-home>/pre-upgrade-backup). The questions are either yes/no or short answers for the user to provide.
## []()Running the upgrade

The upgrade script is executed in a similar manner to tf_restore.sh:
1. 
Change into the server directory in your redbox installation. e.g.,```
cd /opt/redbox/server
```

1. 
Ensure that your redbox installation is shutdown (and afterwards check that the server is no longer running):```
sudo -u redbox ./tf_server stop
```

1. 
Find and execute the script,  tf_upgrade.sh, as the redbox installation user:```
sudo -u redbox ./tf_upgrade.sh
```

In the example, above the redbox user is redbox



If the script is executed correctly, you should now see some command line prompts for you to enter details about your installation upgrade.
## []()Redbox upgrade process

Currently, the redbox upgrade configuration script will step through the following prompts:
### []()Verifies correct upgrade version

* 
 * The script checks that the server library version matches the version named in the upgrade script (e.g, redbox-1.9-SNAPSHOT.pom).
 * If there is no match, the script will display a message that the library version is incorrect and the script will exit.
 * The script will also prompt to upgrade the version recorded by the server - ensure this is also matched.


### []()Checks if changes have been made to the dataset configuration templates

* 
 * If so, no updates are made to the configuration, with a simple message that these should be reviewed.


 * If not, the upgrade script proceeds to update the configuration, which currently checks:


#### **[]()Curation relations which should be excluded

* 
 * 
  * (see [Curation exclude conditions](documentation-system-overview-curating-linked-data-curation-configuration) for details of the new configuration)
  * Previously, identifiers could only be excluded individually. From the 1.9 upgrade, redbox can now also exclude all relations except for that identifer which is nominated.
  * The script steps here are:
   * exclusion conditions for the parties/groups relation identifier prefix that start with are found (e.g., nla identifier prefixes)
   * the user is prompted for the mint identifier prefix
   * the exclusion conditions identified are replaced such that any identifier that doesn't start with the mint identifier prefix are excluded from curation. In this way, redbox will ignore other parties/groups identifiers such as NLA and ORCID, during curation.




#### **[]()Record as location

* 
 * 
  * The user can choose to have a record's URL included as a rif-cs location, using the template named in the upgrade script (e.g, ${urlBase}published/detail/${oid} ) (see [recordAsLocationDefault](documentation-system-administration-administering-redbox-form-fields-collection-form-fields) collection form field)





### []()Configure the new rifcs transformer

* 
 * The user can choose to use:
  *  the existing velocity template  (see [Metadata templates](documentation-system-administration-general-administration-metadata-templates) ), or
  * the new groovy script (see **[Rifcs scripting transformer](documentation-system-administration-general-administration-metadata-templates-script-transformers-1))**

to write the rifcs records.
 * By answering yes, the script will set up the configuration required to use the new rifcs transformer, over the rifcs template. 
 * The script will also enable harvest remapping on restore, to ensure that the rifcs transformer is applied when running the restore/migration process, following this upgrade script.
**

**



**Note: At the 1.9 release, both the rifcs velocity template and the rifcs groovy script are compatible with rif-cs version 1.6.1. **

**However, in the future (TBA), the rifcs velocity template will become deprecated, with revisions made only to the groovy script.**



### []()Create the api user configuration

* 
 * If it doesn't already exist, the upgrade process adds empty api keys config, for user to add their api keys later. **Andrew to link**

**

**



### []()Exit

* 
 * 
Exits with message showing the location of the previous configuration backup (e.g., <fascinator-home>/pre-upgrade-backup) for rollback purposes if needed.


## []()Mint upgrade process

Currently, the mint upgrade configuration script will step through the following prompts:
### []()Verifies correct upgrade version

* 
 * The script checks that the server library version matches the version named in the upgrade script (e.g, redbox-1.9-SNAPSHOT.pom).
 * If there is no match, the script will display a message that the library version is incorrect and the script will exit.
 * The script will also prompt to upgrade the version recorded by the server - ensure this is also matched.


## []()Technical Details

It is not necessary to understand the high-level technical detail by which the upgrade process runs. It is provided here merely as a quick technical reference for those who wish to modify the upgrade according to their institutional requirements.
* In running the upgrade, in a similar manner to tf_restore.sh. That is, the tf_upgrade.sh does not execute any upgrade logic directly, but rather looks for (on the java classpath) and executes the fascinator java class, UpgradeClient.class. To find this class, the script must be executed within the redbox installation server folder in order to find the correct class within server/lib.
* The UpgradeClient class then binds itself (for use of any utility methods) to a groovy script, upgrade.groovy. It is this groovy script that contains the explicit upgrade instructions and can be found under home/upgrade/upgrade.groovy
* As a groovy script, upgrade.groovy does not require explicit compilation - it can be modified, and the tf_upgrade.sh script executed immediately from the server folder, in order to process the changes 
* (for those new to working with groovy, check the redbox installation version number of the groovy-all  java archive and find the correct groovy version documentation [here](http://www.groovy-lang.org/documentation.html)).

 
## []()Troubleshooting

* I get an error message : 
Could not find or load main class com.googlecode.fascinator.UpgradeClient
 * 
Usually this means you are not executing the script from your redbox installation's server folder. You must change into this folder, in order for the shell script to find the correct class under the  server/libfolder.

* I execute the upgrade script OK, but on running the tf_restore.sh, it doesn't seem like the upgrade changes have been applied.
 * Ensure that the redbox server is not running before you execute tf_upgrade.sh. Then once you have answered all the prompts and the script is finished, start the redbox server and then run ./tf_restore.sh, once you have confirmed that the redbox server is up by, say, loading redbox in your browser. 

* I start the script, but I get the message: "The ReDBox installation does not appear to be the correct version. Please ensure you have deployed the latest distribution.", and the script stops.
 * The upgrade process needs to match its version of the redbox configuration parent file against what actually exists in the redbox installations' server library (Look for a pom file, named something like redbox-<version number>.pom).
 * To solve this problem you may need a technical member of your staff to verify the following:
  * Check for a pom file for redbox, such as redbox-<version number>.pom in your server/lib folder. (e.g., redbox-1.9.pom). 
  * Then check this file against the groovy script, home/upgrade/upgrade.groovy. The file should match the file named under the method, verifyCorrectUpgradeVersion().

 * If the server/lib folder has the incorrect pom version, then chances are the entire server library has the wrong installation version. You may need to re-install the new distribution.
 * If the upgrade.groovy script has incorrectly named the pom version, simply change the name to match the version in your server/lib folder, and start the upgrade process from the beginning (see Running the upgrade above)