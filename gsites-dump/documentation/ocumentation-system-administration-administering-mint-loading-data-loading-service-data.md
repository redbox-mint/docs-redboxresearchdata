## []()CSV Upload Fields

Mint can harvest a CSV file containing people information. The fields expected in the CSV file are listed below


** Mandatory**** Field name****Description****Note****Examples** **Mint Field** *IDThe institution's ID for the service  scan001dc.identifier
 *NameThe Service's name Hi-res 3D Scanner
 *TypeThe service typeAs per [RIF- CS service types](http://services.ands.org.au/documentation/rifcs/1.3/vocabs/vocabularies.html)
create, generate, transform  ANZSRC_FOR_1 The 2, 4 or 6 digit Field of Research code as designated by the Australian Bureau of Statistics under ANZSRC (2008)The harvest script will convert this to the associated PURL04, 0101, 070201dc:subject.anzsrc:for.0.rdf:resource
dc:subject.anzsrc:for.0.skos:prefLabel ANZSRC_FOR_2   dc:subject.anzsrc:for.1.rdf:resource
dc:subject.anzsrc:for.1.skos:prefLabel ANZSRC_FOR_3   dc:subject.anzsrc:for.2.rdf:resource
dc:subject.anzsrc:for.2.skos:prefLabel LocationThe physical address of the service
1 Tool St, Dubbo, NSW
 Coverage_Temporal_FromThe initial date for which the service can provide information 
   Coverage_Temporal_ToThe final date for which the service can provide information 
   Coverage_Spatial_TypeThe type of spatial format used in Coverage_Spatial_Value      Coverage_Spatial_ValueThe geographical coverage of the system 
   Existence_StartWhen the service started being available 
   Existence_EndWhen the service stopped being available  
   WebsiteThe service's websiteThis is a RIF-CS relatedInfo element with type="website" http://service.example.edu.au/  Data_Quality_InformationA URL that points to a resource outlining data quality info for the serviceThis is a RIF-CS relatedInfo element with type= "dataQualityInformation"http://service.example.edu.au/dataquality  Reuse_InformationA URL that points to a resource outlining reuse info for the service This is a RIF-CS relatedInfo element with type= "reuseInformation"http://service.example.edu.au/reuse  Access_Policy A URL that points to a resource outlining the access policy for the service 
http://service.example.edu.au/access   URI This may be the same as the ID   Description


dc:description

## []()Notes:


* The FOAF version referred to in this section is Version 0.98: [http://xmlns.com/foaf/spec/20100809.html](http://xmlns.com/foaf/spec/20100809.html)



The sample upload files can be previewed in [http://code.google.com/p/redbox-mint/source/browse/mint/trunk/config/src/main/config/home/data/](http://code.google.com/p/redbox-mint/source/browse/mint/trunk/config/src/main/config/home/data/)