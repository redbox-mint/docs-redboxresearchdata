Please note that most annotations (issue numbers and source versions) in the document below relate to our old code site at https://code.google.com/p/redbox-mint/. 
# []()v1.5.1

Bug fix release to support the ReDBox v1.5.1 release.
**Release Date:** 13 August 2012
**Release Notes:**

* ReDBox, Mint: Upgrading to [Fascinator v1.1.1](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory).
* Mint: Fixed SRU checks against NLA [ issue #109 ](https://code.google.com/p/redbox-mint/issues/detail?id=109), [r1267](https://code.google.com/p/redbox-mint/source/detail?r=1267)
* ReDBox, Mint: Various Apache Maven configuration changes to make the build more reliable

***


# []()v1.5

Last release under the [EIF048](http://www.redboxresearchdata.com.au/governance/eif048) funding. There are numerous functionality improvements and bug fixes included, based on community feedback and testing, but the major focus of v1.5 has been integration with the National Library of Australia, a new Geospatial UI using OpenLayers for data entry and expanded support of RIF-CS v1.3 elements.
**Release Date:** 25 May 2012
**Release Notes:**

* ReDBox, Mint: Upgrading to [Fascinator v1.1](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory).
* ReDBox, Mint: RIF-CS Schema URLs updated to point at v1.3 (or corrected in ReDBox's case). [r1170](https://code.google.com/p/redbox-mint/source/detail?r=1170), [r1172](https://code.google.com/p/redbox-mint/source/detail?r=1172)
* ReDBox, Mint: ANDS OAI-PMH Harvester should no longer complain about resumption tokens at the end. [Fascinator issue #11](http://code.google.com/p/the-fascinator/issues/detail?id=11) addressed this.
* ReDBox: Added additional licenses to the defaults shipped with ReDBox. [ issue #88 ](https://code.google.com/p/redbox-mint/issues/detail?id=88), [r1108](https://code.google.com/p/redbox-mint/source/detail?r=1108)
* ReDBox: Free-text license entry removed RC1 has now been returned with some tweaks. Has second priority to the controlled vocabulary licensing with regards to RIF-CS. [r1110](https://code.google.com/p/redbox-mint/source/detail?r=1110)
* ReDBox: A new upgrade script. Form fields have not changed in the last few versions. The new script can upgrade from all previous versions. [r1116](https://code.google.com/p/redbox-mint/source/detail?r=1116)
* ReDBox: An ongoing issue with search filters on the home pages failing to function correctly has been fixed. [ issue #56 ](https://code.google.com/p/redbox-mint/issues/detail?id=56), [r1126](https://code.google.com/p/redbox-mint/source/detail?r=1126)
* ReDBox: Improvements and corrections in sorting functionality. [ issue #86 ](https://code.google.com/p/redbox-mint/issues/detail?id=86), [r1128](https://code.google.com/p/redbox-mint/source/detail?r=1128)
* ReDBox: Tweaks/fixes to behaviour of retired records. [ issue #87 ](https://code.google.com/p/redbox-mint/issues/detail?id=87), [r1133](https://code.google.com/p/redbox-mint/source/detail?r=1133)
* ReDBox: Numerous bug fixes and improvements to form UI, help text and validation.
* ReDBox: Fixed a Fascinator issue effecting the ability of VITAL Transformers to send emails when configuration was setup to use proprty substitution (which is was by default). [ issue #59 ](https://code.google.com/p/redbox-mint/issues/detail?id=59)
* ReDBox: IDs are trimmed of left and right whitespace before use in curation. [ issue #60 ](https://code.google.com/p/redbox-mint/issues/detail?id=60), [r1145](https://code.google.com/p/redbox-mint/source/detail?r=1145)
* ReDBox: Creators that were not drawn from a machine driven lookup (Mint or NLA) were missing in the MARC and Dublin Core metadata renditions. [ issue #79 ](https://code.google.com/p/redbox-mint/issues/detail?id=79), [r1147](https://code.google.com/p/redbox-mint/source/detail?r=1147)
* ReDBox: Improvements to the functionality of 'Related Data' in the workflow. [ issue #76 ](https://code.google.com/p/redbox-mint/issues/detail?id=76), [r1152](https://code.google.com/p/redbox-mint/source/detail?r=1152)
* ReDBox: Some long standing issues related to attachments have been fixed. [ issue #53 ](https://code.google.com/p/redbox-mint/issues/detail?id=53), [r1042](https://code.google.com/p/redbox-mint/source/detail?r=1042), [ issue #55 ](https://code.google.com/p/redbox-mint/issues/detail?id=55), [r1156](https://code.google.com/p/redbox-mint/source/detail?r=1156)
* ReDBox: Curators now have an easier option if they want to update Notes metadata. [ issue #97 ](https://code.google.com/p/redbox-mint/issues/detail?id=97), [r1165](https://code.google.com/p/redbox-mint/source/detail?r=1165)
* ReDBox: Improved functionality and bug fix for NLA Searching in form. [ issue #94 ](https://code.google.com/p/redbox-mint/issues/detail?id=94), [ issue #95 ](https://code.google.com/p/redbox-mint/issues/detail?id=95), [ issue #102 ](https://code.google.com/p/redbox-mint/issues/detail?id=102)
* ReDBox: Citation functionality re-worked in response to user feedback. [ issue #77 ](https://code.google.com/p/redbox-mint/issues/detail?id=77), [r1172](https://code.google.com/p/redbox-mint/source/detail?r=1172)
* Mint: Improvements to EAC-CPF / NLA template to validate. Date of creation is generated in the curation manager. [r1154](https://code.google.com/p/redbox-mint/source/detail?r=1154)
* Mint: Fixed issue with published Mint People records not interacting with ReDBox lookups correctly with regards to their primary group affiliation. [ issue #58 ](https://code.google.com/p/redbox-mint/issues/detail?id=58), [r1154](https://code.google.com/p/redbox-mint/source/detail?r=1154)
* Mint: RIF-CS Schema URLs updated to point at v1.3. [r1170](https://code.google.com/p/redbox-mint/source/detail?r=1170)

***


# []()v1.5RC2

Some bug fixes and tweaks. Major new feature works focuses on OpenLayers geo-spatial maps and National Library of Australia integration.
**Release Date:** 9 May 2012
**Release Notes:**

* ReDBox, Mint: Update to [Fascinator v1.0.8](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory)
* ReDBox: Parties Person lookups can now search the National Library of Australia as well. [r1040](https://code.google.com/p/redbox-mint/source/detail?r=1040)
* ReDBox: New geo-spatial GUI in forms using OpenLayers map. [r1035](https://code.google.com/p/redbox-mint/source/detail?r=1035)
* ReDBox: Attachments with the same filename will now be considered unique per Collection, rather then across the whole system. [r1042](https://code.google.com/p/redbox-mint/source/detail?r=1042),[ issue #53 ](https://code.google.com/p/redbox-mint/issues/detail?id=53)
* ReDBox: Role permissions for attachments was incorrect for non-admins. [r1046](https://code.google.com/p/redbox-mint/source/detail?r=1046), [ issue #78 ](https://code.google.com/p/redbox-mint/issues/detail?id=78)
* ReDBox: Adjustments to citations in the RIF-CS template. [r995](https://code.google.com/p/redbox-mint/source/detail?r=995)
* ReDBox: The RIF-CS template will now default to sending the 'label' value to RDA for ANZSRC-TOA. Previously the abridged 'value' field was sent. [r994](https://code.google.com/p/redbox-mint/source/detail?r=994)
* ReDBox: DOI button hidden in form until work is completed. [r1036](https://code.google.com/p/redbox-mint/source/detail?r=1036)
* ReDBox: Migration script has a fix available for pre-v1.1 data which wasn't migrating correctly. [r1039](https://code.google.com/p/redbox-mint/source/detail?r=1039)
* Mint: Expands on earlier work integrating with the National Library of Australia. [r1041](https://code.google.com/p/redbox-mint/source/detail?r=1041)
* ReDBox, Mint: The above work integrating National Library of Australia Party records should fit into the exist curation model for both systems.

***


# []()v1.5RC1

Adding new features for integration with ANDS DOI generation and the National Library of Australia. Numerous enhancements to forms in support of above as well as more broadly embracing new options available in RIF-CS v1.3
**Release Date:** 11 April 2012
**Release Notes:**

* ReDBox, Mint: Upgrade to v1.0.7 of Fascinator platform [v1.0.7](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory).
* ReDBox: Bug fix applied to Jaffa Library relating to loading of checkbox fields. [r3258](http://code.google.com/p/the-fascinator/source/detail?r=3158) (Fascinator)
* ReDBox: Changes to the details screen to show the new features below. [r984](https://code.google.com/p/redbox-mint/source/detail?r=984)
* ReDBox: Experimental integration with ANDS' ['Cite My Data'](http://www.ands.org.au/services/cite-my-data.html) service. This is still being put through its paces due to availability of the ANDS API for testing. [r973](https://code.google.com/p/redbox-mint/source/detail?r=973), [r983](https://code.google.com/p/redbox-mint/source/detail?r=983)
* ReDBox: Form UI elements added to support new Mint Service records and lookups. Also integration into curation manager configuration like any other Mint record. [r977](https://code.google.com/p/redbox-mint/source/detail?r=977)
* ReDBox: Changes to licensing and access rights in forms and metadata template for RIF-CS v1.3. [r948](https://code.google.com/p/redbox-mint/source/detail?r=948)
* ReDBox: RIF-CS v1.3 is now the default (and only) template available for this and future releases. [r947](https://code.google.com/p/redbox-mint/source/detail?r=947)
* ReDBox: Significant addition to forms in support of RIF-CS v1.3 Citation fields. Flows through to RIF-CS templates as well. [r936](https://code.google.com/p/redbox-mint/source/detail?r=936), [r973](https://code.google.com/p/redbox-mint/source/detail?r=973)
* ReDBox: Some security enhancements.
* Mint: Party (People) records have an experimental EAC-CPF metadata template to begin testing. Local customisation of this will be required when we know more about generating quality EAC-CPF and institutions source key local information, like an [ISIL](http://ands.org.au/guides/ardc-party-infrastructure-awareness.html#isil). [r982](https://code.google.com/p/redbox-mint/source/detail?r=982)
* Mint: Now supports ingest of RIF-CS Service records and their curation, as well as lookups to provide to other systems. [r974](https://code.google.com/p/redbox-mint/source/detail?r=974), [r976](https://code.google.com/p/redbox-mint/source/detail?r=976)
* Mint: Fixed display issue on Mint details screen with the 'Curate Now' action. [r966](https://code.google.com/p/redbox-mint/source/detail?r=966)

***


# []()v1.4

This release focused on making cross-institutional implementation easier by separating out 'core' functionality from the logic/branding most likely to be customised. A new build process makes it easier to track and upgrade these differences as well.
**Release Date:** 12 Jan 2012
**Release Notes:**

* ReDBox, Mint: Move to [Fascinator v1.0.6](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory), primarily to make use of the bug fixes developed during the v1.3.1 bugfix release.
* ReDBox, Mint: Major overhaul to Maven project structure and build process. 'Core' Mint and ReDBox artifacts are now deployed by institutional build projects ([Mint](http://code.google.com/p/redbox-mint/source/browse/#svn%2Fmint%2Fbuilds) and [ReDBox](http://code.google.com/p/redbox-mint/source/browse/#svn%2Fredbox%2Fbuilds)).
* ReDBox: Collection's "waiting" on optional relations to curate now display an indicator on the details screen to help indicate that they will not hold up publication. [r749](https://code.google.com/p/redbox-mint/source/detail?r=749)
* ReDBox: Some 'opt-in' experimental work done on harvesting RDA Collections into ReDBox for management. [r754](https://code.google.com/p/redbox-mint/source/detail?r=754)
* ReDBox: References to Institutional names and other local information is now more centrally available in configuration. [r760](https://code.google.com/p/redbox-mint/source/detail?r=760), [r762](https://code.google.com/p/redbox-mint/source/detail?r=762), [ issue #54 ](https://code.google.com/p/redbox-mint/issues/detail?id=54)
* ReDBox: Previously hardcoded String references used for storage of persistent ID information are now set in configuration. [r765](https://code.google.com/p/redbox-mint/source/detail?r=765)
* Mint: The Handle Transformer can now publish URLs using non-standard domains if configured. [r756](https://code.google.com/p/redbox-mint/source/detail?r=756)
* Mint: References to Institutional names and other local information is now more centrally available in configuration. [r759](https://code.google.com/p/redbox-mint/source/detail?r=759), [ issue #54 ](https://code.google.com/p/redbox-mint/issues/detail?id=54)
* Mint: Previously hardcoded String references used for storage of persistent ID information are now set in configuration. [r764](https://code.google.com/p/redbox-mint/source/detail?r=764)
* ReDBox: **Added 2012/03/09** A basic 'opt-in' RIF-CS v1.3 template is now available. [usage notes](http://groups.google.com/group/redbox-repo/browse_thread/thread/5a4d9e28ba90ce42)

***


# []()v1.3.1

This is a bug fix release focusing on some related bugs found in v1.2 and persisting through v1.3.
**Release Date:** 20 Nov 2011
**Release Notes:**

* ReDBox: Fixing example RIF-CS template with elements missing the 'rif' namespace. [r707](https://code.google.com/p/redbox-mint/source/detail?r=707)
* ReDBox: Curation of already published items will ensure that newly linked material gets published. [r706](https://code.google.com/p/redbox-mint/source/detail?r=706)
* Mint: Updating CSV Harvester to v1.0.5.1 to avoid bugs it causes. [ issue #50 ](https://code.google.com/p/redbox-mint/issues/detail?id=50), [r705](https://code.google.com/p/redbox-mint/source/detail?r=705)
* Mint: RIF-CS template uses Object property for identifier to be more consistent with behaviours of the rest of the system. [ issue #49 ](https://code.google.com/p/redbox-mint/issues/detail?id=49), [r702](https://code.google.com/p/redbox-mint/source/detail?r=702)

***


# []()v1.3

This release focused on support for Fedora 3 integration underneath VITAL v5.x, as well as ongoing tweaks, improvements and bug fixes to the curation process.
**Release Date:** 16 Nov 2011
**Release Notes:**

* ReDBox: Alerts system now capable of ingesting XML and configuring the mappings to ReDBox fields. [r671](https://code.google.com/p/redbox-mint/source/detail?r=671) + [r679](https://code.google.com/p/redbox-mint/source/detail?r=679)
* ReDBox: VITAL Integration expanded (and code re-arranged) to function with Fedora 2.x and Fedora 3.x. [r654](https://code.google.com/p/redbox-mint/source/detail?r=654) => [r660](https://code.google.com/p/redbox-mint/source/detail?r=660)
* ReDBox: The default build and distribution now configured for Fedora 3.x. [r672](https://code.google.com/p/redbox-mint/source/detail?r=672)
* ReDBox: Fixed bug in 'keyword' form field autocomplete. [r675](https://code.google.com/p/redbox-mint/source/detail?r=675)
* ReDBox: Significant improvements to 'Related Data' form fields and curation. [r678](https://code.google.com/p/redbox-mint/source/detail?r=678)
* Mint: Trivial alteration to license boilerplate to correct incorrect name in about page of Handle Transformers. [r661](https://code.google.com/p/redbox-mint/source/detail?r=661)
* Mint: Added a new 'Local Curation' Transformer to offer a basic template-based alternative to VITAL and Handle. [r663](https://code.google.com/p/redbox-mint/source/detail?r=663)
* Mint: Handle Transformer improved to allow resolvable URLs based on templates. URLs can also be updated in case you change domain (or similar). [r666](https://code.google.com/p/redbox-mint/source/detail?r=666)
* ReDBox, Mint: Fixed an intermittent bug in Curation Managers where new Persistent IDs were not being indexed and committed fast enough to be searchable on first usage. [r668](https://code.google.com/p/redbox-mint/source/detail?r=668), [r669](https://code.google.com/p/redbox-mint/source/detail?r=669)

***


# []()v1.2

The main purpose for release version 1.2 of ReDBox / Mint is to implement staged release steps for linked datasets of related objects (ie. RIF-CS Collections, Parties and Activities) in a coordinated fashion.
It seeks to address the larger issue of publishing linked data in a distributed environment in such a way that:
1. Data is is not needlessly curated or published if it is not part of a significant relationship.
1. All collaborating systems are aware of the need to publish in a time sensitive fashion.
1. External parties that can see one node of a linked network can reasonably expect to see all other related nodes curated and published in a similar timeframe.

**Release Date:** 06 October 2011
**Release Notes:**

* ReDBox, Mint: Fascinator version [upgraded to v1.0.5](http://code.google.com/p/the-fascinator/wiki/ReleaseHistory)
* ReDBox, Mint: A new [CurationManager](https://code.google.com/p/redbox-mint/wiki/CurationBoundaryPlanning) replaces the typical Fascinator tool chain. The Mint and ReDBox CurationManager's talk to each other and are responsible for curating and publishing linked datasets in synch with each other.
* ReDBox, Mint: Alterations to logging rotation to address OS specific file creation problems. [r611](https://code.google.com/p/redbox-mint/source/detail?r=611), [r610](https://code.google.com/p/redbox-mint/source/detail?r=610)
* ReDBox, Mint: A configurable delay will occur during system startup to allow Solr to start before Fascinator. [ issue #45 ](https://code.google.com/p/redbox-mint/issues/detail?id=45)
* ReDBox, Mint: A new 'published' portal is used by the CurationManager to isolate records ready for the ANDS harvester. [ issue #7 ](https://code.google.com/p/redbox-mint/issues/detail?id=7)
* ReDBox, Mint: Related objects are now capable of requesting a reverse relationship from the other party via the CurationManager. [ issue #8 ](https://code.google.com/p/redbox-mint/issues/detail?id=8)
* ReDBox: Major overhaul of internal form data, as per [planning](https://code.google.com/p/redbox-mint/wiki/FormFieldsReMap).
* ReDBox: Form data now stores version information and forms enforce correct version at load time.
* ReDBox: New migration script provided to upgrade older versions of form data.
* ReDBox: The VITAL Subscriber plugin has been converted to a Transformer plugin to fit into the new CurationManager.
* Mint: Sample Parties (People and Groups) now have more fields by default for implementers to use as an example. [ issue #19 ](https://code.google.com/p/redbox-mint/issues/detail?id=19)
* Mint: Mint can now ingest pre-existing relationships between records. This is configurable.
* Mint: NHMRC and ARCS Activities are now ingested with PURLS pointing at ANDS records in RDA by default. [ issue #28 ](https://code.google.com/p/redbox-mint/issues/detail?id=28)
* Mint: Mint Parties are starting to store multiple known identifiers and can be found by any of them in a search. This work is experimental for now.
* Mint: A bug preventing record deletion has been resolved. [ issue #23 ](https://code.google.com/p/redbox-mint/issues/detail?id=23)

***


# []()v1.1

This version focuses on addressing issues raised during the initial v1.0 deployment. Numerous bug fixes and steamlined installation and operational processes are included.
**Release Date:** 09 September 2011
**Release Notes:**

* ReDBox, Mint: Updated to Fascinator v1.0.4, which comes with significant improvements to logging.
* ReDBox, Mint: 'About' pages now show the software version for the ReDBox/Mint project. ([ issue #24 ](https://code.google.com/p/redbox-mint/issues/detail?id=24))
* ReDBox, Mint: Appropriate configuration now supplied for Solr audit logging to function correctly.
* Mint: Updated Geonames integration to streamline installation process. Geonames now released [via Maven](http://code.google.com/p/solr-geonames/).
* ReDBox: Tweaks to the wording on attachment labels sent to VITAL. ([ issue #3 ](https://code.google.com/p/redbox-mint/issues/detail?id=3))
* ReDBox: '*Management*' tab now hidden from guest users on detail screen. ([ issue #12 ](https://code.google.com/p/redbox-mint/issues/detail?id=12))
* ReDBox: Creator names are now being faceted correctly. All creator subfields (eg. first name on its own) were being faceted as creators. ([ issue #11 ](https://code.google.com/p/redbox-mint/issues/detail?id=11))
* ReDBox: Titles on URLs for related publications and websites added to form and mapped to RIF-CS. Notes are commented out in RIF-CS, but easy to alter. ([ issue #13 ](https://code.google.com/p/redbox-mint/issues/detail?id=13))
* ReDBox: Locations and Identifiers improved in form for greater clarity and mapped to RIF-CS with more detail and accuracy. ([ issue #18 ](https://code.google.com/p/redbox-mint/issues/detail?id=18))
* ReDBox: Marc template now maps the identifier. ([ issue #33 ](https://code.google.com/p/redbox-mint/issues/detail?id=33))
* ReDBox: Geospatial data now displays better on the details screen. ([ issue #22 ](https://code.google.com/p/redbox-mint/issues/detail?id=22))
* ReDBox: Details screen shows placeholder '*(no information)*' on empty fields. ([ issue #42 ](https://code.google.com/p/redbox-mint/issues/detail?id=42))
* ReDBox: Several minor bug fixes on display of data in the details screen. ([ issue #43 ](https://code.google.com/p/redbox-mint/issues/detail?id=43))
* ReDBox: Usability of 'Notes' tab in workflow improved. ([ issue #20 ](https://code.google.com/p/redbox-mint/issues/detail?id=20) + [ issue #41 ](https://code.google.com/p/redbox-mint/issues/detail?id=41))
* ReDBox: Bug in Javascript Library corrected, was causing oddities in multi-select drop downs (like ANZSRC). ([ issue #40 ](https://code.google.com/p/redbox-mint/issues/detail?id=40))
* Mint: Handle Transformer now has an 'about' page. ([ issue #37 ](https://code.google.com/p/redbox-mint/issues/detail?id=37))
* ReDBox: VITAL Subscriber now has an 'about' page. ([ issue #38 ](https://code.google.com/p/redbox-mint/issues/detail?id=38))
* ReDBox: Fixed bug displaying attachments on detail screen. ([ issue #44 ](https://code.google.com/p/redbox-mint/issues/detail?id=44))
* ReDBox: Fixed some bugs in dublin core template. ([ issue #32 ](https://code.google.com/p/redbox-mint/issues/detail?id=32))
* Mint: Fixed Solr filter on activities with out-of-date value. ([ issue #34 ](https://code.google.com/p/redbox-mint/issues/detail?id=34))
* ReDBox: Optional 'accessRights' will be mapped to 'rights' (required by RIF-CS) if no 'rights' have been supplied. ([ issue #4 ](https://code.google.com/p/redbox-mint/issues/detail?id=4))
* Mint: Handle Transformer now checks increment file on startup, and can handle basic issues like extra whitespace. ([ issue #1 ](https://code.google.com/p/redbox-mint/issues/detail?id=1))

***


# []()v1.0.1

This release was mostly concerned with migrating to new build/release infrastructure. Some minor bug fixes were also included as time permitted.
**Release Date:** 01 August 2011
**Release Notes:**

* ReDBox, Mint: Migration away from USQ infrastructure to Sonatype and Google Code now completed.
* ReDBox: Geospatial mappings to RIF-CS are uncommented in the default template.
* ReDBox: Resolved a bug observed in IE when uploading multiple attachments to a record in a single sitting. [ Issue #2 ](https://code.google.com/p/redbox-mint/issues/detail?id=2)
* ReDBox: Resolved a bug with numerous form fields containing growing lists of data when the list length exceeded 9 entries. This was a bug in the underlying [Jaffa library](http://code.google.com/p/the-fascinator/source/detail?r=2994). [ Issue #14 ](https://code.google.com/p/redbox-mint/issues/detail?id=14), [ Issue #15 ](https://code.google.com/p/redbox-mint/issues/detail?id=15), [ Issue #16 ](https://code.google.com/p/redbox-mint/issues/detail?id=16), [ Issue #17 ](https://code.google.com/p/redbox-mint/issues/detail?id=17)

***


# []()v1.0.0

Initial release of ReDBox / Mint: May 2011