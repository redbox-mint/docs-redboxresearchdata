[[_TOC_]]


## []()


## []()Status

This document is in DRAFT.
## []()Introduction

The ReDBox/Mint Nectar image has been designed to get a basic instance of ReDBox and Mint up and running for evaluation purposes.  The image uses builds from the [mint-build-nectar-local-curation](https://github.com/redbox-mint/mint-build-nectar-local-curation) and [redbox-build-nectar-local-curation](https://github.com/redbox-mint/redbox-build-nectar-local-curation) github projects. It's intended to be a turnkey installation in that once the image has started up, you have working instances of the applications. However it is likely that you will want to customise your ReDBox and Mint instances in which case, you will need to set up [institutional builds](documentation-how-to-institutional-builds).
## []()Background reading

If you are not familiar with NeCTAR it may be worth having a read through their [support documentation](http://support.rc.nectar.org.au/). 

## []()Launching a Virtual Machine

For general documentation on how to launch a virtual machine. Please see the documentation on the [NeCTAR support site](http://support.rc.nectar.org.au/launching_a_virtual_machine.html). The latest image is named ReDBox Cloud 1.5.2.2 and can be found in the "Instance Snapshots" section. (Note: that the HTTP and SSH Security groups should be enabled)



## []()Accessing ReDBox and Mint

Once your virtual machine has finished starting up, simply go to your web browser and enter the machine's IP address (can be found in the instances section on the dashboard). You should be taken to a landing page with links to both ReDBox and Mint.



[![Screenshot of the Landing page](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1353984949526/documentation/how-to/early-drafts/working-with-the-nectar-image/NeCTAR%20landing%20page.png)
](documentation-how-to-early-drafts-working-with-the-nectar-image-NeCTAR%20landing%20page-png?attredirects=0)



## []()Shell Access to the Virtual Machine

Shell access is possible via SSH using the key pair you selected when launching the virtual machine. The username for this image is "ubuntu". For more info, please see the [NeCTAR support site](http://support.rc.nectar.org.au/demos/communicating_with_vms.html).

## []()Upgrading your NeCTAR instances of ReDBox and Mint

As new versions of the ReDBox and Mint core are released, the ReDBox team will also release new versions of the NeCTAR institutional builds ([ReDBox](https://github.com/redbox-mint/redbox-build-nectar-local-curation) and [Mint](https://github.com/redbox-mint/mint-build-nectar-local-curation)) as well as a new NeCTAR image. If you are already using a previous release of the image and would like to use the latest versions of the software, there are some upgrade scripts in /home/ubuntu/upgradeScripts that you can use.


These scripts:

1. Backup your old installation to /var/backups
1. Download the latest release from the [ReDBox Nectar repository](http://dev.redboxresearchdata.com.au/nexus/index.html) and extract it into the installation direct
1. Shutdown and restart the application
1. Run the data migration script for the new release

To upgrade ReDBox and Mint simply run:

* sudo ./upgradeMint.sh
* sudo ./upgradeRedbox.sh