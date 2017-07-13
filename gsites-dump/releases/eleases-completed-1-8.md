The 1.8 release of ReDBox and Mint (1.3 Fascinator) introduces significant improvements to functionality and configuration in a number of areas:

1. Revision of institutional builds. A [new institutional build](https://github.com/redbox-mint/redbox-build-distro) has been created that includes all the commonly used and supported ReDBox plugins and can be enabled and disabled via configuration. It also removes the requirement of using Maven as part of institutional builds.
1. Revamped configuration file model that splits system-config.json into smaller more manageable units. This should aid in future upgrades as well as allow them to be edited via the new ReDBox Administrator interface (new tool optional install).
1. [Improved security model (using Spring Security) that allows easy locking down of views by URL pattern.](http://www.redboxresearchdata.com.au/documentation/technical/redbox-view-security)
1. Improved ability to customise the configurable forms. New features include the ability to configure the form layout (e.g. tabbed, wizard or no tabs view), configurable page validation functions (on save, validate just the active tab or the whole form), ability to write custom [validatious functions](https://github.com/cjohansen/validatious) and plug them in via configuration
1. New improved harvester functionality to replace existing harvester and alerts available via new Harvest Manager tool (optional).
1. New database API for writing configuration items. Attributes associated with users are now persisted and can be used by features such as sharing data management plans
1. [Internal Roles plugin now has the ability to set a default role to a user if there is nothing configured for them. ](https://github.com/the-fascinator/the-fascinator/blob/master/plugins/roles/internal/src/main/java/com/googlecode/fascinator/roles/internal/InternalRoles.java)
1. Internal Roles plugin admin interface enhanced so that you can now lookup and add a user based on user attributes. For example, you can now apply a role to an AAF user (via the internal roles plugin) if you are know what their email address is.
1. Improved curation performance via the new Curation Manager service(once curation manager is released)
1. Upgrades of various core libraries

Bug fixes as outlined in:
[ReDBox](https://github.com/redbox-mint/redbox/issues?q=milestone%3A%22Redbox+1.8%22)
[Fascinator](https://github.com/redbox-mint/redbox/issues?q=milestone%3A%22Redbox+1.8%22)


As mentioned in the above list of improvements there are three complimentary tools that can optionally be installed.
### []()Harvest Manager

ReDBox and Mint have long had tools to harvest data into the system but they have the following limitations:

1. Mint harvests are restricted to CSV files from the file system
1. ReDBox harvests (known alerts) are restricted to either CSV or XML (RIF-CS) files from the file system and can only create dataset records.
1. The logging of harvest runs was limited and it was difficult to determine why a harvest failed
1. There was no method to implement business rule validation into a harvest 

The [Harvest Manager](https://github.com/redbox-harvester) is java based tool that utilises the [Spring Integration](http://projects.spring.io/spring-integration/) framework to receive data from a variety of sources (such as a RDMS database, the file system, message queue) perform the necessary transforms to the data and produce a message that can be consumed by ReDBox or Mint to create, update or delete a record.