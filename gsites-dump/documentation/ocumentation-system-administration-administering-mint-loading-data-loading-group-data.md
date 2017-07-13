## []()CSV Upload Fields

Mint can harvest a CSV file containing group information. The fields expected in the CSV file are listed below


** Mandatory**** Field name****Description****Note****Examples** **Mint Field** *IDAn identifier for the group FoSfoaf:Group.dc.identifier
 *NameThe name of the group Faculty of Sciencefoaf:Group.foaf:name EmailA contact email address for the group fos@uni.edu.aufoaf:Group.foaf:mbox PhoneA phone number for contacting the group  07 3456 7654foaf:Group.foaf.phone Parent_Group_ID Link to the parent groupUoAfoaf:Group.vivo:subOrganizationWithin URI    This is a URI for the groupThis may be used to hold a Handle-based URIhttp://uni.edu.au/fosfoaf:Group.rdf:about NLA_Party_IdentifierA National Library of Australia party identifierhttp://nla.gov.au/nla.party-461793foaf:Group.foaf:OnlineAccount.redbox:NLAPartyIdentifier HomepageThe group's website
http://www.fos.uni.edu.au/foaf:Group.foaf:homepage DescriptionA brief description of the groupNewlines aren't really supported here.
foaf:Group.dc:description

## []()Notes:


* The FOAF version referred to in this section is Version 0.98: [http://xmlns.com/foaf/spec/20100809.html](http://xmlns.com/foaf/spec/20100809.html)



The sample upload files can be previewed in [http://code.google.com/p/redbox-mint/source/browse/#svn%2Fmint%2Ftrunk%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fdata](http://code.google.com/p/redbox-mint/source/browse/#svn%2Fmint%2Ftrunk%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fdata)