[[_TOC_]]

 
 
## []()Introduction
 
NOTE: The Jaffa library mentioned in this page is being re-developed (see [https://github.com/redbox-mint/jaffa](https://github.com/redbox-mint/jaffa)). If you just want to do a lookup against Mint, please use the links mentioned in Section 1.1 with your own code. Don't forget that the mailing list is a good place to go if you get stuck. 
This document is intended to outline the flow of data from Mint lookups, through the ReDBox proxy scripts, and down to the ReDBox forms. 
To begin, let's outline the various parts that are involved in this, and who they would be of interest to: 
1. Mint has lookup scripts to respond to queries. The section below called 'Lookup Scripts' details what/where they are, as well as the query strings and external URLs that end-users would care about.
 1. ReDBox has proxy scripts to avoid Cross-site scripting (XSS) issues that can appear in the client's browsers. If you are looking to replicate ReDBox's functionality in your own system, you'll need something similar to fulfil this role. The section below on 'Proxy Script' covers this.
 1. The ReDBox forms are built around the [Jaffa Library](https://github.com/redbox-mint/javascript-widgets) which generally handles the AJAX part of the lookups. The 'Jaffa Library' sections covers this, including some considerations for alternatives.
 1. Finally, ReDBox adds its own Javascript and HTML to layer across the top of the Jaffa Library, covering application specific usage. This section is covered in 'ReDBox Forms'.
  
## []()1.0 | Lookup Scripts
 
This is the origin of our data in Mint, and you'll care about two different perspectives, based on who your are, and what you are trying to do: 
### []()1.1 | External Users : Just give me data
 
Ok, so you are simply going to want the external facing URLs and the syntax used to query them. You'll notice in the examples below the format is usually: 
* http://{your_server}/mint/{data_portal}/opensearch/lookup?{search_parameters}
 
We've used the development server as our example: 
* **Activities**: This is typically used for jQuery autocomplete, so it will provide a list of results that gets more and more specific as the user types in more details:
 
 * *Examples*: '[d](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=d)', '[dp](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp)', '[dp0](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp0)', '[dp09](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp09)' ... '[dp0987446](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp0987446)'
  * *Final example*: [http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp0987446](http://demo.redboxresearchdata.com.au/mint/Activities/opensearch/lookup?count=25&searchTerms=dp0987446)
 
* **ANZSRC - FOR**: You can see that you start a query by getting all of the top level codes (first two digits) and the JSON response allows you to go up and down by providing 'skos:broader' and 'skos:narrower' elements. Specific codes are referred to by URI, so rather than '05', you'd use 'http://purl.org/asc/1297.0/2008/for/05'
 
 * *Top level*: The first query... [http://demo.redboxresearchdata.com.au/mint/ANZSRC_FOR/opensearch/lookup?count=999&level=top](http://demo.redboxresearchdata.com.au/mint/ANZSRC_FOR/opensearch/lookup?count=999&level=top)
  * *Details*: Asking for specifics... [http://demo.redboxresearchdata.com.au/mint/ANZSRC_FOR/opensearch/lookup?count=999&level=http%3A//purl.org/asc/1297.0/2008/for/05](http://demo.redboxresearchdata.com.au/mint/ANZSRC_FOR/opensearch/lookup?count=999&level=http%3A//purl.org/asc/1297.0/2008/for/05)
 * **ANZSRC - SEO**: Works just like above.
 
 * *Top level*: The first query... [http://demo.redboxresearchdata.com.au/mint/ANZSRC_SEO/opensearch/lookup?count=999&level=top](http://demo.redboxresearchdata.com.au/mint/ANZSRC_SEO/opensearch/lookup?count=999&level=top)
  * *Details*: Asking for specifics... [http://demo.redboxresearchdata.com.au/mint/ANZSRC_SEO/opensearch/lookup?count=999&level=http%3A//purl.org/asc/1297.0/2008/seo/86](http://demo.redboxresearchdata.com.au/mint/ANZSRC_SEO/opensearch/lookup?count=999&level=http%3A//purl.org/asc/1297.0/2008/seo/86)
 
* **Languages**: Used specifically by ReDBox as a static lookup for language codes... may not be useful to others.
 
 * *Complete List*: [http://demo.redboxresearchdata.com.au/mint/Languages/opensearch/lookup?count=9999](http://demo.redboxresearchdata.com.au/mint/Languages/opensearch/lookup?count=9999)
 
* **Funding_Bodies**: Lets data entry staff lookup which funding bodies have been loading into Mint. This is a JSON autocomplete field, just like 'Activities'.
 
 * *Example* : [http://localhost:9001/mint/Funding_Bodies/opensearch/lookup?count=25&searchTerms=aus](http://localhost:9001/mint/Funding_Bodies/opensearch/lookup?count=25&searchTerms=aus)
 
* **Parties_Groups**: Is used in the forms to populate drop-down lists containing all organisational units, so it is typically queries for a complete list.
 
 * *Example*: Complete list... [http://demo.redboxresearchdata.com.au/mint/Parties_Groups/opensearch/lookup?count=9999](http://demo.redboxresearchdata.com.au/mint/Parties_Groups/opensearch/lookup?count=9999)
 
* **Parties_People**: Is typically queried for specific people, or similarly named groups of people.
 
 * *Example*: Looking for "ludo smith"... [http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith](http://demo.redboxresearchdata.com.au/mint/Parties_People/opensearch/lookup?searchTerms=ludo%20smith)
 
* **Geonames**: If you'd like to do specific geospatial searching, the Mint server is typically installed with a [Geonames instance](https://github.com/redbox-mint/solr-geonames) as well. This is not part of Mint per se, but it is normally available under '/geonames'. The search interface has [its own documentation](https://github.com/redbox-mint/solr-geonames/blob/master/README.md).
 
### []()1.2 | Developers / Admins : I need to know everything
 
In truth, every portal in Mint supports a lookup, since the 'default' portal offers one that other portals can inherit (as per [Fascinator doco](https://sites.google.com/site/fascinatorhome/home/documentation/technical/details/branding-appearance#TOC-Views-vs.-Skins-vs.-Templates)). 
These views inherit the 'default' lookup interface: 
* Funding_Bodies
 * Languages
 * Parties_Groups
 * Parties_People
 * published
 
And these views implement their own version: 
* Activities
 * ANZSRC_FOR
 * ANZSRC_SEO
 
So, if you'd like to overwrite the lookup feature for your own custom portals in Mint these are good examples. They show the correct Velocity template ('/opensearch/lookup.vm') and Jython script ('/scripts/opensearch/lookup.py') to provide in place of the inherited versions from the 'default' portal. Of course, you can always just put an alternative name (eg. 'mylookup') beside it and offer a second URL. 
If you want to make system-wide changes, you can always consider just changing the default: 
* /portal/default/mint/opensearch/lookup.vm
 * /portal/default/mint/scripts/opensearch/lookup.py
 
## []()2.0 | Proxy Script
 
In practice, if you are going to query Mint from another system, there are another two issues you **may** want to consider, and they are both satisfied by having a local server-side script to proxy your searches. 
ReDBox has a Jython script you can [look at for reference](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/portal/default/redbox/scripts/proxyGet.py) if desired, but something similar should be basic to construct in any given language. 
### []()2.1 | Local Application Behaviour
 
This is less of a technical requirement, and more of a 'nice to have' option. In ReDBox the proxy script also offers a local wrapper to ensure that the forms get a consistent response. 
ReDBox wraps appropriate JSON around the results to ensure autocomplete works, and errors are intelligible etc. If you are implementing another system it may be desirable to post-process results before sending them back to your forms. 
### []()2.2 | Cross-site Scripting
 
If your integration is from local system forms you are going to run into standard XSS protections built into the browser if you try to get your Javascript to search Mint directly (because it is, presumably, hosted on a different domain). 
In response you basically submit your searches to a proxy script on your own domain, and it performs server-side queries out to Mint on your behalf. 
## []()3.0 | Jaffa Library
 
The Jaffa Library is a Javascript Library (built on top of [jQuery](http://jquery.com/)) designed to make form development easier. It was developed by one of the original dev team on the ReDBox project and spun off into a separate library: 
* <strike>Documentation is on a USQ server. </strike>(lost)
 * Source code is in the [Git repository](https://github.com/redbox-mint/javascript-widgets). ReDBox imports the widgets to build its form via [https://github.com/redbox-mint/redbox/blob/master/.gitmodules](https://github.com/redbox-mint/redbox/blob/master/.gitmodules).
 
Its origins however do show, in that there is some legacy coupling to ReDBox/Mint in the code where certain assumptions are made (such as the behaviour of name lookups). If you are interested in searching Mint from your own systems, it is worth at least taking a look at Jaffa, if not simply using it yourself. 
You can always roll your own version, in which case a JS Library like jQuery would still be a strong suggestion, given how it makes AJAX a lot easier to work with. 
## []()4.0 | ReDBox Forms
 
As per above, the ReDBox forms are a layer of HTML/Javascript built using Jaffa. It offers utilities when doing potentially complicated elements, like repeatable lists, or drop-downs backed into a lookup etc. The Jaffa doco covers the general usage, but checking out some of the ReDBox code shows the specifics. 
All Fascinator workflow templates are generally found in '/portal/default/{you_portal}/workflows/', so for ReDBox, this is '[/portal/default/redbox/workflows/](https://github.com/redbox-mint/redbox/tree/master/config/src/main/config/portal/default/redbox/workflows)'. 
The ReDBox datasets workflow is particularly complicated, not only in terms of Javascript usage, but also because it is so big, so it has been logically separated into multiple sections. The 'tree-like' setup below is an attempt to show the hierarchy you will find, specifically because lower level templates have access to macros defined in higher level templates: 
* `dataset.vm`: Fascinator page; loads up jQuery and Jaffa, then [loads](http://api.jquery.com/load/) the next script down to bring the form in as an AJAX call.
 
 * `/forms/dataset.vm`: Loaded by an AJAX call when starting Jaffa, and contains macros/HTML used by each section; designed assuming Jaffa is running. All of the scripts below are included during this second AJAX call (ie. they aren't all called separately) and each one corresponds to one of the tabs in the form.
 
  * `/forms/sections/attach.vm`: File attachments... somewhat of a red-headed stepchild; this tab is a very specific setup outside of the Jaffa framework, since it relates to uploading files etc.; Treat this slightly separate from the other templates.
   * `/forms/sections/coverage.vm`: Fairly small (in terms of form fields), but it does have a lot of Javascript related geospatial integration.
   * `/forms/sections/description.vm`: A few basic repeatable lists, and 'Related Data' is more complicated given that it is a lookup back into ReDBox for Collections.
   * `/forms/sections/general.vm`: Fairly basic. There is a Mint lookup for Languages, and Type lookup which ReDBox 'fakes' by referring to a [local static JSON file](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/portal/default/redbox/workflows/forms/data/types.json).
   * `/forms/sections/management.vm`: There's quite a few examples in this template, a [static lookup](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/portal/default/redbox/workflows/forms/data/identifierTypes.json), Activities ('Grants'), Parties_Groups ('Data Affiliation') and Funding_Bodies.
   * `/forms/sections/notes.vm`: Pretty basic.
   * `/forms/sections/people.vm`: A few different Parties_People lookups of varying complexity.
   * `/forms/sections/request.vm`: Pretty basic.
   * `/forms/sections/rights.vm`: Has a [static lookup](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/portal/default/redbox/workflows/forms/data/licences.json) for licenses.
   * `/forms/sections/subject.vm`: ANZSRC Codes, including a [static lookup for TOA](https://github.com/redbox-mint/redbox/blob/master/config/src/main/config/portal/default/redbox/workflows/forms/data/researchTypes.json), and another ReDBox lookup for keywords.
   * `/forms/sections/submit.vm`: Not much here aside from some 'secret sauce' validation logic for Jaffa. I try not to touch it, if it can be helped... requires very careful and thorough testing, and is integrated with validation spread across all the tabs.