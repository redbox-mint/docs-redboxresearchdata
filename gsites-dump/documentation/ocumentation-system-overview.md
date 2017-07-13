[[_TOC_]]


 
 
## []()Introduction
 
The ReDBox (Research Data Box) system provides organisations with the ability to describe research data and make these descriptions (metadata) available to national/global registers. These registers allow researchers from around the world to locate and access research data. Primarily a research data registry, ReDBox provides workflows and interfaces for the creation of metadata. It is assumed that the actual research data (which can be very large) is hosted via another service. However, ReDBox does allow for the upload of data as well as supporting evidence files (e.g licences or IP statements). 
Mint is a name authority and vocabulary system that provides services to web applications (such as ReDBox). The benefit of using a name authority is in the broad identification of people, groups, projects, services, etc. By providing a [linked data](http://en.wikipedia.org/wiki/Linked_Data) approach to identification, Mint helps make your identifiers globally unique -  a must for sharing information. 
In Figure 1 you can see how the two systems interact. Using a variety of data inputs, Mint provides a set of search interfaces to ReDBox. ReDBox's forms capture a range of metadata and utilise Mint as a source for identifying information. Each system provides an [OAI-PMH](http://www.openarchives.org/pmh/) interface that can be configured to provide a variety of metadata formats to organisations such as the [Australian National Data Service](http://www.ands.org.au) and the [National Library of Australia](http://trove.nla.gov.au/general/aboutPeople).  
 
 
[![ReDBox-Mint architecture, displaying various links between the systems](https://docs.google.com/drawings/pub?id=1_4RZIr-wi7hn8NOTgVicE2OWRX1VyhBqHO27ochZaBI&w=1530&h=911)
](https://docs.google.com/drawings/pub?id=1_4RZIr-wi7hn8NOTgVicE2OWRX1VyhBqHO27ochZaBI&w=1530&h=911)  Figure 1: The ReDBox and Mint systems
 Please click on the image for a larger version 
Both systems have been developed so that you are able to customise interfaces and metadata formats. They're also both Open Source so you can even roll up your sleeves and get under the hood. 
## [Mint]()
 
Mint provides services for uploading name authority information and an index system for making this data easily available to web-based user interfaces (such as ReDBox). Instead of providing one large search set, Mint uses Views to partition your data - meaning that (for example) you only get researcher names returned for user selection. The Mint system uses a very simple [AJAX-friendly](http://en.wikipedia.org/wiki/Ajax_(programming)) search interface that returns [JSON ](http://en.wikipedia.org/wiki/Json)for easy presentation in a web browser. 
Most organisations will have information such as researcher details, project information and grant details locked up in their HR or research management software. Mint can read data in comma-separated files - a common export format that's software-neutral. Mint also has a number of common standard vocabularies ready to upload as you need them. 
The [Geonames ](http://www.geonames.org/)relay provides a local "cache" of the extensive Geonames location data. This is a specially tuned system that can deliver Geoname data to your user interfaces in a rapid manner that avoids the latency of the main Geonames service. 
[![](https://docs.google.com/drawings/pub?id=1MvhrPljo7ONFh3m-gH-_ZIjcob-d1cTPrx2l1uwwecU&w=364&h=296)
](https://docs.google.com/drawings/pub?id=1MvhrPljo7ONFh3m-gH-_ZIjcob-d1cTPrx2l1uwwecU&w=364&h=296)
 
 
Figure 2: Mint 
Please click on the image for a larger version  
The Mint system is configurable - you can add your own upload formats and provide views for use by ReDBox as well as your other systems. . 
### []()Mint screenshots
    
[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216863597/documentation/system-overview/Mint%20-%20Screen.png)
](documentation-system-overview-Mint%20-%20Screen-png?attredirects=0)  
[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216863522/documentation/system-overview/Mint-SearchResults.png)
](documentation-system-overview-Mint-SearchResults-png?attredirects=0)   

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216353658/documentation/system-overview/Mint-DetailScreenanddatastreams.png)
](documentation-system-overview-Mint-DetailScreenanddatastreams-png?attredirects=0)    Mint home screen   Mint search results Mint record screen     
## [ReDBox]()
 
ReDBox (Research Data Box) provides the workflow and user interfaces for the entry and management of research data descriptors. 
Using a filesystem storage mechanism by default, ReDBox can help alleviate the need for installing database servers or other "heavier" storage systems. However, if [Fedora Commons](http://fedora-commons.org/) is more your thing, the system can be configured for Fedora 3. 
 
 
[![ReDBox Diagram](https://docs.google.com/drawings/pub?id=1pS84RsPC83nS3dhkPtzdxUYRDfpZHcyWIPktWaeWUIE&w=593&h=625)
](https://docs.google.com/drawings/pub?id=1pS84RsPC83nS3dhkPtzdxUYRDfpZHcyWIPktWaeWUIE&w=593&h=625) 
  
Figure 3: ReDBox 
Please click on the image for a larger version  

  
### []()ReDBox Screenshots
 
    [![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216908443/documentation/system-overview/ReDBox-ReviewerHome.png)
](documentation-system-overview-ReDBox-ReviewerHome-png?attredirects=0)  
![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216908392/documentation/system-overview/ReDBox-Form.png)
   
[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320216908284/documentation/system-overview/ReDBox-DetailScreenanddatastreams.png)
](documentation-system-overview-ReDBox-DetailScreenanddatastreams-png?attredirects=0)
     ReDBox management home ReDBox user form   ReDBox details screen

    
  
## []()System concepts 

A page describing various[ ReDBox and Mint concepts](documentation-system-overview-system-concepts) is available.  
## []()Curating linked data
 
Both ReDBox and Mint understand the importance of correctly aligning metadata to ensure that the network of relationships between datasets, people, groups, activities etc can be correctly represented. The [Curating Linked Data](documentation-system-overview-curating-linked-data) provides an overview of how this is achieved. 
## []()Integration
 
ReDBox and Mint have been designed to make integration with existing systems possible - the [Integration with other systems](documentation-system-overview-integration-with-other-systems) section provides an overview of existing integrations. 
## []()ReDBox and Mint development
 
Both ReDBox and Mint are built on The Fascinator platform so they share a common basis in terms of configuration and system architecture - this makes on-going maintenance that little bit easier 

  
For those interested in how ReDBox and Mint are developed, the following points are likely to be of interest: 
 
* ReDBox and Mint are both covered by open source licenses (GPL v2)
 * All source code is available via our developer site. Refer to the [Development](development) page for more information
 * The system is Java-based and will operate on most modern server operating systems
 * Both systems are applications of [The Fascinator](https://sites.google.com/site/fascinatorhome/), an open-source repository platform
 * [Apache Maven](http://maven.apache.org/) is used to manage the development environment and make it easier to get development going
 
 
 
  
 
***

  
![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BPage+listing'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Subpage+Listing%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D297'dim'%5C%3D10'%3D10'%3D500'%3D297'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=lxEPbO98PQKCTIgDHMqrwU0hJD8)
 
## []()