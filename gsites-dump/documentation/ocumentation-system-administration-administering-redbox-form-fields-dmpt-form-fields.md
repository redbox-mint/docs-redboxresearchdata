[[_TOC_]]

 
Note: please refer to [Collection Form Fields](documentation-system-administration-administering-redbox-form-fields-collection-form-fields) for details pertaining to collections. 
Notes: 
 * The agls namespace is defined at http://www.agls.gov.au/pdf/AGLS%20Metadata%20Standard%20Part%201%20Reference%20Description.PDF
 * All items mapping to skos:note.#.dc:description imply that: 
  * skos:note.#.dc:created: the date on which the collection record was created
  * skos:note.#.foaf:name: the name of the plan owner
  * A prefix to skos:note.#.description is often provided for context
  
  
## []()Overview
 
### []()Project name (dc:title)
    Purpose / Notes The project's name   Internal Usage Important as an internal ReDBox (Fascinator) field where only title and description are mandatory.   ReDBox DMPT Forms "Project name": mandatory, free-text   Maps to Collection form field: 
dc:title (used as a placeholder for the user to change) 
swrc:ResearchProject.dc:title    
 
### []()Project ID (dc:identifier)
    Purpose / Notes A unique identifier for the project   Internal Usage None   ReDBox DMPT Forms "Project ID": mandatory, free-text. May be provided by another system via an alert   Maps to Collection form field: none    
### []()Description (dc:description)
    Purpose / Notes A description for the project    Internal Usage Important as an internal ReDBox (Fascinator) field where only title and description are mandatory.    ReDBox DMPT Forms "Description": Mandatory, text area    Maps to Collection form field: dc:description    
### []()Project website (dc:relation.bibo:Website)
    Purpose / Notes Provides a link to the project's website    Internal Usage None    ReDBox DMPT Forms "Project website": Optional, free-text    Maps to Collection form field: dc:relation.bibo:Website
 This field is broken up further in the collection forms so should map as follows:
    ** Collection Field** **Source **    dc:relation.bibo:Website.0.dc:identifier DMPT: dc:relation.bibo:Website    dc:relation.bibo:Website.0.dc:title "Project website"     dc:relation.bibo:Website.0.skos:note *not mapped *    
     
### []()Start date (dc:coverage.vivo:DateTimeInterval.vivo:start)
    Purpose / Notes Marks the start date of the project   Internal Usage None   ReDBox DMPT Forms "Start date": Mandatory, Date picker    Maps to Collection form field: dc:coverage.vivo:DateTimeInterval.vivo:start    
### []()End date (dc:coverage.vivo:DateTimeInterval.vivo:end)
    Purpose / Notes Marks the start date of the project   Internal Usage None   ReDBox DMPT Forms "End date": Mandatory, Date picker    Maps to Collection form field: dc:coverage.vivo:DateTimeInterval.vivo:end     
### []()Funding source (foaf:fundedBy.foaf:Agent)
 
Represented by two underlying fields: ```
foaf:fundedBy.foaf:Agent.0.dc:identifier foaf:fundedBy.foaf:Agent.0.skos:prefLabel
```
    Purpose / Notes A list of funding bodies for the project   Internal Usage None   ReDBox DMPT Forms 
"Funding source": Optional, list 
free-text with autocomplete against Mint   Maps to Collection form field:  ```
foaf:fundedBy.foaf:Agent.0.dc:identifier foaf:fundedBy.foaf:Agent.0.skos:prefLabel
```
     
### []()Grant numbers (foaf:fundedBy.vivo:Grant)
 
Represented by four underlying fields: ```
foaf:fundedBy.vivo:Grant.0.redbox:internalGrant foaf:fundedBy.vivo:Grant.0.redbox:grantNumber foaf:fundedBy.vivo:Grant.0.dc:identifier foaf:fundedBy.vivo:Grant.0.skos:prefLabel
```
    Purpose / Notes Links the plan to a Mint Activity or denotes a local activity   Internal Usage None   ReDBox DMPT Forms "Grant number(s)": Optional, list
 
 Implementation is a little complicated. If the grant is flagged as internal then simple free-text entry of the grant number and label is expected.
 
 Otherwise the label field will be locked and typing anything in the grant number field will perform an autocomplete lookup against Mint (for both numbers and/or labels) and will populate the grant number, label and identifier upon selection.   Maps to Collection form field:  ```
foaf:fundedBy.vivo:Grant.0.redbox:internalGrant foaf:fundedBy.vivo:Grant.0.redbox:grantNumber foaf:fundedBy.vivo:Grant.0.dc:identifier foaf:fundedBy.vivo:Grant.0.skos:prefLabel
```
     
 
### []()Type of activity (dc:subject.anzsrc:toa)
 
Represented by two underlying fields: ```
dc:subject.anzsrc:toa.rdf:resource dc:subject.anzsrc:toa.skos:prefLabel
```
    Purpose / Notes The ANZSRC 'Type of Research Activity' for this research project.   Internal Usage None   ReDBox DMPT Forms "Type of activity": Optional
 This is implemented as a select control populated by a static list held locally in ReDBox. Also used in Collection forms   Maps to Collection form field:  ```
dc:subject.anzsrc:toa.rdf:resource dc:subject.anzsrc:toa.skos:prefLabel
```
     
### []()FOR codes (dc:subject.anzsrc:for)
 
Represented by two underlying fields: ```
dc:subject.anzsrc:for.0.rdf:resource dc:subject.anzsrc:for.0.skos:prefLabel
```
    Purpose / Notes A List of ANZSRC codes for 'Field of Research' that the research project is associated with.   Internal Usage None   ReDBox DMPT Forms "FoR Codes": Mandatory
 This is implemented as a select control populated by a lookup against Mint.   Maps to Collection form field:  ```
dc:subject.anzsrc:for.0.rdf:resource dc:subject.anzsrc:for.0.skos:prefLabel
```
 
     
### []()SEO Codes (dc:subject.anzsrc:seo)
 
Represented by two underlying fields: ```
dc:subject.anzsrc:seo.0.rdf:resource dc:subject.anzsrc:seo.0.skos:prefLabel
```
    Purpose / Notes A List of ANZSRC codes for 'Socio-economic Objective' that the research project is associated with.   Internal Usage None   ReDBox DMPT Forms "SEO Codes": Optional
 This is implemented as a select control populated by a lookup against Mint.   Maps to Collection form field:  ```
dc:subject.anzsrc:for.0.rdf:resource dc:subject.anzsrc:for.0.skos:prefLabel
```
     
## []()People
 
### []()Principal investigator (locrel:rth.foaf:Person)
 
This element is composed of 6 fields: ```
locrel:rth.foaf:Person.dc:identifier locrel:rth.foaf:Person.foaf:name locrel:rth.foaf:Person.foaf:title locrel:rth.foaf:Person.foaf:givenName locrel:rth.foaf:Person.foaf:familyName locrel:rth.foaf:Person.foaf:email
```
    Purpose / Notes A list of collaborators involved in the project   Internal Usage None   ReDBox DMPT Forms "Primary investigator": Mandatoy   Maps to Collection form field:    Collection field Source   dc:creator.foaf:Person.#.dc:identifier locrel:rth.foaf:Person.dc:identifier    dc:creator.foaf:Person.#.foaf:name  locrel:prc.foaf:Person.foaf:name    dc:creator.foaf:Person.#.foaf:title locrel:prc.foaf:Person.foaf:title   dc:creator.foaf:Person.#.foaf:givenName locrel:prc.foaf:Person.foaf:givenName   dc:creator.foaf:Person.#.foaf:familyName locrel:prc.foaf:Person.foaf:familyName   dc:creator.foaf:Person.#.foaf:email  locrel:prc.foaf:Person.foaf:email   dc:creator.foaf:Person.#.redbox:isPrimaryInvestigator true      
### []()Collaborators (locrel:clb.foaf:Person)
 
This element is composed of 6 fields: ```
locrel:clb.foaf:Person.dc:identifier locrel:clb.foaf:Person.foaf:name locrel:clb.foaf:Person.foaf:title locrel:clb.foaf:Person.foaf:givenName locrel:clb.foaf:Person.foaf:familyName locrel:clb.foaf:Person.foaf:email
```
    Purpose / Notes A list of collaborators involved in the project   Internal Usage None   ReDBox DMPT Forms "Collaborators": Optional, list   Maps to Collection form field:   Collection field Source   dc:creator.foaf:Person.#.dc:identifier locrel:clb.foaf:Person.dc:identifier    dc:creator.foaf:Person.#.foaf:name  locrel:clb.foaf:Person.foaf:name    dc:creator.foaf:Person.#.foaf:title locrel:clb.foaf:Person.foaf:title   dc:creator.foaf:Person.#.foaf:givenName locrel:clb.foaf:Person.foaf:givenName   dc:creator.foaf:Person.#.foaf:familyName locrel:clb.foaf:Person.foaf:familyName   dc:creator.foaf:Person.#.foaf:email  locrel:clb.foaf:Person.foaf:email   dc:creator.foaf:Person.#.redbox:isPrimaryInvestigator false      
### []()Data manager (locrel:prc.foaf:Person)
 
This element is composed of 6 fields: ```
locrel:prc.foaf:Person.dc:identifier locrel:prc.foaf:Person.foaf:name locrel:prc.foaf:Person.foaf:title locrel:prc.foaf:Person.foaf:givenName locrel:prc.foaf:Person.foaf:familyName locrel:prc.foaf:Person.foaf:email
```
    Purpose / Notes The primary data manager and first contact point for data access   Internal Usage None   ReDBox DMPT Forms "Data manager": Mandatory   Maps to Collection form field: ```
Straight mapping: locrel:prc.foaf:Person.dc:identifier locrel:prc.foaf:Person.foaf:name locrel:prc.foaf:Person.foaf:title locrel:prc.foaf:Person.foaf:givenName locrel:prc.foaf:Person.foaf:familyName locrel:prc.foaf:Person.foaf:email
```
    
### []()Supervisor (swrc:supervisor.foaf:Person)
 
This element is composed of 6 fields: ```
swrc:supervisor.foaf:Person.dc:identifier swrc:supervisor.foaf:Person.foaf:name swrc:supervisor.foaf:Person.foaf:title swrc:supervisor.foaf:Person.foaf:givenName swrc:supervisor.foaf:Person.foaf:familyName
```
```
swrc:supervisor.foaf:Person.foaf:email
```
    Purpose / Notes The primary supervisor. Mainly used in student-based projects.   Internal Usage None   ReDBox DMPT Forms "Supervisor": Optional   Maps to Collection form field:  ```
Straight mapping: swrc:supervisor.foaf:Person.0.dc:identifier swrc:supervisor.foaf:Person.0.foaf:name swrc:supervisor.foaf:Person.0.foaf:title swrc:supervisor.foaf:Person.0.foaf:givenName swrc:supervisor.foaf:Person.0.foaf:familyName
```
```
swrc:supervisor.foaf:Person.0.foaf:email
```
    
## []()Data storage
 
### []()Expected size (vivo:Dataset.dc:extent)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
"Expected size of data collected", Mandatory 
This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: dc:extent    
### []()Storage location (vivo:Dataset.dc:location.rdf:PlainLiteral)
    Purpose / Notes Indicates where the data is to be stored   Internal Usage None   ReDBox DMPT Forms 
"Storage location": Mandatory This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: None    
### []()Storage location - other note (vivo:Dataset.dc:location.skos:note)
    Purpose / Notes Provides additional information for the vivo:Dataset.dc:location field   Internal Usage None   ReDBox DMPT Forms 
"Storage location": Optional - used if "Other" is selected 
Textarea   Maps to Collection form field: None    
### []()Master version location (vivo:Dataset.dc:source.dc:location.rdf:PlainLiteral)
    Purpose / Notes Indicates where the master version of the data is stored   Internal Usage None   ReDBox DMPT Forms 
"Storage location": Optional This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: None    
### []()Master version location - other note (vivo:Dataset.dc:source.dc:location.skos:note)
    Purpose / Notes Provides additional information for the master version location   Internal Usage None   ReDBox DMPT Forms 
"Storage location": Optional - used if "Other" is selected 
Textarea   Maps to Collection form field: None    
## []()Ethics and sensitivities
 
### []()Ethics approval number (agls:policy.dc:identifier)
    Purpose / Notes Free text entry of an ethics approval code   Internal Usage None   ReDBox DMPT Forms 
"Ethics approval number", Optional 
Text box   Maps to Collection form field: 
skos:note.#.dc:description -  
"Ethics approval number: " + agls:policy.dc:identifier    
### []()Ethics considerations (agls:policy.skos:note)
    Purpose / Notes Allows the user to provide additional ethical information   Internal Usage None   ReDBox DMPT Forms 
Optional 
Text area   Maps to Collection form field: 
skos:note.#.dc:description -  
"Ethics note: " + agls:policy.dc:identifier    
### []()Type of sensitivity (agls:protectiveMarking.dc:type.*)
 
There are currently 4 sub-classes to select from: 
 * Commercially sensitive: agls:protectiveMarking.dc:type.redbox:CommerciallySensitive
 * Culturally sensitive: agls:protectiveMarking.dc:type.redbox:CulturallySensitive
 * Security classified: agls:protectiveMarking.dc:type.redbox:SecurityClassified
 * Non-public: agls:protectiveMarking.dc:type.redbox:NonPublic
     Purpose / Notes Allows the user to select one or more sensitivity options for the data   Internal Usage None   ReDBox DMPT Forms 
"Type of sensitivity", optional 
Set of check boxes - list populated by a static list held locally in ReDBox.   Maps to Collection form field: 
skos:note.#.dc:description 
"Sensitivity: " + display version of each selected agls:protectiveMarking.dc:type.* 
(May need 1 note per sensitivity but it'd be good to have them all together    
### []()Sensitivity note (agls:protectiveMarking.skos:note)
    Purpose / Notes Allows the user to supply further sensitivity information   Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: 
skos:note.#.dc:description -  
"Sensitivity note: " + agls:protectiveMarking.skos:note    
## []()Ownership, licensing and IP
 
### []()Country of collection (dc:coverage)
    Purpose / Notes One or more countries in which the data will be collected   Internal Usage None   ReDBox DMPT Forms 
"In which country will data be collected", Mandatory 
List 
Can use either the list of countries available in the existing GeoNames data in Mint or the MARC countries (http://id.loc.gov/vocabulary/countries)   Maps to Collection form field: None    
### []()Copyright and inteallectual property owners (dc:rightsHolder.dc:name)
    Purpose / Notes A drop-down of possible owners   Internal Usage None   ReDBox DMPT Forms 
"Copyright and intellectual property owners", optional 
This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: None    
### []()Copyright and intellectual property owners - other (dc:rightsHolder.dc:description)
    Purpose / Notes A drop-down of possible owners   Internal Usage None   ReDBox DMPT Forms 
"Copyright and intellectual property owners", optional 
This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: None    
### []()Contractual obligations (redbox:ContractualObligations)
    Purpose / Notes A drop-down of possible owners   Internal Usage None   ReDBox DMPT Forms 
"Information about contractual obligations that apply to this data", optional 
Textarea   Maps to Collection form field: 
skos:note.#.dc:description -  
"Contractual obligations: " + dc:license    
## []()Data structure, collection and analysis
 
### []()Metadata standards used (vivo:Dataset.redbox:MetadataStandard)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: None    
### []()Standards applied to the data structure (vivo:Dataset.redbox:DataStructureStandard)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: None    
### []()Data collection methodology (vivo:Dataset.redbox:DataCollectionMethodology)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: None    
### []()Software/equipment for collection (vivo:Dataset.redbox:DataCollectionResources)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: None    
### []()Software/equipment for analysis (vivo:Dataset.redbox:DataAnalysisResources)
    Purpose / Notes     Internal Usage None   ReDBox DMPT Forms 
Optional 
Textarea   Maps to Collection form field: None    
### []()Primary file format (vivo:Dataset.dc.format)
    Purpose / Notes Selection of file types   Internal Usage None   ReDBox DMPT Forms 
Optional 
Selection   Maps to Collection form field: None    
## []()Licensing and access
 
### []()Access (dc:accessRights)
    Purpose / Notes A pre-determined set of access rights   Internal Usage None   ReDBox DMPT Forms 
"Access", optional 
Radio buttons   Maps to Collection form field: dc:accessRights.skos:prefLabel    
Note: the field labelled "The data manager is recorded as" is purely a client side field displaying the value of locrel:prc.foaf:Person.foaf:name. 
### []()Licensing (dc:license)
 
Represented by two underlying fields:  ```
dc:license.skos:prefLabel dc:license.dc:identifier
```
    Purpose / Notes Allows the user to either select a known licence or enter one of their own   Internal Usage None   ReDBox DMPT Forms 
"Licensing", optional 
This is implemented as a select control populated by a static list held locally in ReDBox and used in the collection forms.   Maps to Collection form field: 
dc:license.skos:prefLabel 
dc:license.dc:identifier
### []()Rights (dc:RightsStatement)
   Purpose / Notes Allows the users to describe any rights asserted on the research   Internal Usage None   ReDBox DMPT Forms 
"Additional asserted statement of rights", optional 
Textarea   Maps to Collection form field: dc:accessRights.dc:RightsStatement.skos:prefLabel    
### []()Access notes (dc:rights.skos:note)
    Purpose / Notes Notes to further describe access options etc   Internal Usage None   ReDBox DMPT Forms 
"Other relevant information", optional 
Text area   Maps to Collection form field: skos:note.#.dc:description    
## []()Retention and disposal
 
### []()Application retention period (redbox:retentionPeriod.dc:date)
    Purpose / Notes A selection of pre-defined retenition periods   Internal Usage None   ReDBox DMPT Forms 
"Application retention period", optional 
This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: redbox:retentionPeriod    
### []()Justification for extended retention period (redbox:retentionPeriod.dc:date.skos:note)
    Purpose / Notes A selection of reasons for extended retention   Internal Usage None   ReDBox DMPT Forms 
"Justification for extended retention period", optional 
This is implemented as a select control populated by a static list held locally in ReDBox.   Maps to Collection form field: None    
### []()Earliest possible disposal date (redbox:disposalDate)
    Purpose / Notes Date picker that indicates the disopsal date   Internal Usage None   ReDBox DMPT Forms 
"Earliest possible disposal date", Optional 
Date picker   Maps to Collection form field: redbox:disposalDate