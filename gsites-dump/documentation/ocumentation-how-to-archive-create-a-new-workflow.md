[[_TOC_]]


## []()Status

This document is in DRAFT. It is still being developed so expect it to be incomplete. I am also pasting in large code chunks - these will be setup as files or in a repo once sorted.
## []()Introduction

This how-to explores an approach to creating a new workflow within ReDBox. The new workflow will provide a set of self-submission forms for users.
## []()Background reading


* It'd be really useful to read the [Edit the ReDBox forms](documentation-how-to-archive-edit-the-redbox-forms) how-to first.
* https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/common-library/plugins/harvester/workflows-harvester
* https://dev.redboxresearchdata.com.au/jenkins/view/Clean%20builds/job/The%20Fascinator%20-%20Clean%20Build/javadoc/com/googlecode/fascinator/harvester/workflow/package-summary.html
* Knowing how the portal works will be extremely important:

 * https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/portal/jython-and-velocity
 * https://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/portal
* It may be useful to read up about the Security model: [https://sites.google.com/site/fascinatorhome/home/documentation/technical/investigations/security-model](https://sites.google.com/site/fascinatorhome/home/documentation/technical/investigations/security-model)

## []()Key files and folders


* portal/default/redbox/scripts/home.py: The basis for the home page display
* 


## []()Procedure

Before you start this procedure, please make sure .
### []()Step 1: Create a new role and user

For this how-to we'll setup a new role (submitter) and add a new user to that role (jim). 

1. Edit home/security/roles.properties and add the line "`submitter=jim,guest`"
1. (Re)Start ReDBox
1. Login as admin
1. In the Admin menu, go to Security
1. In the Users section, create a user. For this example, I'll refer to the username as "jim"


### []()Step 2: Setup the new workflow harvest config

The forms system in ReDBox is essentially a harvest in which the data is coming from a form rather than a file. Each harvest has an associated config (a json file) and rules (a py file)



In home/harvest/workflows create a new file called selfsubmit.json with the content below.

***



        {

            "harvester": {

                "type": "workflow-harvester",

                "workflow-harvester": {

                    "force-storage": "true"

                }

            },




            "transformer": {

                "curation": ["local"],

                "metadata": ["jsonVelocity"]

            },

            "curation": {

                "neverPublish": true,

                "alreadyCurated": false

            },




            "transformerOverrides": {

                "local": {

                    "template": "${server.url.base}published/detail/[[OID]]"

                }

            },




            "indexer": {

                "script": {

                    "type": "python",

                    "rules": "selfsubmit-rules.py"

                },

                "params": {

                    "repository.name": "ReDBox",

                    "repository.type": "Metadata Registry"

                }

            },




            "stages": [

                {

                    "name": "ss-draft",

                    "label": "Draft",

                    "description": "Records being created",

                    "security": ["submitter", "admin"],

                    "visibility": ["submitter", "admin"],

                    "template": "workflows/selfsubmit"

                },

                {

                    "name": "ss-completed",

                    "label": "Completed",

                    "description": "Completed records.",

                    "security": ["submitter", "admin"],

                    "visibility": ["submitter", "admin"],

                    "template": "workflows/selfsubmit"

                }

            ]

        }



***

Next, create another file in home/harvest/workflows called selfsubmit-rules.py. (This is more complex and I'm testing my code - will post when it's all good)


Add to system-config.json

        "packageTypes": {

                    "default": {

                        "jsonconfig": "packaging-config.json",

                        "packages-in-package": false

                    },

                    "dataset": {

                        "jsonconfig": "dataset.json",

                        "packages-in-package": false

                    },

                    "dataset": {

                        "jsonconfig": "selfsubmit.json",

                        "packages-in-package": false

                    }

                },




At this point you've setup the basic workflow and its rules file.


### []()Step 3: Setup the homepage for submitters

In redbox/portal/default/redbox/scripts/home.py, add the following code block to HomeData.__search at about line 90:

        # Load in the selfsubmit workflow        `selfSubmitWfConfig = JsonSimple(FascinatorHome.getPathFile("harvest/workflows/selfsubmit.json"))`
            selfSubmitJsonStageList = selfSubmitWfConfig.getJsonSimpleList(["stages"])
            selfSubmitStages = []
            for jsonStage in selfSubmitJsonStageList:
              wfStage = WorkflowStage(jsonStage, self.__steps)
              selfSubmitStages.append(wfStage)


            self.__selfSubmitStages = selfSubmitStages

Open portal/default/redbox/home.vm and change it to contain the code below:

***





        #set($pageTitle = "Home")






        #parseTemplate("elements/atom-link.vm")


        <h2 id="page-heading">


          <span>$page.portalName</span> $atomHtml


        </h2>






        #parseTemplate("menus/home-main-menu.vm")






        <div class="grid_12">


            #if($page.authentication.is_admin())


                #parseTemplate("dialogs/view-reharvest.vm")


                #parseTemplate("dialogs/view-delete.vm")


            #end


            #parseTemplate("dialogs/basic-search-form.vm")


        </div>


        <div class="clear"></div>






        <!-- IE7 needs a blank div here, class="prefix_4" doesn't work -->


        <div class="grid_4">&nbsp;</div>






        #if($page.authentication.is_logged_in())


          #if($page.authentication.has_role('submitter') or $page.authentication.is_admin())


            <div class="grid_6">


                #parseTemplate("home/self-submit-stages.vm")


            </div>


          #end


          #if($page.authentication.has_role('reviewer') or $page.authentication.has_role('librarian') or $page.authentication.is_admin())


            <div class="grid_6">


                #parseTemplate("home/stages.vm")


                ##parseTemplate("menus/home-new-items.vm")


            </div>


            <div class="grid_6">


                #parseTemplate("home/alerts.vm")


            </div>


            <div class="clear"></div>


          #end


        #end


***

In portal/default/redbox/home, create a file called "self-submit-stages.vm" with the following content:

***




        #set($menuTitle = "My submissions")


        #parseTemplate("wrapping/info-box-open.vm")


        <div class="block">


            <p>Select a stage to find all records in that stage.</p>


            <table>


                <thead>


                    <tr>


                        <th>#</th>


                        <th>Name</th>


                        <th>Records</th>


                    </tr>


                </thead>


                <tbody id="stages">


                #foreach($stage in $self.selfSubmitStages)


                    #set($count = $stage.count)


                    <tr id="wfStage$velocityCount">


                        <td>$velocityCount</td>


                        <td>


                            <img src="$portalPath/images/icons/wf_${stage.name}.png" />


                            <span class="titleText">


                            #if($count == 0)


                                <em>$stage.label</em>


                            #else


                                <a class="stagelink" href="$portalPath/stage?stage=$stage.label" rel="$stage.label">$stage.label</a>


                            #end


                            </span>


                            <br/>


                            <span>$stage.description</span>


                        </td>


                        <td class="count" style="text-align:center">$count</td>


                    </tr>


                #end


                </tbody>


            </table>


        </div>


        #parseTemplate("wrapping/info-box-close.vm")




***

You can copy some of the icon files in portal/default/redbox/images so that you have wf_ss-completed.png and wf_ss-draft.png.


Open portal/default/redbox/menus/home-main-menu.vm and replace the content with the following:

***




        <div class="grid_4">


            #set($menuTitle = "Options")


            #parseTemplate("wrapping/main-menu-open.vm")


            <ul class="menu">


                <li><a href="$portalPath/search">Browse records</a></li>


                #if($page.authentication.is_admin())


                <li><a id="reharvest-view" href="#" rel="$portalId">Reharvest view...</a></li>


                #if($portalId != $defaultPortal)


                <li><a id="delete-view" href="#" rel="$portalId">Delete view...</a></li>


                #end


                #end


                <li>


                    #if($page.authentication.is_logged_in())


                        #if($page.authentication.has_role('reviewer') or $page.authentication.has_role('librarian') or $page.authentication.is_admin())


                            <a id="new-record" href="#">Add a new dataset</a>


                        #elseif ($page.authentication.has_role('submitter') or $page.authentication.is_admin())


                            <a id="new-ss-record" href="#">Self-submit a new dataset</a>


                        #end


                    #else


                    <a id="notify-record" href="$portalPath/workflows/submission">Notify us about a dataset</a>


                    #end


                </li>


            </ul>


            #parseTemplate("wrapping/main-menu-close.vm")


        </div>


        #if($page.authentication.is_logged_in())


            #if($page.authentication.has_role('reviewer') or $page.authentication.has_role('librarian') or $page.authentication.is_admin())


                    <script type="text/javascript">


                    $(function() {


                        var jQ = $;


                        var processing = false;


                        jQ("#new-record").click(function() {


                            if (processing) return;


                            processing = true;


                            jQ("#new-record").append('<img class="right" src="$portalPath/images/icons/loading.gif" />');


                            jQ.ajax({


                                type: "POST",


                                url: "$portalPath/actions/packaging.ajax",


                                data: {


                                    ajax: 1,


                                    func: "create-new",


                                    packageType: "dataset",


                                    metaList: ["title", "description", "redbox:newForm", "redbox:formVersion"],


                                    title: "[Untitled]",


                                    description: "",


                                    "redbox:newForm": "true",


                                    "redbox:formVersion": "$systemConfig.getString("", "redbox.version.string")"


                                },


                                success: function(data) {


                                    if (data.status == "ok") {


                                        function redirect() {


                                            window.location.href = data.url;


                                        }


                                        setTimeout(redirect, 1500);


                                    } else {


                                        alert("Failed to create record!");


                                    }


                                },


                                error: function(xhr, status, e) {


                                    alert("Failed to create record!");


                                },


                                dataType: "json"


                            });


                            return false;


                        });


                    });


                </script>


            #elseif ($page.authentication.has_role('submitter') or $page.authentication.is_admin())


                <script type="text/javascript">


                    $(function() {


                        var jQ = $;


                        var processing = false;


                        jQ("#new-ss-record").click(function() {


                            if (processing) return;


                            processing = true;


                            jQ("#new-record").append('<img class="right" src="$portalPath/images/icons/loading.gif" />');


                            jQ.ajax({


                                type: "POST",


                                url: "$portalPath/actions/packaging.ajax",


                                data: {


                                    ajax: 1,


                                    func: "create-new",


                                    packageType: "selfsubmit",


                                    metaList: ["title", "description", "redbox:newForm", "redbox:formVersion"],


                                    title: "[Untitled]",


                                    description: "",


                                    "redbox:newForm": "true",


                                    "redbox:formVersion": "$systemConfig.getString("", "redbox.version.string")"


                                },


                                success: function(data) {


                                    if (data.status == "ok") {


                                        function redirect() {


                                            window.location.href = data.url;


                                        }


                                        setTimeout(redirect, 1500);


                                    } else {


                                        alert("Failed to create record!");


                                    }


                                },


                                error: function(xhr, status, e) {


                                    alert("Failed to create record!");


                                },


                                dataType: "json"


                            });


                            return false;


                        });


                    });


                </script>


            #end


        #end




***




When you log in as Jim you should see something like:




[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1362453387310/documentation/how-to/archive/create-a-new-workflow/Home%20-%20Everything%20-%20ReDBox-4.jpg)
](documentation-how-to-archive-create-a-new-workflow-Home%20-%20Everything%20-%20ReDBox-4-jpg?attredirects=0)



### []()Notes


* 


### []()Finishing up