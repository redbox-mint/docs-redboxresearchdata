## []()Determining the package type

To create a record, the package type you'd like to create has to be specified. If you are not sure of the package types available, you can find a listing of them by using the API's [Server Information method](https://redbox.restlet.io/#operation_get_information_about_the_redbox_instance).
## []()Creating a new record

To create a new record, use the API's [create a new object method](https://redbox.restlet.io/#operation_create_a_new_redbox_object), supplying the desired package type for the record and the record metadata in the post body.
## []()Example (Curl)

### []()1. Get package type listing

 
**Request**

 





        curl -X GET -H `"Authorization: Bearer xxxxxxxxxxx"`  `"[https://localhost:9000/redbox/api/v1/info"](https://localhost:9000/redbox/api/v1/info)`
**Response**






        {
            `"code"``: ``"200"``,`
            `"institution"``: ``"University of Examples"``,`
            `"applicationVersion"``: ``"1.9"``,`
            `"packageTypes"``: [`
                `"default"``,`
                `"dataset"``,`
                `"dmpt"``,`
                `"self-submission"``,`
                `"simple"`
            `]`
        }
 
### []()2. Create a new record

**Request**






        curl -X POST -H `"Content-Type: application/json"` `-H ``"Authorization: Bearer xxxxxxxxxx"`  `-d '{`
            `"title"``: ``"Created via the API"``,`
            `"viewId"``: ``"default"``,`
            `"packageType"``: ``"dataset"``,`
            `"description"``: ``"A description"``,`
            `"redbox:newForm"``: ``"false"``,`
            `"redbox:formVersion"``: ``"1.8.1-SNAPSHOT"``,`
            `"dc:title"``: ``"Created via the API"``,`
            `"dc:type.rdf:PlainLiteral"``: ``""``,`
            `"dc:type.skos:prefLabel"``: ``""``,`
            `"dc:created"``: ``"2016-07-29"``,`
            `"dc:modified"``: ``""``,`
            `"xmlns:dc"``: ``"[http://dublincore.org/documents/2008/01/14/dcmi-terms/"](http://dublincore.org/documents/2008/01/14/dcmi-terms/)``,`
            `"xmlns:foaf"``: ``"[http://xmlns.com/foaf/spec/"](http://xmlns.com/foaf/spec/)``,`
            `"xmlns:anzsrc"``: ``"[http://purl.org/anzsrc/"](http://purl.org/anzsrc/)``,`
            `"metaList"``: [`
                `"dc:title"``,`
                `"dc:type.rdf:PlainLiteral"``,`
                `"dc:type.skos:prefLabel"``,`
                `"dc:created"``,`
                `"dc:modified"``,`
                `"dc:language.dc:identifier"``,`
                `"dc:language.skos:prefLabel"``,`
                `"redbox:formVersion"``,`
                `"redbox:newForm"``,`
                `"dc:coverage.vivo:DateTimeInterval.vivo:start"``,`
                `"dc:coverage.vivo:DateTimeInterval.vivo:end"``,`
                `"dc:coverage.redbox:timePeriod"``,`
                `"dc:coverage.vivo:GeographicLocation.1.dc:type"``,`
                `"dc:coverage.vivo:GeographicLocation.1.redbox:wktRaw"``,`
                `"dc:coverage.vivo:GeographicLocation.1.rdf:PlainLiteral"`
            `]`
        }' `"[http://localhost:9000/redbox/api/v1/object/dataset"](http://localhost:9000/redbox/api/v1/object/dataset)`
**Response**






        {
          `"code"``: ``"200"``,`
          `"oid"``: ``"4a7eb6df1b6aeca80dcc5257b1c56cda"`
        }