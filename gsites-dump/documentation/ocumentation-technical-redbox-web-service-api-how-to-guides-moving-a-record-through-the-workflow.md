## []()Workflow Stages Definition

## []()For records that contain a workflow, the stages defined for that workflow are described in the package configuration in home/harvest. See the [configuring a workflow](http://www.redboxresearchdata.com.au/documentation/how-to/setup-a-new-workflow#TOC-Configuring-the-system) section of the set up a new workflow documentation for an example of a definition.

## []()The workflow.metadata datastream

The workflow.metadata stream defines the current workflow state in JSON format. To modify the workflow state, you will need to retrieve this datastream and modify it's values. A sample of the datastream is below





        {
        "id"`: ``"dataset"``,`
        "step"`: ``"metadata-review"``,`
        "pageTitle"`: ``"Metadata Record"``,`
        "label"`: ``"Metadata Review"`
        }
## []()
Example (Curl)

### []()1. Get the workflow.metadata datastream

**Request**






        curl -X GET -H `"Content-Type: application/octet-stream"` `-H ``"Authorization: Bearer xxxxxxxxxxx"` `"[http://localhost:9000/redbox/api/v1/datastream/e0220ec80b52e04da72813801b934da8?datastreamId=workflow.metadata"](http://localhost:9000/redbox/api/v1/datastream/e0220ec80b52e04da72813801b934da8?datastreamId=workflow.metadata)`
**Response**






        {
            `"id"``: ``"dataset"``,`
            `"step"``: ``"investigation"``,`
            `"pageTitle"``: ``"Metadata Record"``,`
            `"label"``: ``"Investigation"`
        }
 
### []()2. Update the workflow.metadata datastream

**Request**






        curl -X POST -H `"Authorization: Bearer xxxxxxxxxxxxx"` `-H ``"Content-Type: multipart/form-data"` `-F ``"content=@workflow.metadata"` `"[http://localhost:9000/redbox/api/v1/datastream/e0220ec80b52e04da72813801b934da8?datastreamId=workflow.metadata"](http://localhost:9000/redbox/api/v1/datastream/e0220ec80b52e04da72813801b934da8?datastreamId=workflow.metadata)`
**Response**






        {
            `"code"``: ``"200"``,`
            `"oid"``: ``"e0220ec80b52e04da72813801b934da8"`
        }