[[_TOC_]]



## []()Introduction
This documentation will describe how you can change the look of your Mint and ReDBox user interface.



The following technologies are used by ReDBox/Mint to present the user interface:

* The[ Apache Velocity](http://velocity.apache.org/) template engine
* Cascading Style Sheets (CSS)
* HTML

Specifically relating to the data entry forms, there is also a [document regarding lookups](documentation-technical-redbox-mint-lookups). This is a slightly more complicated topic, but the linked document contains quite detailed information for developers and web editors.

## []()File locations

Before you start editing any files, it's important to understand how the system is laid out. The `portal `sub-directory of your installation contains most of the files you'll need.


Open your ReDBox installation directory and then the `portal `sub-directory. You will see a number of objects:

* default: This is the default view 
* packages: This is a view of all packages - a model used by ReDBox for grouping items (and not overly useful for this discussion)
* published: This is a view of all published items - its main use is in ReDBox
* portal-library.vm: Contains a library of Velocity macros for use in templates

You may see several other directories as each view that you configure will create a directory. For example, Mint has a Parties-People directory and this contains the configuration for that view.


The `home/system-config.json` file contains the configuration for the portal and the skins it will use. Open this file and have a look for the following segment:



        "portal": {
                "home": "${portal.home}",
                "contentDir": "${fascinator.home}/content",
                "defaultView": "default",
                "skins": {
                    "default": "default",
                    "order": [ "local", "mint" ]
                },
In the code above you should notice the following:
* The "defaultView" is set to "default"
* Within the "skins" section:

 * The "default" skin is called "default"
 * Two other skins also exist: "local" and "mint"

This configuration basically tells us that there is a "default" skin that will be used as the basis for the user interface. If no other skins can be found, the system will drop back to the "default" skin. 


Skins are layered over each other using the "order" directive. In the configuration above, the "local" skin will applied over a "mint" skin which sits over the "default" skin. This means that the system should always have a skin to fall back on - this avoids parts of the interface from going missing. 


Whilst we're in the `system-config.json` file, it'd be handy to turn off caching whilst we try things out. That way, all of our changes will be picked up without having to restart. There's only two options to change:

* `scriptObjects.profile`
* `pathLookup.profile`

The table below illustrates these changes:


***Original******Change***


        "caching": {
          "caches": {
            "scriptObjects": {
               "label": "Cache for Jython scripts",
               ***"profile": "dynamic",***

               "lastModifiedCheck": true
             },
             "pathLookup": {
               "label": "Cache for path lookups",
               ***"profile": "full"***

             }
           },
        ...

        "caching": {
          "caches": {
            "scriptObjects": {
               "label": "Cache for Jython scripts",
              * **"profile": "off",***

               "lastModifiedCheck": true
             },
             "pathLookup": {
               "label": "Cache for path lookups",
               ***"profile": "off"***

             }
           },
        ...


Just don't forget to re-enable caching for your production system!
## []()Changing the user interface

Open the  `portal/``default `directory and you'll see three directories, matching the entries in `system-config.json`:

* `default`
* `mint`
* `local`

We suggest that you avoid changing the contents of the `default `and `mint `skin directories - for the most part you should be editing the `local `skin. Another skin called "`usq`" is provided as a sample skin.
## []()Branding guides 


![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BPage+listing'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D297'dim'%5C%3D10'%3D10'%3D500'%3D297'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=x5z8H4DplZUYp7X4J5EY798sKFM)



## []()Further reading

The method of managing skins is part of The Fascinator platform: 

* Branding: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/branding-appearance](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/branding-appearance)
* Object display: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/branding-appearance/object-display](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/branding-appearance/object-display)

If you're wanting to know about bindings: [http://code.google.com/p/the-fascinator/source/browse/the-fascinator/trunk/portal/src/main/java/com/googlecode/fascinator/portal/services/impl/CachingDynamicPageServiceImpl.java](http://code.google.com/p/the-fascinator/source/browse/the-fascinator/trunk/portal/src/main/java/com/googlecode/fascinator/portal/services/impl/CachingDynamicPageServiceImpl.java)