### []()1. Obtain the RIF-CS metadata

You'll need to get the metadata either via the ANDS ORCA system or from an OAI-PMH feed. The first option is easiest: (*thanks to Grant from Flinders for providing the details below*)



Assumptions:
* You have Data Source Admin rights to the ANDS Collection registry with AAF authorisation.
* ANDS Online Services (ie. ANDS Collections Registry) Release 8.1
* Firefox 3.6.23 web browser under Windows 7 Enterprise SP1.

Procedure:

1. Use your web browser to navigate to [https://researchdata.ands.org.au/registry/login.php](https://researchdata.ands.org.au/registry/login.php)
1. Login using AAF; Select your home institution then click Select; Type your username and password. You should now be logged into ANDS Online Services.
1. In the left pane, click "Export from Data Source".
1. In the first "Data Source Export Tool" form, complete appropriately for your institution (eg. see screen shot) then click "Export Data Source".
1. In the second "Data Source Export Tool" form, click "Download as XML"; save the file to your local workstation.
1. In the left pane, click "Logout".


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1346024467646/config/pagetemplates/how-to/orcaR8.1dsexport_c.png)
](config-pagetemplates-how-to-orcaR8-1dsexport_c-png?attredirects=0)

### []()2. Break up the RIF-CS file

The downloaded file will contain several collection records so you'll need to break it up into 1 collection per file. The python script below should do this for you (but you'll want to check it first). You'll also notice that the file will break out the different RIF-CS object types - this is handy if you have a mix in the XML file. Don't forget that you only want to import COLLECTIONS into ReDBox.


A utility script for splitting up the file is provided at https://github.com/redbox-mint-contrib/config-samples/blob/master/util/rifsplit.py: 

* run it using "python rifsplit.py <xml file>". 
* I will assume you have python 2.7 (ish) installed and have read the code.

Once you've run the script across your XML export you should have a series of files in the "output/" directory. Each filename is prefixed with the object type and you only need those with "collection_".