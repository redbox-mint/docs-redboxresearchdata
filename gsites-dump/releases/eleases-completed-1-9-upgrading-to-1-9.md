**Note**


While upgrading ReDBox and Mint, you may wish to consider your Java runtime environment to the latest version 8. While ReDBox 1.9 will run on previous versions of the JVM, upgrading the JRE will ensure full support of the platform and future versions of ReDBox may take advantage of the latest Java features.


New to the upgrade process in 1.9 is the inclusion of an upgrade script (tf_upgrade) to check the configuration of the application and to optionally enable new features. Note that if you are currently running version 1.6.2 or lower, you must first upgrade to 1.8. Please see here for instructions.
**

**

**Process**

1. Download the application tarballs (ReDBox and Mint)
1. Shutdown ReDBox and Mint. 
1. Remove the server/lib and server/plugin directories of ReDBox and Mint
1. Extract the tarballs and copy them over the existing ReDBox and Mint
1. Overlay your changes from your new format institutional build
1. Run the tf_upgrade.sh (tf_upgrade.bat in Windows) script to check your config and make any changes needed. More information on upgrade script for 1.9 is available here.
1. Restart ReDBox and Mint
1. Run the tf_restore.sh (tf_restore.bat in Windows) scripts to migrate the data (This script only needs to be run in ReDBox as there are no schema changes to Mint)