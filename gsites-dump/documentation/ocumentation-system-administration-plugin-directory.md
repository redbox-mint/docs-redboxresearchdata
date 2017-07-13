[[_TOC_]]


## []()Introduction

ReDBox and Mint are based on a plug-in architecture. A number of plugins are available to assist you in integrating with your institutional system. The sections within this page describe plugins that can be used with your ReDBox or Mint system and advise you as to the level of support to expect. It's important to remember that ReDBox is an open source system and a number of plugins may be developed by different sites, based on their requirements.

* ***Core***: this is considered a central feature and will be available with each release. Those with a [Support](support) agreement are able to log issues against any bugs found in this plug-in.
* ***Stable***: whilst not part of the core system, sites are using the plugin successfully. These plugins are not covered by the [Support](support) agreement 
* ***Developmental***: this plugin has not been fully tested and is made available with no support provisions. The developer may assist sites that encounter problems but there are no guarantees.


In order to use the plugin at your site you'll need to establish an [institutional build](documentation-how-to-institutional-builds). 


For those seeking technical information about the various plugins, please refer to [The Fascinator website](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins) or, for third-party plugins, the link provided against the listing. The "Access" column provides a link to the Maven Central location of the plugin artefacts - for use in your Maven POMs.
### []()Writing a plugin

As ReDBox-Mint is based on The Fascinator platform, all ReDBox plugins use [the platform interfaces](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugin-api). You should get a firm grip of [The Fascinator's architecture](https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/architecture) before you look to write your own plugin.


If you have written a plugin for ReDBox, please let us know through the [Discussion Group](discussion-group).
## []()Harvesters

Harvester plugins [load data into ReDBox and Mint](documentation-system-administration-loading-data).
***Plugin******Status ******Description*** ***Access***CSV harvesterCoreUsed in Mint for loading data such as parties and activities[http://search.maven.org/#browse%7C-1903352839](http://search.maven.org/#browse%7C-1903352839)Workflow harvesterCore Used in ReDBox to manage the user interface forms[http://search.maven.org/#browse%7C1921344832](http://search.maven.org/#browse%7C1921344832)SKOS Harvester Core Used in Mint to load [SKOS](http://www.w3.org/2004/02/skos/)-based information such as the Field of Research codes. This plugin supports a subset of the SKOS model - specifically focussing on hierarchical information[http://search.maven.org/#browse%7C114013757](http://search.maven.org/#browse%7C114013757)1

Note: the [alerts system](documentation-system-administration-administering-redbox-configuring-alerts) loads data into ReDBox via a slightly different model and is considered to be a Core feature.
## []()Transformers

Transformers provide renditions of the harvested information for a variety of uses.


***Plugin******Status ******Description*** ***Access***Handle transformerCoreUsed in ReDBox and Mint to create Handle-based identifiers. See [Handles](documentation-system-overview-integration-with-other-systems-handles).[http://search.maven.org/#browse%7C-1359915570](http://search.maven.org/#browse%7C-1359915570)JSON Velocity transformerCore Used in ReDBox and Mint to create renditions of the metadata in various formats (e.g. RIF-CS). As this plugin uses [Velocity](http://velocity.apache.org/) templates, you can develop templates to suit your needs.[http://search.maven.org/#browse%7C-965963747](http://search.maven.org/#browse%7C-965963747)Local curation transformerCore Used in ReDBox and Mint for the creation of locally defined identifiers. See [Local Curation](documentation-system-administration-integration-local-curation) [http://search.maven.org/#browse%7C-1302226307](http://search.maven.org/#browse%7C-1302226307)Vital-Fedora transformerCore Used by sites wishing to have ReDBox integrated with their VTLS Vital repository. See [VITAL Integration](documentation-system-administration-administering-redbox-vital-integration)[http://search.maven.org/#browse%7C-32031224](http://search.maven.org/#browse%7C-32031224)
## []()Security

A number of plugin types can be grouped under "security":

* Authentication plugins define how a user's login is verified
* Roles plugins are used to list possible roles and indicate the assignment of users to roles
* Access Control plugins define which users/roles can access each object stored in the system.

In most cases, developers are likely to be wanting a new type of Authentication or Role plugin to suit their organisational security infrastructure. Access Control is largely an internal component but is presented as a plugin to allow for future flexibility.
### []()Authentication

***Plugin******Status ******Description******Access***Internal authenticationCoreA basic file-based username/password system [http://search.maven.org/#browse%7C1358302262](http://search.maven.org/#browse%7C1358302262)LDAP authenticationCoreUses an LDAP-based authentication server. Refer also to the LDAP Roles plugin. [http://search.maven.org/#browse%7C755041932](http://search.maven.org/#browse%7C755041932)AAF authentication StableUtilises the [Australian Access Federation](http://www.aaf.edu.au/) for authentication services. Developed by James Cook University.[](-)[https://github.com/jcu-eresearch/fascinator-shibboleth](https://github.com/jcu-eresearch/fascinator-shibboleth)CAS authentication StableProvides [CAS](https://wiki.jasig.org/display/CAS/Home) SSO authentication. Developed by Innodev. [https://github.com/innodev-au/fascinator-authn-cas](https://github.com/innodev-au/fascinator-authn-cas)
### []()Roles

***Plugin******Status ******Description*** ***Access***Internal rolesCoreUses a properties file to allocate users to roles [http://search.maven.org/#browse%7C1887418793](http://search.maven.org/#browse%7C1887418793)LDAP rolesCoreUses roles assigned to users from an LDAP service [http://search.maven.org/#browse%7C378103167](http://search.maven.org/#browse%7C378103167)
### []()Access control

***Plugin******Status ******Description******Access***Derby accessCoreUtilises an in-process Apache Derby database to assign user/role access to objects[http://search.maven.org/#browse%7C-1915203849](http://search.maven.org/#browse%7C-1915203849)
## []()Storage

Objects and their renditions are stored in the storage layer. This is a generic storage layer and may not make full use of the underlying repository's features.


***Plugin******Status ******Description******Access***Filesystem storageCoreUses a filesystem-based tree structure for storing objects[http://search.maven.org/#browse%7C-414186823](http://search.maven.org/#browse%7C-414186823)Fedora Commons 3.x storageCoreUses the [Fedora Commons](http://fedora-commons.org/) repository for storing objects[http://search.maven.org/#browse%7C-113154656](http://search.maven.org/#browse%7C-113154656)
## []()Subscriber

Subscriber plugins can listen to changes in the system and handle them as needed. Useful for logging and notifications.


***Plugin******Status ******Description******Access***Solr event log subscriberCoreCreates an event log in a Solr index[http://search.maven.org/#browse%7C1331801079](http://search.maven.org/#browse%7C1331801079)
## []()Indexer

The Apache Solr indexer plugin is the only one available in the system. Whilst fashioned as a plugin, development of various interfaces increasingly made use of the Solr API. Whilst you can create a new indexer plugin please be aware that this is likely to require a range of development work to bring the system into line.