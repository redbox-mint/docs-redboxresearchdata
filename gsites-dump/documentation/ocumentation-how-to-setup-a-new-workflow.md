[[_TOC_]]

 
 
## []()Introduction
 
Version 1.6.1 of ReDBox introduced a new method for defining forms and workflows. These new forms can be seen in action in the "Researcher Dashboard" as the Data Management Planning Tool (DMPT) and the self-submission form for collections. A central design goal was to make it easier for sites to customise (completely redo) the forms as they are likely to need local decisions regarding the appropriate fields and display text. In order to achieve this goal, a number of new features have been put into place: 
 
* A declarative method for describing a form
 
 * (rather than requiring direct "hacking" of the form code itself)
 * A set of user interface widgets for use in the form definition
 * Workflow transitions:
 
 * Rather than a rich (complicated) workflow toolset we have opted for linear workflows that can then be transitioned to other workflows. 
 * For example, once the user completes the self-submission and the system moves the record into the main review forms.
 * Allow individual access permissions rather than the original role-based access provided in ReDBox
 
 * The system now allows for 1 user to have ownership (edit) access, and other specified users to have view access.
 * A PDF Transformer that takes an XHTML-format input and provides the user's data management plan in PDF format
 
 * The XHTML is generated using a Velocity template
 * Enhancements to the workflow configuration
  
This How-To provides an overview of the process for customising the DMPT and self-submission forms. It's a somewhat technical process so requires some technical skills. The information here is likely to also provide you a starting point for adding new forms and workflows to ReDBox and Mint. 
### []()Thanks!
 
We'd like to thank the following stakeholders for their assistance in this work: 
 
* Flinders University (lead)
 * Deakin University (stakeholder)
 * James Cook University (co-development)
  
 
## []()Notes

* As of 1.6.1, the main review workflow (the one that's always been provided in ReDBox) still uses the old form/workflow configuration so you can't use these instructions to edit those forms.

 * The ReDBox dev team really want to move these forms over to the new system so [watch this space](https://www.pivotaltracker.com/epic/show/631685)
* Much of the work here is configurable and this is best undertaken using [Institutional Builds](documentation-technical-institutional-builds). This allows you to locally define and configure your setup and avoids future updates ruining your hard work.
* To make this document more readable, I'll refer to our base directories as home, portal and server. In an institutional build you'll find these map to the following:

 * home: src/main/config/home
 * portal: src/main/config/portal
 * server: src/main/config/server
* Where this document describes a file in one of these locations, you can easily "localise" the file by creating a new version (with the path) in your institutional build. When you build your system, these local changes will overwrite the defaults.

 * For example, if you create src/main/config/home/form-configuration/dmptform.json if your institutional build, the one that is provided by ReDBox is overwritten by your local version

## []()Key directories/files
 
 
* Forms are configured in: `home/form-configuration/`
 * Language files can be found in: `home/language-files/`
 * Harvest rules files and scripts: `home/harvest/workflows`
 * The "Researcher Dashboard" portal: `portal/dashboard/dashboard`
 * DMPT PDF template: `home/templates/self-submission/selfSub.vm`
 * The display of a dmpt or self-submission collection uses files in:
 
 * `portal/dashboard/dashboard/display`
  * `portal/dashboard/dashboard/scripts/display`
 
## []()Getting to work
  
Don't let the "Key directories/files" section worry you too much - most of the customisation work is done in configuration-level files. 
### []()Configuring the system
 
There are a few items you'll need to add to system-config.json in order to get the forms up and running. The config segment below should appear in your configuration - handy if something doesn't appear to be working. Also check out our [sample config](https://github.com/redbox-mint/redbox-build-dev-local/blob/master/src/main/config/home/system-config.json). The role of these elements will be described in the rest of this document.



        {
        ...
        "transitionWorkflow": {
                "transitions": [
                    {
                        "name": "Researcher Record",
                        "from-workflow-id": "self-submission",
                        "from-workflow-step": "self-submission-submitted",
                        "to-workflow-id": "dataset",
                        "to-workflow-step": "metadata-review"
                    }
                ]
            },
        ...
        "houseKeeping": {
            "config": {
                ...
                "jobs": [
                    ...
                    {
                      "name": "transitionWorkflow",
                      "type": "external",
                      "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/transitionWorkflow.ajax",
                      "timing": "0 0/15 * * * ?"
                    },
                    ...
                ]
        },
        ...
        "transformerDefaults": {
            ...
            "jsonVelocityPDF": {
                "id": "jsonVelocityPDF",
                "sourcePayload": ".tfpackage",
                "templatesPath": "${fascinator.home}/pdf-templates",
                "checkForTFMETAProperty": "true",
                "TFMETAPropertyName": "generatePDF",
                "TFMETAPropertyValue": "true",
                "clearPropertyOnTransform": "true",
                "versioning": "true"
            },
            "dmpToSelfSub": {
                "id": "jsonVelocity",
                "sourcePayload": ".tfpackage",
                "templatesPath": "${fascinator.home}/templates/self-submission/selfSub.vm",
                "checkForTFMETAProperty": "true",
                "TFMETAPropertyName": "copyTFPackage",
                "TFMETAPropertyValue": "dmpToSelfSub",
                "clearPropertyOnTransform": "true",
                "transformSource": "true"
            }
            ...
        }, 
 
Configuring a workflow 
As in prior versions, the workflow configuration is provided in a harvest definition: 
 
* DMPT: `home/harvest/workflows/dmpt.json`
 * Self-submission: `home/harvest/workflows/self-submission.json`
 
The "stages" element in `dmpt.json` appears as: 
  
 
            `"stages"``: [` 
                `{` 
                    `"name"``: ``"dmpt-draft"``,` 
                    `"label"``: ``"Draft"``,` 
                    `"description"``: ``"Draft plans by researchers"``,` 
                    `"owner_edit_allowed"``: ``"true"``,` 
                    `"security"``: [``"admin"``],` 
                    `"visibility"``: [``"admin"``],` 
                    `"actions"`` :[ { ``"action-name"`` : ``"next"``,` 
                                   `"target-step"`` : ``"dmpt-complete"`` }]` 
                `},` 
                `{` 
                    `"name"``: ``"dmpt-complete"``,` 
                    `"label"``: ``"Complete"``,` 
                    `"description"``: ``"Records under investigation."``,` 
                    `"security"``: [ ``"admin"``],` 
                    `"visibility"``: [``"admin"``],` 
                    `"template"``: ``"workflows/simpleworkflow"` 
                `}` 
            `]`   
You can see that we have 2 stages: "dmpt-draft" and "dmpt-complete". The following options are provided: 
* `name`: provides the stage name
 * `label`: is displayed in the user interface
 * `description`: a basic description for the stage
 * `owner_edit_allowed`: true if the specified user can edit, false otherwise (default)
 * `security`: sets the role-based edit access
 * `visibility`: sets the role-bases view access
 * `template: `to take advantage of the configuration below, this should be set as "workflows/simpleworkflow". If you want to go it alone, this should reference the vm and associated python file to render your view.
 * `actions`: provides an array of objects, each with:
 
 * `action-name`: The action to take (usually this is "next")
  * `target-step`: The stage we expect to move to after the action
 
The security and visibility settings are the traditional role-based model used in ReDBox but, in terms of the DMPT, this isn't overly useful - a data plan is owned by an individual and not a role. This meant that a new option is needed (owner_edit_allowed) to indicate that, when the object is created, the user that created the object is given direct ownership. In the config above, those in the admin role can still manage the object (as you'd expect). The user can change ownership via the user interface but we won't go into that here. 

  
ReDBox workflows are simple things - they move forward in a linear fashion. There's no ability to setup complicated workflow logic such as conditionals so don't start planning in-depth flow charts. Instead, the transitions approach lets an object in a specific state move to another stage. More about this a bit later. 
#### **[]()A gotcha to watch for
 
Did you notice that the stage names use a <workflow>-<stage> format (e.g. dmpt-draft)? This is required as it's actually the stage names that are used by the system when handling workflows. Each stage should have a unique "name" property across the system (not just across a specific workflow). 
#### **[]()For the very technical
 
If you're wanting to dig further: 
 
* The `actions` property uses a script from The Fascinator: [https://github.com/the-fascinator/the-fascinator/blob/master/portal/src/main/config/portal/default/default/scripts/workflows/simpleworkflow.py#L44](https://github.com/the-fascinator/the-fascinator/blob/master/portal/src/main/config/portal/default/default/scripts/workflows/simpleworkflow.py#L44)
 * The `SimpleWorkflowHelper` class provides a number of services to the `simpleworkflow.py` script
 
 * Code: [https://github.com/the-fascinator/the-fascinator/blob/master/portal/src/main/java/com/googlecode/fascinator/portal/workflow/SimpleWorkflowHelper.java](https://github.com/the-fascinator/the-fascinator/blob/master/portal/src/main/java/com/googlecode/fascinator/portal/workflow/SimpleWorkflowHelper.java)
  * JavaDoc: [https://dev.redboxresearchdata.com.au/jenkins/view/Clean%20builds/job/The%20Fascinator%20-%20Clean%20Build/javadoc/?](https://dev.redboxresearchdata.com.au/jenkins/view/Clean%20builds/job/The%20Fascinator%20-%20Clean%20Build/javadoc/?)
 
Unfortunately, the template property inherits a somewhat confusing notion from the base workflow implementation. Whilst it's used in "dmpt-complete", it actually relates to the previous stage ("dmpt-draft"). Basically, it's used as a progression element that tells the workflow plugin what needs to be done to get to that stage.  
### []()Setting up the form fields
 
The workflow configuration describes the progression through which the object/record moves in the system - what's now needed is to map a form configuration to each stage so that you can define what the user sees. Take a look at `home/form-configuration/dmptform.json` and you'll find it starts with: 
 
 
        { 
            `"stages"``:{` 
                `"dmpt-draft"``:{` 
                    `"divs"``:[`   
The "`stages`" property consists of objects for each workflow stage. In the example above, we're looking at the start of the form config for the "dmpt-draft" stage. The "`divs`" array breaks up the form in a set of fields. Each `divs` elements can then be displayed using various layout mechanisms - we use a "wizard" approach but that could be customised (see the "For the very technical" subsection). 
Each element in "`divs`" provides a heading and an array of fields - for example: 
  
 
        { 
                            `"heading"``:``"Welcome"``,` 
                            `"fields"``:[`  
Just glancing at `dmptform.json` will reveal that there's a lot of options.  
A field is defined with properties such as: 
* `component-type`: sets the widget to use - examples are provided in the following subsections
 * `type`: used in widgets that can have a subtype of `component-type`
 * `content`: the text displayed in the widget
 * `help`: an additional help text displayed with the widget
* `field-name`: Used for a form input field
 
The following subsections take a look at possible fields. 
Note: Where you see odd text such as dmpt.heading.welcome, these will make more sense when you get the "Language files" section. 
#### **[]()Text and HTML
 
A heading is quite easy: 
 
 
        { 
          "component-type"`:``"text-block"``,` 
          "type"`:``"h2"``,` 
          "content"`:``"dmpt.heading.welcome"` 
        },   
A plain old paragraph: 
  
 
        { 
          `"component-type"``:``"paragraph"``,` 
          "content"`:``"dmpt.welcome.paragraph1"` 
        },  
An HTML label: 
 
        { 
          `"component-type"``:``"text-block"``,` 
          "type"`:``"label"``,` 
          "content"`:``"dmpt.field.label.dc-title"``,` 
          `"help"``:``"dmpt.field.help.dc-title"` 
        },  
You can also add straight HTML. The example below is basic but you'll notice in the configuration files native-html can be used to insert tricky layouts etc 
  
 
        { 
          "component-type"`: ``"native-html"``,` 
           `"content"``: ``"<hr>"` 
        },  
#### **[]()Text fields
 
A text input: 





        {
            "component-type":"text-input",
            "field-name":"dc:relation.bibo:Website",
            "label":"",
            "additional-classes":""
        },
 
The label property can be set for the text-input rather than have a separate label element.
A textarea: 

        {
            "component-type":"textarea",
            "field-name":"dc:description",
            "label":"",
            "content":"",
            "additional-classes":"mandatory"
        }, 
Some fields are repeatable - they allow the user to type an entry and add it to a list of values:

        {
            "component-type":"repeatable-text",
            "field-name":"foaf:fundedBy.foaf:Agent",
            "label":"",
            "additional-classes":""
        },
#### **[]()Date pickers
 
Date pickers are defined as text-input components with a dateYMD set for the "additional-classes" property.





        {
            "component-type":"text-input",
            "field-name":"dc:coverage.vivo:DateTimeInterval.vivo:start",
            "label": "",
            "additional-classes":"dateYMD"
        }, 
#### **[]()Lookups
 
The following segment provides a drop-down using a set of options from a JSON file (`portal/dashboard/dashboard/dmpt/data/projectActivityType.json`)



        {
            "component-type":"data-source-dropdown",
            "field-name":"dc:subject.anzsrc:toa.rdf:resource",
            "label":"",
            "field-name-label":"dc:subject.anzsrc:toa.skos:prefLabel",
            "sourceUrl":"dmpt/data/projectActivityType.json"
        },


These components can also use Mint as a source:



        {
            "component-type": "data-source-dropdown",
            "field-name": "dc:language.dc:identifier",
            "field-name-label": "dc:language.skos:prefLabel",
            "label": "",
            "sourceUrl": "proxyGet.script?ns=Languages&qs=count%3D9999",
            "listKey": "results",
            "labelKey": "dc:title",
            "idKey": "dc:identifier"
        }, 
#### **[]()Others
 
The People field is a tricky one - it's best to hit the mailing list if you're trying to hack it.


The Field or Research widget is a bit more involved and I'll just highlight it here rather than get stuck into it:



        {
            "component-type":"repeatable-anzsrc-selection",
            "label":"FoR code(s)",
            "fieldName":"dc:subject.anzsrc:for.0.rdf:resource",
            "fieldNameLabel":"dc:subject.anzsrc:for.0.skos:prefLabel",
            "sourceUrl":"proxyGet.script?ns=ANZSRC_FOR&qs=count%3D999%26level%3D",
            "dataLevel":"top",
            "mandatory":"false",
            "idKey":"rdf:about",
            "labelKey":"skos:prefLabel",
            "listKey":"results",
            "defaultValue":"skos:narrower",
            "anzsrcType":"for"
        }, 
#### **[]()Validation

You're likely to need to check some fields and this is where the "validation" property comes in:



        {
            "component-type":"text-input",
            "field-name":"dc:title",
            "label":"",
            "additional-classes":"",
            "validation": {
                            "save-rules": [
                                "required"
                            ],
                            "validation-id": "dc:title",
                            "focus-id": "dc:title",
                             "validation-messages":{
                                                    "required": "Project name is required"
                                                    }
                          }                          
        }, 

Validation is configured as follows:

* save-rules: this is a list of validation rules to check against a field on the form. We use [validatious](http://validatious.org/) as our framework to perform these actions and so far we just support the included [rules](http://validatious.org/download/validators). [Custom Validators](http://validatious.org/learn/features/core/custom_validators) are also possible.
* validation-id: the field to run the save-rules against.
* focus-id: the field to focus on when the user clicks on the arrow in the error box. Often as above, it will be the same as the validation-id but it may be necessary to be different if you are validating on a field that isn't directly editable by the user
* validation-messages: The message to show when one of your save-rules fails. The key in your key value pair should match a value in your save-rules list.

#### **[]()Controls

The transition-control component provides our form with functions to move to the next "div" or save



        {
            "component-type": "transition-control",
            "transitions": [
                `{ ``"name"``: ``"Back"`` }, { ``"name"``: ``"Next"``}` 
            ]
        },
        {
            "component-type": "transition-control",
            "transitions": [
                {
                    "name": "Save",
                    "form-submit-action": "save",
                    "type": "button"
                },
                {
                    "name": "Save and close",
                    "form-submit-action": "save",
                    "type": "button",
                    "close": true,
                    "closeUrl": "dmpt/details"
                }
            ]
        }


#### **[]()For the very technical
 
The current display format for the forms is based on a "wizard" display whereas the review workflow forms use a tab display. We intend to make this configurable in future versions but if you want to change it now, check the `portal/dashboard/dashboard/form-components/dmptform-footer.vm` template and you can edit it to use a tab display rather than `wizard.js`. 

  
The form components (component-type) use a set of velocity templates that deserve their own documentation but can be accessed at: 
 
* The Fascinator code: `[https://github.com/the-fascinator/the-fascinator/tree/master/portal/src/main/config/portal/default/default/form-components](https://github.com/the-fascinator/the-fascinator/tree/master/portal/src/main/config/portal/default/default/form-components)`
 * ReDBox code:` portal/dashboard/dashboard/form-components`
 
These components use a set of JQuery-based UI widgets called the JAFFA library: 
 
 
 * Available at [https://github.com/redbox-mint/jaffa/](https://github.com/redbox-mint/jaffa/)
  * ReDBox imports the library into: `portal/default/redbox/jaffa`
   
### []()Language files
 
Rather put the text (e.g. labels and help) directly into the user interface or form configuration, a language service allows you put all of your text into a properties file. The properties file consists of a series of key/value pairs (with each pair on a single line). 

  
In the last section, the form configuration featured 2 key elements (content and help) that refer to a language file entry rather than put the text directly into the config. For example: 
 
 
        "content"`:``"dmpt.field.label.dc-identifier"``,` 
        "help"`:``"dmpt.field.help.dc-identifier"`   

  
If you take a look in `home/language-files` directory you'll find a set of properties files, specifically one called `dmpt-help` and another `dmpt-form`. Whilst the two properties used above can be found in their pertinent file, the sample below combines the two: 
 
 
        #Project Identifier 
        dmpt.field.help.dc-identifier=`Enter` `a` `unique` `identifier` `for` `your` `research` `project.` 
  
        dmpt.field.label.dc-identifier=`Project` `ID` `(*)`   

  
As you can see, the key used in the form definition is provided in a properties file with a text value for use in the display. 

  
If you'd like to use different strings in your system, you'll need to: 
 
1. Copy the .properties file containing the text you want to change to the `home/language-files` directory in your institutional build
 1. Edit the text as required
   

  
The language service is enabled via the "services" array in system-config.json. There's not really much to configure here, just make sure your config features the segment below: 
 
 
        "services"`: [` 
                ... 
               ` ``{` 
                   ` ``"id"``:`` ``"languageService"``,` 
                   ` ``"className"``:`` ``"com.googlecode.fascinator.portal.services.impl.JavaPropertiesLanguageService"``,` 
                   ` ``"config"``: {` 
                       ` ``"propertiesDir"``:`` ``"${fascinator.home}/language-files/"` 
                   ` ``}` 
               ` ``},` 
              ... 
           ] 
### []()Transitions (self submission to main review workflow)
 
In the 1.6.1 version of ReDBox there is 1 transition that takes place:

1. The user completes a self-submission draft (stage: self-submission-draft) so it moves to the next stage (self-submission-submitted)
1. A housekeeping job picks up objects in the self-submission-submitted stage and "moves" them to the metadata-review stage (that's part of the main review workflow)

It's important to note that, with a transition, the object/record isn't copied/cloned - it's the same object/record, just in a new workflow stage. Because of the security setup in the main review workflow, the user can see their record but will no longer be able to edit it.


The record is able to transition easily as the self-submission forms and review forms use the same fields so no field mapping takes place.


Transitions are defined in the `transitionWorkflow` section of system-config.json. Below you can see the config for the situation described above (it's rather self-describing): 
 
 
        "transitionWorkflow"`: {` 
               ` ``"transitions"``: [` 
                   ` ``{` 
                       ` ``"name"``:`` ``"Researcher Record"``,` 
                       ` ``"from-workflow-id"``:`` ``"self-submission"``,` 
                       ` ``"from-workflow-step"``:`` ``"self-submission-submitted"``,` 
                       ` ``"to-workflow-id"``:`` ``"dataset"``,` 
                       ` ``"to-workflow-step"``:`` ``"metadata-review"` 
                   ` ``}` 
               ` ``]` 
           ` ``},`   
A [housekeeping job](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/portal/housekeeper) is configured to check for objects awaiting transition:

        {
          "name": "transitionWorkflow",
          "type": "external",
          "url": "http://localhost:${jetty.port}/redbox/default/hkjobs/transitionWorkflow.ajax",
          "timing": "0 0/15 * * * ?"
        },
### []()Cloning (DMPT to self-submission)

Once a user is happy with a data management plan, they can create a self-submission collection based on that plan. This is different to the transition just described as a copy needs to be made. The process also takes place as soon as the user clicks the action - it doesn't use the housekeeping system as the user will expect to see a result within moments.


The process for this cloning is:

1. The user clicks on a link to create a dataset based on their plan

 1. See `portal/dashboard/dashboard/display/package-dmpt/detail/preview/body.vm`
1. Javascript code then:

 1. Creates a new empty object
 1. Copies the data management plan object to the new object
1. A transformer runs over the new object and maps the metadata to the fields required by the self-submission form

The transformer is defined in system-config.json:




        "transformerDefaults": {
          "dmpToSelfSub": {
            "id": "jsonVelocity",
            "sourcePayload": ".tfpackage",
            "templatesPath": "${fascinator.home}/templates/self-submission/selfSub.vm",
            "checkForTFMETAProperty": "true",
            "TFMETAPropertyName": "copyTFPackage",
            "TFMETAPropertyValue": "dmpToSelfSub",
            "clearPropertyOnTransform": "true",
            "transformSource": "true"
          }
* The transformer being used is the jsonVelocity transformer. This basically uses the Velocity engine to crosswalk/map the metadata from a data management plan to a self-submission.

 * `templatesPath: ``home/templates/self-submission/selfSub.vm` defines this mapping 
 * `sourcePayload` determines which payload is to be transformed
* The remaining properties are controls to manage the process

### []()Change the data plan PDF

The PDF created for a data management plan uses a template (`home/pdf-templates/Data Management Plan.vm`). For the PDF transformer to work correctly, this template MUST create XHTML-compliant output.


The settings are defined in the transformerDefaults section of system-config.json:


        "transformerDefaults"`: {`
                ...
                `"jsonVelocityPDF"``: {`
                   ` ``"id"``:`` ``"jsonVelocityPDF"``,`
                   ` ``"sourcePayload"``:`` ``".tfpackage"``,`
                   ` ``"templatesPath"``:`` ``"${fascinator.home}/pdf-templates"``,`
                   ` ``"checkForTFMETAProperty"``:`` ``"true"``,`
                   ` ``"TFMETAPropertyName"``:`` ``"generatePDF"``,`
                   ` ``"TFMETAPropertyValue"``:`` ``"true"``,`
                   ` ``"clearPropertyOnTransform"``:`` ``"true"``,`
                   ` ``"versioning"``:`` ``"true"`
               ` ``},` 
* `sourcePayload`: which object payload to use in the transformation (this would need to change to metadata.json if used in Mint)
* `versioning`: If true, previous versions of the PDF are kept
* The `TFMetaProperty` settings are used to manage properties in the object control file (TF-OBJ-META) to make sure that PDFs are created when needed

## []()Other resources
 
 
* The Workflows Harvester: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/harvester/workflows-harvester](https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/harvester/workflows-harvester)
 * Applying branding to your ReDBox user interface: [Branding](documentation-system-administration-general-administration-branding)