The ReDBox Web Service API is a [RESTful web service](https://en.wikipedia.org/wiki/Representational_state_transfer) available in ReDBox and Mint from version 1.9. It exposes to clients the ability to:
1. Retrieve, create and update records
1. Search the solr index
1. Send messages to the message queues for asynchronous processing

## []()Concepts

ReDBox's storage is modeled on the [Fedora Commons 3 Digital Object Model](https://wiki.duraspace.org/display/FEDORA38/Fedora+Digital+Object+Model). Records are stored in Object containers made up of a number of metadata datastreams to describe the record.
To aid interaction with the storage, the API uses the following terminology:
**Object: **The record's object container. Containers are identified by a 16 character identifier
**Datastream: **Metadata about the record. An object typically contains many datastreams. They can be of any media type.
**Object Metadata: **A metadata datastream that contains control information about the object (e.g. the owner of the object, what indexation script (rules) to use etc). The datastream ID for this metadata is TF-OBJ-META.
**Record Metadata: **A json metadata datastream that contains the principle metadata for a record. The forms in ReDBox store all their metadata in this file. The datastream ID for this metadata is metadata.json in Mint and a file that ends in .tfpackage in ReDBox
**Package type: **The structure of a record is defined by its package type. Packages are described by their harvest configuration present in the application's home/harvest directory
## []()Authentication

The API is secured using a secret key passed in the header using the [OAuth 2.0 Bearer token](https://tools.ietf.org/html/rfc6750) standard on each request. For example using curl





        curl -X GET -H `"Authorization: Bearer xxxxxxxxxxx"`  `"[https://localhost:9000/redbox/api/v1/info"](https://localhost:9000/redbox/api/v1/info)`
Information on how to manage API keys for clients is available [here](https://qcifltd.atlassian.net/wiki/display/REDBOX/Web+Service+Client+Management)
## []()Working with the Web Service 

The API is specified using the [Swagger 2.0 Open API specification](http://swagger.io/specification/) and is available for download from here. Web documentation has been generated from this specification and can be viewed at [https://redbox.restlet.io](https://redbox.restlet.io/).
Using the Swagger specification, API client stubs can be generated with the [swagger-codegen](https://github.com/swagger-api/swagger-codegen) tool. During development, testing of the API was performed using the [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=en) chrome extension and the test suite can be found [here](https://raw.githubusercontent.com/redbox-mint-contrib/redbox-web-service/master/postman/ReDBox-Web-Service-API.postman_collection.json) for importation into your own installation. As well as being a useful tool for testing against the API, it also has the ability to generate API client stubs in some languages that aren't supported by swagger-codgen such as .NET.
For how to use the API to achieve common tasks, please see the [How-to section](https://qcifltd.atlassian.net/wiki/display/REDBOX/How-to+Guides)[.](https://qcifltd.atlassian.net/wiki/display/REDBOX/How-to+Guides)