## []()CSV Upload Fields

Mint can harvest a CSV file containing people information. The fields expected in the CSV file are listed below

 
    ** Mandatory** ** Field name** **Description** **Note****Examples**  **Mint Field**   * ID The institution's ID - likely to be a staff, student or researcher ID   00007429 foaf:Person.dc.identifier
   * Given_Name The first given name   Michael foaf:Person.foaf:givenName Other_NamesAny intermediary (middle) names  Alfredfoaf:Person.vivo:middleName    Family_Name Often referred to as surname   Jones foaf:Person.foaf:familyName    Pref_Name The preferred name, usually a single name   Mick foaf:Person.foaf:nick HonorificA name prefix Mrs, Dr, Proffoaf:Person.foaf:honorificPrefix    Email The person's email address  mick@staff.edu.au foaf:Person.foaf:mbox    Job_Title The primary job title for the person  Facility Director foaf:Person.vivo:hasRole    GroupID_1 Links to Party (Group) records See [Loading Group Data](documentation-system-administration-administering-mint-loading-data-loading-group-data)
 foaf:Person.vivo:currentMemberOf.0    GroupID_2   

 foaf:Person.vivo:currentMemberOf.1 GroupID_3   foaf:Person.vivo:currentMemberOf.2    ANZSRC_FOR_1   The 2, 4 or 6 digit Field of Research code as designated by the Australian Bureau of Statistics under ANZSRC (2008) The harvest script will convert this to the associated PURL04, 0101, 070201 foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.0.rdf:resource
foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.0.skos:prefLabel     ANZSRC_FOR_2      foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.1.rdf:resource
foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.1.skos:prefLabel     ANZSRC_FOR_3      foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.2.rdf:resource
foaf:Person.foaf:topic_interest.dc:subject.anzsrc:for.2.skos:prefLabel  URI    This is a URI rather than a staff IDThis may be used to hold a Handle-based URIhttp://staff.edu.au/5474365foaf:Person.rdf:about (if a URI is provided)    NLA_Party_Identifier  A National Library of Australia party identifier http://nla.gov.au/nla.party-461793  foaf:Person.foaf:OnlineAccount.redbox:NLAPartyIdentifier    ResearcherID  A Thomson Reuters ID created using the http://www.researchid.com service  http://www.researcherid.com/rid/F-3500-2011 foaf:Person.foaf:OnlineAccount.redbox:researcherID openID A valid Open ID - see http://openid.net mike.myopenid.comfoaf:Person.foaf:openid Personal_URI A URI nominated by the staff memberThis is an identifier entry. Please use Personal_Homepage for websites.http://me.example.com/foaf:Person.redbox:personalURI   Personal_Homepage The researcher's personal website 
http://www.me.example.com/ foaf:Person.foaf:homepage Staff_Profile_HomepageThe researcher's webpage within the institutional web presence  http://staffprofiles.edu.au/mjonesfoaf:Person.foaf:workInfoHomePage  Description A brief bio or overview of the researcher Newlines aren't really supported here.Mike has been investigating the effects of loud noises.foaf:Person.dc:description   
 
## []()Notes:


* The FOAF version referred to in this section is Version 0.98: [http://xmlns.com/foaf/spec/20100809.html](http://xmlns.com/foaf/spec/20100809.html)



The sample upload files can be previewed in [http://code.google.com/p/redbox-mint/source/browse/#svn%2Fmint%2Ftrunk%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fdata](http://code.google.com/p/redbox-mint/source/browse/#svn%2Fmint%2Ftrunk%2Fsrc%2Fmain%2Fconfig%2Fhome%2Fdata)