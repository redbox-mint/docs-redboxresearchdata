[[_TOC_]]



## []()System architecture

As ReDBox and Mint are built on [The Fascinator platform](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/architecture) , they both have a plug-in based, flexible architecture. This architecture is based on the following processes:

* **Harvests **gather together information from sources such as web-based forms (ReDBox) and file-based ingest from formats such as XML and CSV (comma separated) files. Harvests can be configured and/or scripted to provide access to new data sources and harvester plug-ins can be added for new types of data.
* **Transformers **are used to manipulate or supplement the data gathered during a harvest. A common example in Mint and ReDBox is the creation of alternate metadata renditions - for example, a CSV entry may be transformed into dublin core and RIF-CS formats. Transformer plug-ins can be configured to customise transformations and new transformer plug-ins can be installed/developed.
* The **Storage **layer provides the  data storage facility. The two main storage layer plug-ins are a file system storage engine and Fedora Commons. 
* An Apache Solr **Index **provides search facilities
* **Subscribers **can monitor the system and react to events.

ReDBox and Mint are pre-configured with a good set of default plug-ins that meet most needs. However, a number of plug-ins are available to help in local customisation. For the most part, the terms described above provide an insight into the terms used when describing how ReDBox and Mint function. For those wanting further technical information, please see The Fascinator's[ tool chain documents](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain/basics).
## []()Views

A View is basically a saved search. Using search pre-defined parameters, ReDBox and Mint views allows users to search a specific subset of data. The list of default Mint views in the screenshot below indicates that users could choose to explore specific subsets such as Parties-People or Funding Bodies. Views can easily be added or modified in ReDBox and Mint, allowing for targeted and individually branded interfaces.


Views extend beyond the user interface and provide the same pre-defined search parameters to the OAI-PMH interface and the Mint query interface used by ReDBox forms.



[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216863559/documentation/system-overview/Mint-ViewMenu.png)
](documentation-system-overview-Mint-ViewMenu-png?attredirects=0)
Screen shot: Mint views menu

## []()Skins

The ReDBox and Mint interfaces can be altered to meet local branding requirements through the creation of skins. Using a system of cascading templates, these skins can selectively or completely replace the user interface. Skins can be applied to the entire system or to specific views as well as defining how different types of information are displayed in search results and details pages.


This concept is discussed more fully in [Branding](documentation-system-administration-general-administration-branding)