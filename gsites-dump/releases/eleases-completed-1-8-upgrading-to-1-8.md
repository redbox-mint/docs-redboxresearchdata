The 1.8 version of ReDBox has introduced changes to how the application is deployed and managed. The old institutional builds model has been removed and the application now comes pre-packaged with all the supported plugins so that it's much simpler to deploy. Additionally the configuration of the application has been revamped so that future versions of the application will be simpler to upgrade.


Before upgrading, you will need to convert your existing institutional build to the new format. Please [read the documentation for more information on how to upgrade](https://github.com/redbox-mint-contrib/rb-sample-1.8-institutional-build/blob/master/README.md).


Process
1. Download the application tarballs (ReDBox and Mint)
1. Shutdown ReDBox and Mint. 
1. Remove the server/lib and server/plugin directories of ReDBox and Mint
1. Extract the tarballs and copy them over the existing ReDBox and Mint
1. Overlay your changes from your new format institutional build
1. Restart ReDBox and Mint
1. Run the tf_restore.sh (tf_restore.bat in Windows) scripts to migrate the data