[[_TOC_]]



### []()dc:title


**Information:**

Purpose / NotesThe collection's Title.Internal UsageImportant as an internal ReDBox (Fascinator) field where only title and description are mandatory.ReDBox FormsBy default the ReDBox form calls this 'Title' and this is a mandatory, free-text field.Checkedv1.5
**Mappings:**

RIF-CS:collection > name@type="primary" > namePartDC:dc > titleMARC-XML:collection > record > datafield@tag="245" > subfield@code="a"
### []()dc:type

**Information:** Represented by two underlying fields:```
dc:type.rdf:PlainLiteral dc:type.skos:prefLabel
```

Purpose / NotesDefines the type of collection.Internal UsageUsed as a facet on searches. The 'prefLabel' is mainly intended for on-screen usage in the application.ReDBox Forms'Type' on-screen as well, and this is a required field. It is a controlled vocabulary based on valid strings accepted by RIF-CS.Checkedv1.5
**Mappings:**

RIF-CS:collection@typeDC:dc > typeMARC-XML:collection > record > datafield@tag="655" > subfield@code="a"
**History:**

* [r635](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=635): Added 'rdf:PlainLiteral' subfield to make the field functional as a JSON Object.

### []()dc:created

**Information:**

Purpose / NotesCreation date for the registry entry (ie. not the collection). Possible bug/improvement... [ issue #29 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=29).Internal UsageNoneReDBox FormsOptional. Labeled as 'Date Record Created' and implemented as a calendar control allowing granularity to the day (ie. no times). By default this will auto-populate with the current date.Checkedv1.5
**Mappings:**

RIF-CS:collection@dateAccessionedDC:dc > dateMARC-XML:collection > record > datafield@tag="260" > subfield@code="c"
Also forms the first 6 characters of 008 field as 'YYMMDD':
collection > record > controlfield@tag="008"
### []()dc:modified

**Information:**

Purpose / NotesModification date for the registry entry (ie. not the collection). Possible bug/improvement... [ issue #29 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=29).Internal UsageNone. Of particular note, this is **NOT** the modification date used by OAI-PMH feeds. Whenever the object is re-indexed ReDBox's internal modified date is reset (that is not form data).ReDBox FormsOptional. Labeled as 'Date Record Modified' and implemented as a calendar control allowing granularity to the day (ie. no times).Checkedv1.5
**Mappings:**

RIF-CS:collection@dateModifiedDC:UnmappedMARC-XML:Unmapped
### []()dc:language

Represented by two underlying fields:```
dc:language.dc:identifier dc:language.skos:prefLabel
```

**Information:**

Purpose / NotesIdentify the language of the metadata record (ie. not the language of the underlying collection). See [ issue #30 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=30) as well. Mappings below are not as good/simple as they should be.Internal UsageNoneReDBox FormsOptional. Labeled as 'Lanuage' and implemented as a select control populated by a lookup from the Mint server. 'dc:identifier' is a complete URI for the language (eg. [English](http://id.loc.gov/vocabulary/iso639-2/eng))Checkedv1.5
**Mappings:**

RIF-CS:Used on all tags as @xml:lang. Currently hardcoded to 'en' until [ issue #30 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=30) is addressed.DC:dc > language (truncated URI)MARC-XML:Under collection > record > datafield@tag="041" we have the subfields:
subfield@code="a" (truncated URI)
subfield@code="b" = "iso639-2b"

Finally, we also use the truncated URI in the 008 field:
collection > record > controlfield@tag="008"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Added 'dc:identifier' subfield.

### []()dc:coverage.vivo:DateTimeInterval

Represented by two underlying fields:```
dc:coverage.vivo:DateTimeInterval.vivo:start dc:coverage.vivo:DateTimeInterval.vivo:end
```

**Information:**

Purpose / NotesProvides precise definition of temporal coverage for the collection. Can be open ended on either end.Internal UsageNoneReDBox FormsOptional. Labeled as 'Date Coverage' and implemented as calendar controls allowing granularity to the day (ie. no times)Checkedv1.5
**Mappings:**

RIF-CS:collection > coverage > temporal > date@type="dateFrom"
collection > coverage > temporal > date@type="dateTo"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Subfields changed to use VIVO terminology.

### []()dc:coverage.redbox:timePeriod

**Information:**

Purpose / NotesProvides free-text definition of temporal coverage for the collection (eg. "World War II").Internal UsageNoneReDBox FormsOptional. Labeled as 'Time Period' in the form and allows free-text entry.Checkedv1.5
**Mappings:**

RIF-CS:collection > coverage > temporal > textDC:dc > subjectMARC-XML:collection > record > datafield@tag="653" > subfield@code="a"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:coverage.vivo:GeographicLocation

Represented by five underlying fields:```
dc:coverage.vivo:GeographicLocation.0.dc:type dc:coverage.vivo:GeographicLocation.0.gn:name dc:coverage.vivo:GeographicLocation.0.geo:long dc:coverage.vivo:GeographicLocation.0.geo:lat dc:coverage.vivo:GeographicLocation.0.dc:identifier dc:coverage.vivo:GeographicLocation.0.redbox:wktRaw
```

**Information:**

Purpose / NotesA list of any number of Geospatial locations in a variety of formats, representing the areas covered by the underlying dataset.Internal UsageNoneReDBox FormsOptional. Labeled as 'Geospatial Location'. The 'text' type is special and will perform an autocomplete lookup against the Mint server's [Geonames Solr index](https://sites.google.com/site/redboxmint/documentation/system-administration/administering-mint/geonamesdata) and populate the other fields with proper geo data (enough to build a DCMI Point). The other types (all of the geospatial formats accepted by RIF-CS) allow the user to directly input what will be sent to ANDS.NoteThe redbox:wktRaw sub-field contains the polygon information for the map drawing toolCheckedv1.5
**Mappings:**

RIF-CS:Everything is mapped under: collection > coverage > spatial
There are two scenarios:
1) When 'dc:type' is 'free' then build a DCMI Point as: spatial@type="dcmiPoint"
with a template like: 'name=${gn:name}; east=${geo:long}; north=${geo:lat};'
2) Otherwise dc:type will go into spatial@type and gn:name will be used as the text value of spatial.DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Subfields changed to use VIVO terminology, and top level field changed to be more semantic web ready.

### []()dc:description

Represented by three underlying fields:```
dc:description:0:text dc:description:0:type dc:description:0:shadow
```

**Information:**

Purpose / NotesA list of html tagged descriptions, types, and their 'escaped' descriptions of the ReDBox collection.Internal UsageImportant as an internal ReDBox (Fascinator) field where only title and description are mandatory.ReDBox FormsMandatory. Labeled as 'Description' and offers a wysiwyg textarea for html entry, with a hidden escaped text which follows the wysiwyg entry.Checkedv1.9
**Mappings:**

RIF-CS:
Uses 'shadow' in: collection > description@type=full/brief/noteDC:dc > descriptionMARC-XML:collection > record > datafield@tag="520" > subfield@code="a"
### []()
dc:relation.swrc:Publication

Represented by three underlying fields:```
dc:relation.swrc:Publication.0.dc:identifier dc:relation.swrc:Publication.0.dc:title dc:relation.swrc:Publication.0.skos:note
```

**Information:**

Purpose / NotesA list of links to any published material related to this collection.Internal UsageNoneReDBox FormsOptional. A simple UI allowing three free-text fields, one for the URL, one for title, and another for notes (which are not mapped anywhere).Checkedv1.5
**Mappings:**

RIF-CS:collection > relatedInfo@type="publication" > identifier@type="url"DC:dc > relationMARC-XML:collection > record > datafield@tag="856" > subfield@code="u"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.
* [r535](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=535): 'dc:title' subfield added.

### []()dc:relation.bibo:Website

Represented by three underlying fields:```
dc:relation.bibo:Website.0.dc:identifier dc:relation.bibo:Website.0.dc:title dc:relation.bibo:Website.0.skos:note
```

**Information:**

Purpose / NotesA list of links to any websites related to this collection.Internal UsageNoneReDBox FormsOptional. A simple UI allowing three free-text fields, one for the URL, one for title, and another for notes (which are not mapped anywhere).Checkedv1.5
**Mappings:**

RIF-CS:collection > relatedInfo@type="website" > identifier@type="url"DC:dc > relationMARC-XML:collection > record > datafield@tag="856" > subfield@code="u"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.
* [r535](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=535): 'dc:title' subfield added.

### []()dc:relation.vivo:Dataset

Represented by seven underlying fields:```
dc:relation.vivo:Dataset.0.dc:identifier dc:relation.vivo:Dataset.0.vivo:Relationship.rdf:PlainLiteral dc:relation.vivo:Dataset.0.vivo:Relationship.skos:prefLabel dc:relation.vivo:Dataset.0.dc:identifier dc:relation.vivo:Dataset.0.skos:note dc:relation.vivo:Dataset.0.redbox:origin dc:relation.vivo:Dataset.0.redbox:publish
```

**Information:**

Purpose / NotesA list of links to any data related to this collection.Internal UsageNoneReDBox FormsOptional. This is a complicated field provisioning a linked data relationship to other ReDBox datasets. Future work may involve a more global lookup against RDACheckedv1.5
**Mappings:**

RIF-CS:Unmapped... could use relatedObject([ issue #31 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=31))DC:dc > relationMARC-XML:collection > record > datafield@tag="856" > subfield@code="u"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Major overhaul of form functionality for linked data usage.
* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.
* [r535](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=535): 'dc:title' subfield added.

### []()dc:creator.foaf:Person

Represented by underlying fields:```
dc:creator.foaf:Person.0.dc:identifier dc:creator.foaf:Person.0.foaf:name dc:creator.foaf:Person.0.foaf:title dc:creator.foaf:Person.0.redbox:isCoPrimaryInvestigator dc:creator.foaf:Person.0.redbox:isPrimaryInvestigator dc:creator.foaf:Person.0.foaf:givenName dc:creator.foaf:Person.0.foaf:familyName dc:creator.foaf:Person.0.foaf.Organization.dc:identifier dc:creator.foaf:Person.0.foaf.Organization.skos:prefLabel [identifierText.0.creatorName.input]()
```
```


```

**Information:**

Purpose / NotesThis field holds a list of the creators of the work, and their association affiliations (eg. Faculty of XYZ)Internal UsageThis is a faceted field, on 'foaf:name'.ReDBox FormsMandatory. Labeled as 'Creators'. The UI here is one of the more complicated areas of the form. Free-text entries are possible, although they are typically used to fuel a search of Mint, which will return authoritative data to each field.Checkedv1.5
**Mappings:**

RIF-CS:Because Mint sends its own Parties (EAC-CPF) records to the NLA, we just need to link by identifier:

collection > relatedObject > key with the static addition of:
collection > relatedObject > relation@type="hasCollector"DC:dc > creator (full names, see [ issue #11 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=11) though)MARC-XML:The first creator is mapped to:
collection > record > datafield@tag="100" > subfield@code="a"

Additional creators are mapped to:
collection > record > datafield@tag="700" > subfield@code="a"
**Proposal for Future Work:** This field should become 'dc:creator.foaf:Agent' at the top level, with a new sub-field of 'dc:type' which can contain values like 'foaf:Person' or 'foaf:Organisation' to accommodate institutional authors.
There was some discussion around this, and the idea of using a value in 'dc:type' was not an ontology based decision, but one driven by the simplicity of designing templates and algorithms when mapping metadata.
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Some subfields changed to be more semantic web ready, 'dc:title' has been replaced with 'foaf:name' and 'foaf:title'.

### []()locrel:prc.foaf:Person

Represented by six underlying fields:```
locrel:prc.foaf:Person.dc:identifier locrel:prc.foaf:Person.foaf:name locrel:prc.foaf:Person.foaf:title locrel:prc.foaf:Person.foaf:givenName locrel:prc.foaf:Person.foaf:familyName locrel:prc.foaf:Person.foaf:email [pcName.identifierText]()
```

**Information:**

Purpose / NotesThe primary contact person for the collection.Internal UsageNoneReDBox FormsOptional. Labeled as 'Primary Contact', and also backed into a lookup against Mint, similar to creator, although less detailed.Checkedv1.5
**Mappings:**

RIF-CS:Because Mint sends its own Parties records to NLA, we just need to link by identifier:

collection > relatedObject > key with the static addition of:
collection > relatedObject > relation@type="isPrimaryContactFor"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready, 'dc:title' has been replaced with 'foaf:name' and 'foaf:title'.

### []()swrc:supervisor.foaf:Person

Represented by five underlying fields:```
swrc:supervisor.foaf:Person.0.dc:identifier swrc:supervisor.foaf:Person.0.foaf:name swrc:supervisor.foaf:Person.0.foaf:title swrc:supervisor.foaf:Person.0.foaf:givenName swrc:supervisor.foaf:Person.0.foaf:familyName [identifierText.0.supName.input]()
```

**Information:**

Purpose / NotesA list of supervisors associated with the data creation.Internal UsageNoneReDBox FormsOptional. Labeled as 'Supervisors', and also backed into a lookup against Mint, similar to creator, although less detailed.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:collection > record > datafield@tag="720" > subfield@code="a"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready, 'dc:title' has been replaced with 'foaf:name' and 'foaf:title'.

### []()dc:contributor.locrel:clb.0.foaf:Agent
```
Free text input captured in(but these are not used for mapping):
```
```
identifierText.0.collaborativeName.input
```

**Information:**

Purpose / NotesList the names of any other collaborators on the research project (both people and organizations).Internal UsageNoneReDBox FormsOptional. Labeled as 'Collaborators' and implemented as a basic list of free-text entry fields.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:subject.anzsrc:for

Represented by two underlying fields:```
dc:subject.anzsrc:for.0.rdf:resource dc:subject.anzsrc:for.0.skos:prefLabel
```

**Information:**

Purpose / NotesA List of ANZSRC codes for 'Field of Research' that the research project is associated with.Internal UsageThis is a faceted field, grouped with keywords.ReDBox FormsOptional. Labeled as 'Fields of Research'. This is implemented as a select control populated by a lookup against Mint.Checkedv1.5
**Mappings:**

RIF-CS:Only the actually code is sent to ANDS, not the label:
collection > subject@type="anzsrc-for". The termIdentifier could also be provided here ([ issue #108 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=108))DC:The labels only are mapped to 'dc:subject'MARC-XML:Both the code and label are mapped to here:
collection > record > datafield@tag="654" > subfield@code="a"

And the static string 'ANZSRC-FOR' is mapped here:
collection > record > datafield@tag="654" > subfield@code="2"
**History:**

* [r548](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=548): [ Issue #32 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=32) fixed. Now mapped to DC.

### []()dc:subject.anzsrc:seo

Represented by two underlying fields:```
dc:subject.anzsrc:seo.0.rdf:resource dc:subject.anzsrc:seo.0.skos:prefLabel
```

**Information:**

Purpose / NotesA List of ANZSRC codes for 'Socio-Economic Objective' that the research project is associated with.Internal UsageThis is a faceted field, grouped with keywords.ReDBox FormsOptional. Labeled as 'Socio-Economic Objective'. This is implemented as a select control populated by a lookup against Mint.Checkedv1.5
**Mappings:**

RIF-CS:Only the actually code is sent to ANDS, not the label:
collection > subject@type="anzsrc-seo". The termIdentifier could also be provided here ([ issue #108 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=108))DC:The labels only are mapped to 'dc:subject'MARC-XML:Both the code and label are mapped to here:
collection > record > datafield@tag="654" > subfield@code="a"

And the static string 'ANZSRC-SEO' is mapped here:
collection > record > datafield@tag="654" > subfield@code="2"
**History:**

* [r548](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=548): [ Issue #32 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=32) fixed. Now mapped to DC.

### []()dc:subject.vivo:keyword.0.rdf:PlainLiteral

**Information:**

Purpose / NotesA list of user entered keywords covering the research project's subject area.Internal UsageThis is a faceted field, grouped with ANZSRC codes.ReDBox FormsMandatory (at least one). Labeled as 'Keywords' and implemented as a free-text entry field. An autocomplete feature will try to suggest already existing keywords as you type.Checkedv1.5
**Mappings:**

RIF-CS:collection > subject@type="local"DC:dc > subjectMARC-XML:collection > record > datafield@tag="653" > subfield@code="a"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:subject.anzsrc:toa

Represented by two underlying fields:```
dc:subject.anzsrc:toa.rdf:resource dc:subject.anzsrc:toa.skos:prefLabel
```

**Information:**

Purpose / NotesThe ANZSRC 'Type of Research Activity' for this research project.Internal UsageNoneReDBox FormsOptional. Labeled as 'Type of Research Activity'. This is implemented as a select control populated by a static list held locally in ReDBox.
**Mappings:**

RIF-CS:Only the actually code is sent to ANDS, not the label:
collection > subject@type="anzsrc-toa"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()dc:accessRights

Represented by four underlying fields:```
dc:accessRights.skos:prefLabel dc:accessRights.dc:identifier dc:accessRights.dc:RightsStatement.skos:prefLabel dc:accessRights.dc:RightsStatement.dc:identifier dc:accessRightsType 
```

**Information:**

Purpose / NotesThese fields indicate any and all rights held over the data. Within the "Access Rights/Conditions" section the 'dc:accessRights.skos:prefLabel' is for the access rights text, whilst the 'dc:accessRights.dc:identifier' contains a URI for the linked access rights information. Within the "Rights" section, 'dc:accessRights.dc:RightsStatement.skos:prefLabel' is used for the rights text and 'dc:accessRights.dc:RightsStatement.dc:identifier' as a link to a rights statement (resource). Type indicates the access rights type.Internal UsageNoneReDBox FormsLabeled as 'Access/Rights' and implemented as free-text entry fields. Mandatory; at least one of the field sets (Access Rights or Rights) must be populated, but both are allowed. The type 'open' is selected on selection of an open licence.Checkedv1.9
**Mappings:**

RIF-CS:collection > rights > accessRights (dc:accessRights.skos:prefLabel) and collection > rights > accessRights@rightsType@rightsURI (dc:accessRights.dc:identifier) 
collection > rights > rightsStatement (dc:accessRights.dc:RightsStatement.skos:prefLabel) and collection > rights > rightsStatement@rightsURI(dc:accessRights.dc:RightsStatement.dc:identifier)DC:Not mappedMARC-XML:Not mapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.
* [r552](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=552): [ Issue #4 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=4) fixed addressing subtle changes to mappings.

### []()dc:license

Represented by two underlying fields:```
dc:license.skos:prefLabel dc:license.dc:identifier
```

**Information:**

Purpose / NotesSpecifies what license(s) the data is released under.Internal UsageNoneReDBox FormsOptional. Labeled 'License - ...'. At present the creative commons licenses are implemented as a select control populated from a data list held in ReDBox. The idea is that implementers will add to the list of possibilities as they will manage a set of licences. The older version of ReDBox provided a text box for free text entry but it is hoped that the linking model encourages the implementation of licence management.Checkedv1.5
**Mappings:**

RIF-CS:Only the license names are sent to ANDS:
collection > rights > licence (dc:license.skos:prefLabel) and collection > rights > licence@rightsUri (dc:license.dc:identifier)DC:dc > rights (only the URLs)MARC-XML:collection > record > datafield@tag="540" > subfield@code="a" (only the URLs)
**Thought for future work **Mint could provide an authority for licences.
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()dc:identifier

Represented by four underlying fields:```
dc:identifier.rdf:PlainLiteral dc:identifier.dc:type.rdf:PlainLiteral dc:identifier.dc:type.skos.prefLabel dc:identifier.redbox:origin
```

**Information:**

Purpose / NotesSpecifies the unique identifier in use for this record.Internal UsageNoneReDBox FormsMandatory. Labeled as 'Identifier'.

'redbox:origin' is a checkbox that allows the user to delegate this task to the system. It is checked by default.Checkedv1.5
**Mappings:**

RIF-CS:collection > identifier@type="..." (the type and identifier)DC:dc:identifierMARC-XML:collection > record > controlfield@tag="001"
**History:**

* [r535](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=535): Fields spun off to stand alone, to clarify the 'vivo:Location' field below.

**History:**

* [r635](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=635): Added 'rdf:PlainLiteral' subfield to both the identifier and the type to make the field functional as a JSON Object.



### []()dc:additionalidentifier

Represented by underlying fields:```
dc:additionalidentifier.rdf:PlainLiteral dc:additionalidentifier.dc:type.rdf:PlainLiteral dc:additionalidentifier.dc:type.skos.prefLabel
```

**Information:**

Purpose / NotesSpecifies additional identifiers in use for this record.Internal UsageNoneReDBox FormsOptional. Labeled as 'Identifier'.Checkedv1.9
**Mappings:**

RIF-CS:collection > identifier@type="..." (the type and identifier)>name@type="primary">namePartDC:
MARC-XML:

### []()bibo:Website.0.dc:identifier

**Information:**

Purpose / NotesAny URLs where the research data can be found.Internal UsageNoneReDBox FormsMandatory (sometimes). Within the Location section, either this field, or physical storage will be required.Checkedv1.5
**Mappings:**

RIF-CS:collection > location > address > electronic@type="url"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r635](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=635): Altered base field from 'vivo:Location' to 'bibo:Website' to make the field functional as a JSON Object.
* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.
* [r535](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=535): Identifier removed to become its own field (see 'dc:identifier' above), and 'url' has become a list.

### []()vivo:Location

Represented by two underlying fields:```
vivo:Location.vivo:GeographicLocation.gn:name vivo:Location.vivo:GeographicLocation.skos:note
```

**Information:**

Purpose / NotesThese fields describe the physical location of the underlying data.Internal UsageNoneReDBox FormsMandatory (sometimes). Within the Location section, either this field, or URL(s) will be required.Checkedv1.5
**Mappings:**

RIF-CS:collection > location > address > physical > addressPart@type="text"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()redbox:retentionPeriod

**Information:**

Purpose / NotesPeriod of time that the data must be kept in line with institutional/funding body retention policies.Internal UsageNoneReDBox FormsMandatory. Labeled as 'Retention Period'. Free-text entry field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:extent

**Information:**

Purpose / NotesDescription of the extent of material, such as number of files or physical items.Internal UsageNoneReDBox FormsMandatory. Labeled as 'Extent or Quantity', and implemented as a free-text entry field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()redbox:disposalDate

**Information:**

Purpose / NotesThe date this data can be disposed of.Internal UsageNoneReDBox FormsOptional. Labeled as 'Disposal Date' and implemented as a calendar control allowed granularity down to the day.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()locrel:own.foaf:Agent.0.foaf:name

**Information:**

Purpose / NotesA list of the names of Intellectual Property owners of this data (can be people or organizations).Internal UsageNoneReDBox FormsOptional. Labeled as 'Data Owner (IP)', and implemented as a list of free-text entry fields.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()locrel:dtm.foaf:Agent.foaf.name

**Information:**

Purpose / NotesPerson/institution identified as custodian of the data.Internal UsageNoneReDBox FormsOptional. Labeled as 'Data Custodian', and implemented as a basic free-text fieldCheckedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()foaf:Organization

Represented by two underlying fields:```
foaf:Organization.dc:identifier foaf:Organization.skos:prefLabel
```

**Information:**

Purpose / NotesThe organizational unit (ie. internal to your organization) affiliated with this research project.Internal UsageNoneReDBox FormsOptional. Labeled as 'Data Affiliation', and implemented as a select control populated by a look against Mint 'Groups'.Checkedv1.5
**Mappings:**

RIF-CS:Because Mint sends its own Parties records to ANDS, we just need to link by identifier:

collection > relatedObject > key with the static addition of:
collection > relatedObject > relation@type="isManagedBy"DC:dc > contributorMARC-XML:collection > record > datafield@tag="710" > subfield@code="b"
The template also maps your institution (by default 'Uni. of Newcastle') to:
collection > record > datafield@tag="710" > subfield@code="a"
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()foaf:fundedBy.foaf:Agent

Represented by two underlying fields:```
foaf:fundedBy.foaf:Agent.0.dc:identifier foaf:fundedBy.foaf:Agent.0.skos:prefLabel
```

**Information:**

Purpose / NotesA list of funding bodies for the research project funding.Internal UsageNoneReDBox FormsOptional. Labeled as 'Funding Body/s', and implemented as free-text entry fields with autocomplete lookups against Mint to retrieve identifiers.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()foaf:fundedBy.vivo:Grant

Represented by four underlying fields:```
foaf:fundedBy.vivo:Grant.0.redbox:internalGrant foaf:fundedBy.vivo:Grant.0.redbox:grantNumber foaf:fundedBy.vivo:Grant.0.dc:identifier foaf:fundedBy.vivo:Grant.0.skos:prefLabel
```

**Information:**

Purpose / NotesLinks the collection to a list of research projects (grants, or 'Activities' in Mint/ANDS terms).Internal UsageNoneReDBox FormsOptional. Labeled as 'Grant Numbers'. Implementation is a little complicated. If the grant is flagged as internal then simple free-text entry of the grant number and label is expected.

Otherwise the label field will be locked and typing anything in the grant number field will perform an autocomplete lookup against Mint (for both numbers and/or labels) and will populate the grant number, label and identifier upon selection.Checkedv1.5
**Mappings:**

RIF-CS:Because Mint sends its own Activity records to ANDS, we just need to link by identifier/key:

collection > relatedObject > key with the static addition of:
collection > relatedObject > relation@type="isOutputOf"DC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()swrc:ResearchProject.dc:title

**Information:**

Purpose / NotesThe title of the research project.Internal UsageNoneReDBox FormsOptional. Labeled as 'Project Title' and implement as a basic free-text field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()locrel:dpt.foaf:Person.foaf:name

**Information:**

Purpose / NotesThe name of the depositor.Internal UsageNoneReDBox FormsOptional. Labeled as 'Depositor' and implement as a basic free-text field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:SizeOrDuration

**Information:**

Purpose / NotesIntended specifically for the size of electronic data on disk. Used organizationally for scoping storage requirements.Internal UsageNoneReDBox FormsOptional. Labeled as 'Data Size' and implement as a basic free-text field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field name changed to be more semantic web ready.

### []()dc:Policy

**Information:**

Purpose / NotesA description of an any institutional policies associated with this data.Internal UsageNoneReDBox FormsOptional. Labeled as 'Institutional Data Management Policy' and implement as a basic free-text field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**Future thought** Could be managed through Mint.
**History:**

* r605: Field name changed to be more semantic web ready.

### []()redbox:ManagementPlan

Represented by two underlying fields:```
redbox:ManagementPlan.redbox:hasPlan redbox:ManagementPlan.skos:note
```

**Information:**

Purpose / NotesUsed to indicate whether a data management plan exists for this data.Internal UsageNoneReDBox FormsOptional. Labeled as 'Data Management Plan'. 'redbox:hasPlan' is a simple 'yes/no' radio control, and notes is a basic free-text field.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**Future thought** Could link to an attachment or Mint record.
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.

### []()skos:note

Represented by three underlying fields:```
skos:note.0.dc:created skos:note.0.foaf:name skos:note.0.dc:description
```

**Information:**

Purpose / NotesAdministrative notes for the record. [ Issue #20 ](http://www.redboxresearchdata.com.au/p/redbox-mint/issues/detail?id=20) outlines some required work regarding deletion of notes.Internal UsageNoneReDBox FormsOptional. Labeled as 'Notes' on its own tab. Description is the only input required, as the time of creation and the logged in user will be used to fill the other fields automatically.Checkedv1.5
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.
* [r539](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=539): Behavioural changes to form for clarity and usability.

### []()redbox:submissionProcess

Represented by eight underlying fields:```
redbox:submissionProcess.redbox:submitted redbox:submissionProcess.dc:date redbox:submissionProcess.dc:description redbox:submissionProcess.locrel:prc.foaf:Person.foaf:name redbox:submissionProcess.locrel:prc.foaf:Person.foaf:phone redbox:submissionProcess.locrel:prc.foaf:Person.foaf:mbox redbox:submissionProcess.dc:title redbox:submissionProcess.skos:note
```

**Information:**

Purpose / NotesProvides read-only access to submission data that comes from self-submission processes. eg. An academic submitting their data for ingest via the web portal.Internal UsageNoneReDBox FormsThis tab is all read-only, and simply displays the data for informational purposes to administrative staff.
**Mappings:**

RIF-CS:UnmappedDC:UnmappedMARC-XML:Unmapped
**History:**

* [r605](http://www.redboxresearchdata.com.au/p/redbox-mint/source/detail?r=605): Field names changed to be more semantic web ready.



### []()redboxAsLocationDefault

**Information:**

Purpose / NotesCorresponds to system-config rifcs key: "recordAsLocation", which picks whether to show the record as a rifcs location, and if so, the template to use (usually the record URL pattern).Internal UsageNoneReDBox FormsThe field exists under Management>Location
**Mappings:**

RIF-CS:collection > location > address > electronic@type="url" > valueDC:UnmappedMARC-XML:Unmapped