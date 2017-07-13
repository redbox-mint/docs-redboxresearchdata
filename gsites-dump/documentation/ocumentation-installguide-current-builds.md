With the release of version 1.4 of ReDBox and Mint we've made considerable effort to separate out the 'core' parts of the software from the areas that each institution is most likely to want to change. 
The new build setup is an extension of the pre-existing Maven project setup, where ReDBox and Mint list the Fascinator platform as a dependency and then deploy on top. The new builds list ReDBox and Mint as dependencies, bringing in the core elements with default configuration before deploying your local configuration and customisations on top. 
The technical details aren't particularly relevant here (they are covered on the [developer's wiki](http://code.google.com/p/redbox-mint/)), but what we do want to look at is what kind of builds are already available and what they do. The builds currently available are a great starting point for creating your own institutional build.
#### **[]()Mint Builds


* '`dev-local`': A development build using the [Local Curation plugin](documentation-system-administration-integration-local-curation). This build is used for the demonstration system packaged up for download on the Google code site and requires almost no configuration. It is **not recommended for use as a production Metadata Store** however as it trivialises the implementation of curation and persistent identification. Good for simple tests and demos however if you want to install on a laptop you take off campus.

* '`dev-handle`': Is similar to above, but brings in dependencies and config for [integrating with a Handle server](documentation-system-administration-administering-mint-handle-server) for identification. This is suitable for production use, and although it doesn't require much more configuration than the 'local' demo it does require you to integrate with your institution's Handle server.

#### **[]()ReDBox Builds

* '`dev-local`': Same as above for Mint... again, this is **not recommended for use as a production Metadata Store**.
* '`dev-handle`': Direct Handle integration, as per above description. This is distinguished from the VITAL integration builds below in that it interfaces directly with Handle.
* '`dev-vital2`': [Integrates with VITAL](documentation-system-administration-administering-redbox-vital-integration) versions on top of Fedora v2.x... possibly '`vital2`' is not the most obvious name now that I explain this. If you have VITAL set up as an institutional repository, this build is designed to integrate with this. The premise is that VITAL will do all of you integration with Handle as well as being responsible for external publication. ReDBox can do these things, but institutions with VITAL already setup as a public facing portal with publication data will likely want to put research data alongside it.
* '`dev-vital3`': Same as above, but updates to Fedora v3.x to integrate with newer versions of VITAL.