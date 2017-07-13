**Interested parties** **Primary contact** **Release planned?**  **Action items**

*  



[[_TOC_]]



## []()Overview

ReDBox and Mint are designed to integrate with existing institutional systems. This is a broad feature descriptor aimed at capturing requirements across many types of systems. Substantial feature development aimed at a specific platform may be described within a separate feature descriptor.


At the [ReDBox Community Day](governance-redbox-community-days-ReDBox-Community-Day-1-2012) the following numbers were determined regarding system usage:

* Research management systems:

 * ResearchMaster: 11
 * Callisto: 1
 * In hous: 2
* HR Systems:

 * Talent2 Alesco: 4
 * Peoplesoft: 2
* Institutional repository:

 * VITAL: 6
 * DSpace: 4
 * ePrints: 1

## []()Functional requirements


* Database harvest:

 * Rather than harvesting a CSV/XML file, work could be undertaken to create an SQL Harvester
* Normalisation of feeds:

 * Data is often drawn from a variety of systems and require cleaning before import
 * The harvester rules file and transformers can undertake some aspects of data cleaning/munging
 * The [ALA Filing Rules](http://www.ala.org/alcts/resources/org/cat/alafilingrules) may provide a guideline for this work
* Alert scenarios:

 * A number of triggers should alert ReDBox/Mint:

  * Research project starting/completing
  * Someone (researcher) leaving
  * Resources allocated to a project: e.g. research storage
* Submission interfaces:

 * An OAI-PMH harvester exists but other options might be considered:

  * [RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer)
  * [ATOM Publishing](http://en.wikipedia.org/wiki/AtomPub)
  * [SWORD](http://en.wikipedia.org/wiki/SWORD_(Protocol))
  * [Web Services](http://en.wikipedia.org/wiki/Web_service)
 * The ReDBox/Mint queue interface is useful for integration

## []()Design



## []()Resources


* ReDBox and Mint: [Loading data](documentation-system-administration-loading-data)

* Description of ingest architecture:  [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain/basics](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain/basics)
* Description of object life-cycle:  [https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/object-life-cycle](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/object-life-cycle)
* Harvester plug-ins:  [https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/common-library/plugins/harvester](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/harvester)