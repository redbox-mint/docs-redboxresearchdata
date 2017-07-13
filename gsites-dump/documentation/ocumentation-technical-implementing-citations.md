## []()Introduction

This page outlines proposed changes/additions to form data and forms in the workflow, required to support citation information in RIF-CS v1.3.
## []()RIF-CS XML

The XML requirements seems a bit strange to me, and I've found contradictions between [the guidelines](http://services.ands.org.au/documentation/rifcs/1.3/guidelines/rif-cs.html) and [the schema](http://services.ands.org.au/documentation/rifcs/1.3/schemadocs/registryObjects.html). For example the guildlines discuss '`<citationInfo>`' elements as: "*Use this element where it is not possible to provide a set of segmented citation parts in citationMetadata.*"
And yet the spec states that both '`<citationInfo>`' and '`<citationMetadata>`' are mandatory, as well has having citation sub parts that are mandatory and don't always make sense (to me at least) in the context of citing online datasets, such as '`<edition>`' and '`<placePublished>`'. The only optional elements at all are '`<publisher>'` and '`<date>`'.
Anyway, here's a block of sample XML we would plan to output ('`ANDSVocab`' is used wherever we would pull a value from one of ANDS' controlled vocabularies):




        <citationInfo>
            <fullCitation style="ANDSVocab">MANDATORY</fullCitation>
            <citationMetadata>
                <identifier type="ANDSVocab">MANDATORY</identifier>
                <contributor seq="1">
                    <namePart type="title">MANDATORY FIRST</namePart>
                    <namePart type="given">OPTIONAL LIST</namePart>
                    <namePart type="family">OPTIONAL LIST</namePart>
                </contributor>
                <contributor seq="2">
                    <namePart type="title">MANDATORY FIRST</namePart>
                    <namePart type="given">OPTIONAL LIST</namePart>
                    <namePart type="family">OPTIONAL LIST</namePart>
                </contributor>
                <title>MANDATORY</title>
                <edition>MANDATORY</edition>
                <publisher>OPTIONAL LIST</publisher>
                <publisher>OPTIONAL LIST</publisher>
                <placePublished>MANDATORY</placePublished>
                <date type="ANDSVocab">OPTIONAL LIST</date>
                <date type="ANDSVocab">OPTIONAL LIST</date>
                <date type="ANDSVocab">OPTIONAL LIST</date>
                <url>MANDATORY</url>
                <context>MANDATORY</content>
            </citationMetadata>
        </citationInfo>
## []()Form Fields

Below are some proposed form fields to add to our [current list](documentation-system-administration-administering-redbox-form-fields-collection-form-fields). As per usual I pulled RDF'ish syntaxes from any of the [namespaces we use](documentation-system-administration-administering-redbox-form-fields-collection-form-fields-remapping-work). Perhaps I'm incorrect, but they all seem fairly straight forward to me. Please advise if you can think of a better implementation, I'm not married to this.
The field that won't be obvious however is the use of '`dc:publisher`' to refer to the publisher, and then '`vivo:Publisher.vivo:Location`' to refer to place of publication. From my perspective this is simply to avoid using the same term for two different contexts when parsing the for data later, but I'd much prefer it if I could find an existing field that denoted 'place of publication' without resorting to this.




        dc:biblioGraphicCitation.redbox:sendCitation


        ## Full Citation - Mandatory
        dc:biblioGraphicCitation.skos:prefLabel
        dc:biblioGraphicCitation.dc:type.rdf:PlainLiteral
        dc:biblioGraphicCitation.dc:type.skos:prefLabel


        ## Part : Identifier - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.dc:identifier.rdf:PlainLiteral
        dc:biblioGraphicCitation.dc:hasPart.dc:identifier.dc:type.rdf:PlainLiteral
        dc:biblioGraphicCitation.dc:hasPart.dc:identifier.dc:type.skos:prefLabel


        ## Part : Contributors - Mandatory (List)
        dc:biblioGraphicCitation.dc:hasPart.locrel:ctb.0.foaf:title
        dc:biblioGraphicCitation.dc:hasPart.locrel:ctb.0.foaf:givenName
        dc:biblioGraphicCitation.dc:hasPart.locrel:ctb.0.foaf:familyName


        ## Part : Title - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.dc:title


        ## Part : Edition - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.dc:hasVersion.rdf:PlainLiteral


        ## Part :  Publisher - Optional
        dc:biblioGraphicCitation.dc:hasPart.dc:publisher.rdf:PlainLiteral


        ## Part :  Place of Publication - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.vivo:Publisher.vivo:Location


        ## Part :  Dates - Optional (List)
        dc:biblioGraphicCitation.dc:hasPart.dc:date.0.rdf:PlainLiteral
        dc:biblioGraphicCitation.dc:hasPart.dc:date.0.dc:type.rdf:PlainLiteral
        dc:biblioGraphicCitation.dc:hasPart.dc:date.0.dc:type.skos:prefLabel


        ## Part :  URL - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.bibo:Website.dc:identifier


        ## Part :  Context - Mandatory
        dc:biblioGraphicCitation.dc:hasPart.skos:scopeNote