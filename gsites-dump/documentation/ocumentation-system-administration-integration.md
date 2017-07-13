## []()Plugins

ReDBox and Mint have been developed within a plug-in architecture. There's a range of plugins available in our [Plugin Directory](documentation-system-administration-plugin-directory) that you can roll into your [Institutional Builds](documentation-technical-institutional-builds).
## []()Data Sources
 
Both ReDBox and Mint have the ability to ingest (harvest) data from sources that can provide data files (such as XML and CSV). The [loading data documentation](documentation-system-administration-loading-data) has more details, but more generally, the [Fascinator platform](http://sites.google.com/site/fascinatorhome/home/documentation/technical) is very flexible in terms of offering methods for data ingest. A quick message to [the mailing list](http://groups.google.com/group/redbox-repo/) is advised if you are considering how to get your data from a new system, as experienced community members can provide a lot fo background knowledge and ideas. 
## []()Geonames
 
Case in point... the [Geonames](http://www.geonames.org/) service was integrated with Mint in a somewhat 'outside-the-square' fashion to avoid flooding the core system with 7+ million records. It has [its own documentation](documentation-system-administration-administering-mint-geonamesdata). 
## []()Curation Transformers
 
With the new curation model implemented in v1.2 and upwards the specific integration with 'curation' systems like Handle and VITAL are starting to become more flexible. Rather then indicating direct integration with a particular system, the table below shows which Curation Transformer exist, their general purpose, and which systems they have been integrated with.    **Transformer** **Description** **Default** **ReDBox** **Mint**   [Handle Curation](documentation-system-administration-administering-mint-handle-server) Integrates with the Handle network to generate URI and (optionally) allows them to resolve as URLs. Mint Compatible Compatible   [Local Curation](documentation-system-administration-integration-local-curation) A new optional Transformer created in v1.3. Intended to be useful in test/demo systems, it simply 'curates' objects by building an ID based on a template. N/A Compatible Compatible   [VITAL Curation](documentation-system-administration-administering-redbox-vital-integration) Integrates with VITAL to piggyback of the existing Handle setup resolving back to VITAL's front-end for publication. ReDBox Compatible Untested   

## []()NLA Integration

Mint can interact with the National Library of Australia's [Party Infrastructure Project](https://wiki.nla.gov.au/display/ARDCPIP/ARDC+Party+Infrastructure+Project+Home). See [NLA Integration](documentation-system-administration-administering-mint-nla-integration) for details.