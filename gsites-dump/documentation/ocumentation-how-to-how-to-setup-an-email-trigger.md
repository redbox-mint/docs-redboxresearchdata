[[_TOC_]]


## []()Status

This document is in DRAFT.
## []()Introduction

Redbox 1.6.1 introduced the ability to react to record changes, in particular, by sending template-based email notifications. This how-to describes the vanilla configuration with the goal of helping you write your own custom configuration. This feature requires at least the following technical skills: 

1. Familiarity with the JSON format 
1. Familiarity with the system properties / variables in ReDBox (e.g. *${admin.email}*)
1. Familiarity with the [workflow configuration](documentation-how-to-setup-a-new-workflow) and/or if you are not using workflows, the ability to query SOLR directly in order to design a criteria.
1. Familiarity with SOLR query syntax

This how-to currently pertains to ReDBox 1.6.1. A future version of ReDBox will bring in further improvements.
## []()Background 

This feature is composed of 3 discrete steps: 


 1. Determine if any records meet the specified criteria.
 1. Send the email for each of the records. 
 1. Indicate that the records had been 'processed'.
This how-to focuses on the steps 1 and 2, as the implemented feature and configuration for step 3 is sufficient for most implementations.
## []()Starting with Version 1.6.1 

### []()Key files 


* Criteria configuration: home/process/newRecords.json
* Email configuration:home/process/emailer.json
* System configuration: `home/system-config.json`

### []()Vanilla Configuration


Criteria configuration is simply defined as:


``  ``{``
``  ``    ``"query":"workflow_id=\"dmpt\""``
``  }``


This specifies that we are interested in all records with the [workflow name](documentation-how-to-setup-a-new-workflow#TOC-Configuring-the-system) equal to "dmpt". Defining the query requires the [third and fourth skill](documentation-how-to-how-to-setup-an-email-trigger#TOC-Introduction) outlined above. Please take note that the current date and time consideration is automatically appended to the query and that the "*create_timestamp*" field is required for the query to successfully execute.



### []()Email configuration

Email configuration is defined as:





``{``
``    "host":"${smtp.host}",``
``    "port":"25",``
``    "username":"${admin.email}",``
``    "password":"",``
``    "tls":"true",``
``    "ssl":"true",``
``    "from":"${admin.email}",``
``    "to":"$piEmail",``
``    "subject":"New ReDBox record added: '$title'",``
``    "body":"Dear User,\n\nThis is a notification of a newly created record: '$title' on $createTime. \n\n ${server.url.base}$viewId/detail/$oid \n\nCheers,\nEmailNotifier",``
``    "vars":[``
``        "$oid",``
``        "$title",``
``        "$createTime",``
``        "$viewId",``
``        "$piEmail"``
``    ],``
``    "mapping": {``
``        "$oid":"id",``
``        "$title":"dc_title",``
``        "$createTime":"create_timestamp",``
``        "$viewId":"viewId",``
``        "$piEmail":"locrel:rth.foaf:Person.foaf:email"``
``    }``
``}``



### []()Configuration variables

Below is a table explaining the configuration values. Please take note that system variables are different from user-defined variables by the *${}* that encloses system variable names.


**Property  ** **Value(s)** hostSMTP server host. This could be a system variable.  portSMTP server port. This could be a system variable.  username SMTP account username. This could be a system variable.  passwordSMTP account password. This could be a system variable.  tlsWhether the server requires TLS (true or false). This could be a system variable.  sslWhether the server requires SSL (true or false). This could be a system variable.  from The sender email address. This could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  toRecipient email address. This could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  subjectThis could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  bodyThis could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".      varsArray of user-defined variables. These variables are used in the *from, to, subject *and *body *properties*. *For clarity, a "$" prefix on the name is encouraged. mappingJSON that defines the mapping between variables and the fields defined in the workflow. This is also where the [third required skill](documentation-how-to-how-to-setup-an-email-trigger#TOC-Introduction) comes in handy.



### []()Customising the configuration 

#### **[]()Step 1

Make sure highlighted configuration below is present in your system-config.json.






        "houseKeeping": {
                    "config": {
                        "quartzConfig": "${fascinator.home}/quartz.properties",
                        "desktop": true,
                        "frequency": "3600",
                        "jobs": [
                            {
                                "name": "alerts-poll",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/alerts.script",
                                "timing": "0 0/15 * * * ?"
                            },
 `{`
 `  "name": "transitionWorkflow",`
 `  "type": "external",`
 `  "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/transitionWorkflow.ajax",`
 `  "timing": "0 0/15 * * * ?"`
 `}``,`
                            {
                                "name": "process-set-notifyNewRecords",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/process/launch.script?processingSet=notifyNewRecords",
                                "timing": "0 0/15 * * * ?"
                            }` ` ` ``            `
 `    `` ]`
                    }
                }
#### **[]()Step 2

Design a criteria of your records. You will need the [third and fourth skills](documentation-how-to-how-to-setup-an-email-trigger#TOC-Introduction) outlined above. You will need to specify a valid SOLR query. You may want to test the query using the Solr admin interface if you have access. Once you are satisfied, update the "query" property of criteria configuration, making sure that it is [properly escaped](http://lucene.apache.org/core/2_9_4/queryparsersyntax.html#Escaping%20Special%20Characters).

#### **[]()Step 3

Modify the email configuration. Please note that as of 1.6.1. only one email can be sent out per email configuration. 



### []()Optional step:

If you need to manually process a particular set of records, just specify their record IDs in the criteria configuration, using the "*includeList*" array. On the next process execution, the records are included in the processing as if these satisfied the criteria. The "*includeList" *is also auto-populated when an exception occurs while a processing record. When the record is successfully processed, its ID is removed from the "*includeList*".




## []()Starting with Version 1.7:


### []()Key files 


* Main processing configuration: `home/process/processConfig.json`
* Criteria configuration file, as specified in the "config" entry.
* Email configuration:home/process/emailer.json
* System configuration: `home/system-config.json`

### []()Vanilla Configuration


The main processing configuration contains an array of criteria configuration in the form of a JSON objects, identified by the "id" property. The order of the entries is important. The following example snippet configures notifications when there are newly submitted records and when there are newly approved records.
```

[
    {
        "id":"notifyNewSubmission",
        "pre" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.RecordProcessor",
                "config":"${fascinator.home}/process/newRecords.json",
                "inputKey":"",
                "outputKey":"newOids"
            }
        ],
        "main" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.EmailNotifier",
                "config":"${fascinator.home}/process/emailer.json",
                "inputKey":"newOids",
                "outputKey":"failedOids"
            }
        ],
        "post" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.RecordProcessor",
                "config":"${fascinator.home}/process/newRecords.json",
                "inputKey":"failedOids",
                "outputKey":""
            }
        ]
    },
    {
        "id":"notifyApproval",
        "pre" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.RecordProcessor",
                "config":"${fascinator.home}/process/approvedRecords.json",
                "inputKey":"",
                "outputKey":"newOids"
            }
        ],
        "main" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.EmailNotifier",
                "config":"${fascinator.home}/process/emailer.json",
                "inputKey":"newOids",
                "outputKey":"failedOids"
            },
            { 
 "class":"com.googlecode.fascinator.portal.process.HomeInstitutionNotifier",
 "config":"${fascinator.home}/process/notification/homeInstitutions.json",
 "inputKey":"newOids",
 "outputKey":"failedOids"
 } 
        ],
        "post" : [
            { 
                "class":"com.googlecode.fascinator.portal.process.RecordProcessor",
                "config":"${fascinator.home}/process/approvedRecords.json",
                "inputKey":"failedOids",
                "outputKey":""
            }
        ]
    } 
]
```

As with 1.6.1, the criteria configuration is specified in the "config" entry file entry. You will need to design a valid Solr query to retrieve the set of records you are interested in.
Email configuration
Email configuration now has support for multiple emails to be sent per record processed. The following snippet contains configuration for each of the search criteria above. However, one of the config blocks, "notifyApproval" contains configuration to send 2 emails per record, with different set of content and/or recipients. 
```



```

{
    "host":"${smtp.host}",
    "port":"25",
    "username":"${admin.email}",
    "password":"",
    "tls":"true",
    "ssl":"true",
    "notifyNewSubmission" : [ 
        {
          "from":"${admin.email}",
          "to":"$piEmail",
          "subject":"Request submission acknowledgement: '$title'",
          "body":"$fname $lname,\n\nThis email acknowledges your request: '$title'. To view your request, use this link: ${server.url.base}$viewId/detail/$oid \n\nCheers,\nRequest Notifier",
          "vars":[
              "$oid",
              "$title",
              "$fname",
              "$lname", 
              "$viewId",
              "$piEmail"
          ],
          "mapping": {
              "$oid":"id",
              "$title":"dc_title",
              "$lname":"requester:familyName",
              "$fname":"requester:givenName", 
              "$viewId":"viewId",
              "$piEmail":"requester:email"
          }
      },
      {
          "from":"${admin.email}",
          "to":"${admin.email}",
          "subject":"Approval needed - New Request submission: '$title'",
          "body":"Reviewer,\n\nA new submission is waiting for your approval: '$title'. To view this request, use this link: ${server.url.base}$viewId/detail/$oid \n\nCheers,\nRequest Notifier",
          "vars":[
              "$oid",
              "$title",
              "$viewId" 
          ],
          "mapping": {
              "$oid":"id",
              "$title":"dc_title",
              "$viewId":"viewId"
          }
      }
    ],
    "notifyApproval" : [ 
        {
          "from":"${admin.email}",
          "to":"$piEmail",
          "subject":"Request approved: '$title'",
          "body":"$fname $lname,\n\n Your request: '$title' has been approved. To view your request, use this link: ${server.url.base}$viewId/detail/$oid \n\nCheers,\nRequest Notifier",
          "vars":[
              "$oid",
              "$title",
              "$fname",
              "$lname", 
              "$viewId",
              "$piEmail"
          ],
          "mapping": {
              "$oid":"id",
              "$title":"dc_title",
              "$lname":"requester:familyName",
              "$fname":"requester:givenName", 
              "$viewId":"viewId",
              "$piEmail":"requester:email"
          }
      },
      {
          "from":"${admin.email}",
          "to":"${admin.email}",
          "subject":"Request '$title' has been approved.",
          "body":"Admin,\n\nA request has been approved: '$title'. To view this request, use this link: ${server.url.base}$viewId/detail/$oid \n\nCheers,\nRequest Notifier",
          "vars":[
              "$oid",
              "$title",
              "$viewId" 
          ],
          "mapping": {
              "$oid":"id",
              "$title":"dc_title",
              "$viewId":"viewId"
          }
      }
    ] 
}
```

```


### []()Configuration variables

Below is a table explaining the configuration values. Please take note that system variables are different from user-defined variables by the *${}* that encloses system variable names.


**Property  ** **Value(s)** hostSMTP server host. This could be a system variable.  portSMTP server port. This could be a system variable.  username SMTP account username. This could be a system variable.  passwordSMTP account password. This could be a system variable.  tlsWhether the server requires TLS (true or false). This could be a system variable.  sslWhether the server requires SSL (true or false). This could be a system variable.  from The sender email address. This could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  toRecipient email address. This could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  subjectThis could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".  bodyThis could be a system variable. This could also be any user-defined variable. Please see "vars" and "mapping".      varsArray of user-defined variables. These variables are used in the *from, to, subject *and *body *properties*. *For clarity, a "$" prefix on the name is encouraged. mappingJSON that defines the mapping between variables and the fields defined in the workflow. This is also where the [third required skill](documentation-how-to-how-to-setup-an-email-trigger#TOC-Introduction) comes in handy.



### []()Customising the configuration

#### **[]()Step 1

As with 1.6.1, make sure highlighted configuration below is present in your system-config.json.






        "houseKeeping": {
                    "config": {
                        "quartzConfig": "${fascinator.home}/quartz.properties",
                        "desktop": true,
                        "frequency": "3600",
                        "jobs": [
                            {
                                "name": "alerts-poll",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/alerts.script",
                                "timing": "0 0/15 * * * ?"
                            },
 `{`
 `  "name": "transitionWorkflow",`
 `  "type": "external",`
 `  "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/transitionWorkflow.ajax",`
 `  "timing": "0 0/15 * * * ?"`
 `}``,`
                            {
                                "name": "process-set-notifyNewRecords",
                                "type": "external",
                                "url": "http://localhost:${jetty.port}/redbox/default/process/launch.script?processingSet=notifyNewRecords",
                                "timing": "0 0/15 * * * ?"
                            }` ` ` ``            `
 `    `` ]`
                    }
                }
#### **[]()Step 2

Design a criteria of your records. You will need the [third and fourth skills](documentation-how-to-how-to-setup-an-email-trigger#TOC-Introduction) outlined above. You will need to specify a valid SOLR query. You may want to test the query using the Solr admin interface if you have access. Once you are satisfied, update the "query" property of criteria configuration, making sure that it is [properly escaped](http://lucene.apache.org/core/2_9_4/queryparsersyntax.html#Escaping%20Special%20Characters).

#### **[]()Step 3

Modify the email configuration. As with criteria, make sure this is properly escaped.



### []()Optional step:

If you need to manually process a particular set of records, just specify their record IDs in the criteria configuration, using the "*includeList*" array. On the next process execution, the records are included in the processing as if these satisfied the criteria. The "*includeList" *is also auto-populated when an exception occurs while a processing record. When the record is successfully processed, its ID is removed from the "*includeList*".





### []()Other resources


* [Setting up a workflow](documentation-how-to-setup-a-new-workflow)

### []()