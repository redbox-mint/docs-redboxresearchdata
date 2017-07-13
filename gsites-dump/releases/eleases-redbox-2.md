Introduction

ReDBox 2 represents a review and renewal of the ReDBox system. The program is driven by a number of things:
1. A lot has changed since the program started in late 2010:
 1. New initiatives have started such as [ORCID](https://qcifltd.atlassian.net/wiki/display/REDBOX/ORCID) and [ISNI](https://qcifltd.atlassian.net/wiki/display/REDBOX/ISNI)
 1. Standards such as [RIF-CS](https://qcifltd.atlassian.net/wiki/display/REDBOX/RIF-CS) have been improved upon

1. We've also established a broader user base (from 1 to 12) in a brief amount of time:
 1. We've seen where parts of the system have stretched/broken
 1. As different groups have brought the system into their environment we've seen where they've had issues around integration,  and skills.


## []()Big-ticket items

There are a few areas that we know cause annoyance to our users and resolving them
* Merge ReDBox and Mint into the one system
 * There were reasons (long ago) that saw them stand alone - these reasons don't really exist anymore
 * The underlying platform is the same

* Deliver a new harvest client
 * This will replace both the harvester system (used in Mint) and alerts system (used in ReDBox)
 * Key goals:
  * Improve error handling on incoming data
  * Improve the range of information sources that can be harvested
  * Improve the configuration of the harvester


* Completely rewrite the curation sub-system
 * Curation of a large network of relationships just fails. 
 * We want to create a more effective curation sub-system that also provides better levels of feedback

* Standardise the technology mix used in the application stack so that customizing the application to your needs is simpler.
* Make administration and upgrading of the system more user friendly
* Renew the dataset forms
 * The current review forms use an older layout system that's difficult to maintain - it's time to drop that and move to the system created for Data Management Plans
 * The forms themselves are in need of review



## []()Delivery

The elements described under the ReDBox 2 plan are quite broad and involve a substantial amount of work. We prefer to take bite-sized chunks so will take an approach that allows for
1. Delivering components that can be used in ReDBox 1 and 2
1. A safer and more trouble-free upgrade process

The following roadmap has been proposed to meet the afforementioned requirements:
### []()Version 1.8 

* New curation manger 
* New harvest client
* New administration application to allow for configuration via web interface
* Simplification of the institutional builds concept by removing the requirement of using Maven


### []()Version 1.9 

* Merge ReDBox and Mint into the one application


### []()Version 1.10 


* Implement a fully featured API to improve the capabilities of integration with external systems as well as the potential to easily create custom portals.


### []()Version 2 

* Re-working of back-end system (technology stack to be devised)