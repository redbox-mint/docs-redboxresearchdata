[[_TOC_]]


## []()Status

This document is in DRAFT.
## []()Introduction

Redbox/Mint development involves working with a number of technologies and associated libraries. Setting up your IDE correctly will aid you in being as productive as possible. This How-to describes setting up the [Eclipse IDE](http://eclipse.org/). This is by no means your only option, [Netbeans ](http://netbeans.org/) for example is used by a number of developers in the community and may be more suitable to you. If you are like me and are more familiar with working with Eclipse then this may help you. 
Note: This How-to assumes you have already your source code checked out.
## []()Background reading

If you have never worked with Eclipse before you may like to read the help inside the application by hitting F1.
## []()Procedure

For Windows users, I have zipped up my Eclipse installation with all the required plugins already installed. You can download this version, unzip it to a directory of your choice, start the application up and commence from Step 4.
### []()1. Step 1 Download Eclipse and Install

You can get the latest version from [Eclipse.org](http://eclipse.org/downloads/). Once you've downloaded your package follow the [installation instructions](http://wiki.eclipse.org/Eclipse/Installation). There are a number of editions you can download from Eclipse.org, the one I have used is Eclipse Classic but there may be a flavour you are more familiar with. If you want something with a more web development focus then consider [Aptana](http://aptana.org/) which includes some of the plugins we will install and many others that may or may not be relevant to our project.

### []()2. Step 2 Install M2Eclipse

M2Eclipse is the maven plugin for Eclipse. It handles dependency resolution (though not always perfectly more on that later) and the project lifecycle within Eclipse. To install go to Help > Install New Software and put [http://download.eclipse.org/technology/m2e/releases/](http://download.eclipse.org/technology/m2e/releases/) into the Work With field.



[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347511230975/documentation/how-to/how-to-setup-your-ide-eclipse/m2eclipse_installation.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-m2eclipse_installation-png?attredirects=0) 
Check the m2e plugin checkboxes, click Next > and follow the wizard through to install the plugins.



### []()3. Step 3 Install the other plugins

Follow the instructions in step 2 but with the following Eclipse update sites. I've marked some as optional as I find them useful but aren't critical to work with the code base:
 Plugin          Eclipse Update Url [Pydev](http://pydev.org/) http://pydev.org/updates [Egit](http://www.eclipse.org/egit) http://download.eclipse.org/egit/updates/ Subclipse (Optional) http://subclipse.tigris.org/update_1.8.x[Veloeclipse](http://code.google.com/p/veloeclipse/) (Optional) http://veloeclipse.googlecode.com/svn/trunk/update/ 


### []()3. Step 3 Import your institutional build

Right Click on the Package Explorer and Select Import...

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347519540658/documentation/how-to/how-to-setup-your-ide-eclipse/import%20project.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-import%20project-png?attredirects=0)


Select Existing Maven Projects

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347519771870/documentation/how-to/how-to-setup-your-ide-eclipse/maven%20existing%20import.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-maven%20existing%20import-png?attredirects=0)
Fill in the root directory field with the path to your project(s) and check the one(s) that you would like to import and then click Finish

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347519991930/documentation/how-to/how-to-setup-your-ide-eclipse/maven%20import%20select.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-maven%20import%20select-png?attredirects=0)


After some processing you should see your project imported in the Package Explorer

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347520152916/documentation/how-to/how-to-setup-your-ide-eclipse/Imported%20project.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Imported%20project-png?attredirects=0)



### []()4. Step 4 Configure PyDev

Once all the required plugins and your institutional build is installed, we need to configure PyDev. Go to Window > Preferences, select the Interpreter - Jython in the tree in the left pane and click New...


A dialog will pop up that will allow you to select your jython interpreter. Put in the location of your jython.jar and click OK. (Note it has been reported that in some versions of eclipse you may need to double click the jython.jar when you select it for it to work.)

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347588128702/documentation/how-to/how-to-setup-your-ide-eclipse/Select%20Interpreter%20Jython.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Select%20Interpreter%20Jython-png?attredirects=0)



Click OK to accept the defaults on the Python Path Dialog that pops up and you should see the following:

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347588144432/documentation/how-to/how-to-setup-your-ide-eclipse/Jython%20interpreter%20configured.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Jython%20interpreter%20configured-png?attredirects=0)


Next we need to tell Pydev that our institutional build is a Jython project. 
Right click on the institutional build project to bring up the context menu and select Pydev > Set as PyDev project.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347597942025/documentation/how-to/how-to-setup-your-ide-eclipse/Set%20PyDev%20Project.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Set%20PyDev%20Project-png?attredirects=0)

PyDev by default assumes that we have a Python Interpreter installed and configured (which if you are following this tutorial you won't). Simply click the Don't ask again button.



[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347598209829/documentation/how-to/how-to-setup-your-ide-eclipse/Python%20not%20configured.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Python%20not%20configured-png?attredirects=0)

PyDev by default uses the Python 2.7 grammar so we need to now tell it we are using Jython 2.5. Right click again on the Institutional Build project and this time select Properties to bring up the Project Properties Page.
Select PyDev - Interpeter/Grammar from the tree in the left pane, select Jython as the project type, Grammar Version as 2.5 and leave the Interpreter as Default



[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347598749254/documentation/how-to/how-to-setup-your-ide-eclipse/Jython%20configure%20the%20project%20interpreter.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Jython%20configure%20the%20project%20interpreter-png?attredirects=0)


That should be it! Open up a python file and make sure it can interpret the file correctly



[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347600150844/documentation/how-to/how-to-setup-your-ide-eclipse/Python%20file.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-Python%20file-png?attredirects=0)



## []()Optional Extras

### []()1. Importing core projects(optional)

When working on an institutional implementation. you may need to do no more that the procedure above. However, you may want to implement a core project into Eclipse to fix an issue you find in core or simply to browse the source code a little easier when trying to get understanding of some functionality (source is available with our jars so this isn't 100% necessary).
The procedure to import core projects is no different Step 3 Import your Institutional Build. The main thing to point out here is when developing, if you change your dependency version in your institutional build to match the core projects version, M2Eclipse will put a reference to the core project in your institutional build project.

[

![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347602582428/documentation/how-to/how-to-setup-your-ide-eclipse/dep%20list%20pt1.png)
](documentation-how-to-how-to-setup-your-ide-eclipse-dep%20list%20pt1-png?attredirects=0)![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1347602607717/documentation/how-to/how-to-setup-your-ide-eclipse/dep%20list%20pt2.png)









### []()Notes


* 


### []()Finishing up