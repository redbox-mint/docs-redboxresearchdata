[[_TOC_]]



## []()Introduction

This topic has arisen several times on the mailing list, in teleconferences, workshops and discussions, so this page is an attempt to provide some information on why things are the way they are.
Also, and perhaps more importantly, it is about offering systems administrators the chance to 'hack' some solutions to problems when they choose to break the rules.
## []()Community Decisions

It is worth keeping in mind that the current business logic implemented in ReDBox and Mint reflects the requirements of the University of Newcastle during initial development.
The community driven effort to evolve ReDBox, now and in future, will most certainly allow for this to change, but part of this discussion needs to be how we can work on a solution that offers the flexibility in terms of configuration and customisation, to appeal to a broad range of usage contexts. Not just on how it can be quickly changed to align with 'Purpose X'.
## []()Background

So the driver behind the current model is that any data/record/object which has been published to the world should (in theory) NEVER be deleted. During initial development, we focused on the ReDBox Collections and designed the form UI around this:

*  The 'Submit' tab offers a delete option for Collections right up until the last workflow stage.
*  Once an object is in this final stage ('Published') the delete feature is removed and replaced with the option to archive the object instead.

This method creates the data model for institutions to decide what they want to do next. The ReDBox software doesn't specifically deal with what an archived object should 'be', or how it should perform, but it allows you to configure your own behaviour. For example, you could offer a light-weight, public metadata sheet for the record, along with embargo information and a reason why it was taken down... or you could pretend it doesn't exist at all... your call.
When v1.2 came along we started dealing with curation relationships; where a Collection would be linked to records in Mint, such as a Party record for the creator. The same underlying logic was extended to this feature, in that deletion is not offered as a feature (publication is implied by the curation process), although the [https://sites.google.com/site/redboxmint/documentation/system-overview/curating-linked-data/curation-implementation underlying implementation] doesn't exclude future development of this. A 'delete-relationship' message would be a logical extension of the current curation model.
Whilst v1.2 hasn't offered deletion, it does have some issues (covered below) where normal data entry activity can create essentially broken data if used carelessly.
## []()Gaps & Problems

A couple of areas that could benefit from additional development work:
### []()Fascinator Deletion

The Fascinator platform offers 'admin' users a delete function. This wasn't designed into ReDBox/Mint per se, but it also wasn't disabled... If institutions want to remove the feature because of broad usage of the 'admin' user, or even just to avoid accidental usage, we should probably explored disabling this. Perhaps it could be turned off in configuration?
### []()Deleting Relationships

So there is a potential pitfall here. To explain just why, let's look at the data under two different scenarios, using the example of ReDBox Collection linked to a single Mint Party as the creator. These notes cover the broad ideas; specific information on where/how the data is stored is cover in the next section.

*  Non-curated Collections; basically Collections that are still in the workflow and haven't reached the 'Published' stage yet. These Collections only have references to the Party in a single location:



 1. The form data stores a non-curated, local identifier for the Mint Party.


*  Curated/Published Collections; these being Collections that have reached 'Published' and the Curation Manager has started building up relationship data for linked objects.



 1. The form data stores a non-curated, local identifier for the Mint Party.
 1. Relationship data will store the same non-curated identifier, and it will add to this a curated, publishable identifier when the Mint Curation Manager has finished its job and responds.
 1. The Party over in Mint is similarly going to have some Relationship data stored pointing back to this Collection.
 1. External systems, both on campus (like VITAL) and external (such as RDA/NLA) will have copies of this data (including any published relationships).

So the problem we run into is that the forms pre-date v1.2 (obviously) and ***what we probably should do is remove the ability for data entry staff to delete a link to a curated creator* **(or any other linked relations).
At present (using our above example), the creator could be removed from the form data, but the references in all other three locations will remain.
The business choice of when/if to delete data aside, another section below focuses on how a sysadmin could manually remove the data in the above bullet points.
## []()Manually Deleting Relationships

So as per above, removing a curated relationship requires careful deletion from several locations. Three inside ReDBox and Mint, and however many external systems.



* Form data: Typically this would be done by your data entry staff from inside the system... in fact this is recommended, since it will ensure appropriate data integrity for the list field (ie. if you delete the 4th of 5 creators it fixes the sequence).
* Relationship data (ReDBox): You need to edit the object on disk (you can find it by ID in the /storage path), focusing on the payload with a '`*.tfpackage`' extention. This JSON file will contain a top-level JSON object called '`relationships`' that you want to edit. Remove the reference to the creator you just removed from the form (by ID). So this example:





            "relationships": [
                {
                    "field": "dc:creator.foaf:Person.0.dc:identifier",
                    "authority": true,
                    "identifier": "redbox-mint.googlecode.com/parties/people/1241",
                    "relationship": "hasCollector",
                    "reverseRelationship": "isCollectorOf",
                    "broker": "tcp://localhost:9201",
                    "isCurated": true,
                    "curatedPid": "http://hdl.handle.net/10880/3"
                },
                {
                    "field": "dc:creator.foaf:Person.0.dc:identifier",
                    "authority": true,
                    "identifier": "redbox-mint.googlecode.com/parties/people/1238",
                    "relationship": "hasCollector",
                    "reverseRelationship": "isCollectorOf",
                    "broker": "tcp://localhost:9201",
                    "isCurated": true,
                    "curatedPid": "http://hdl.handle.net/10880/5"
                },
                {
                    "field": "dc:creator.foaf:Person.0.dc:identifier",
                    "authority": true,
                    "identifier": "redbox-mint.googlecode.com/parties/people/1236",
                    "relationship": "hasCollector",
                    "reverseRelationship": "isCollectorOf",
                    "broker": "tcp://localhost:9201",
                    "isCurated": true,
                    "curatedPid": "http://hdl.handle.net/10880/6"
                }
            ],




becomes:


            "relationships": [
                {
                    "field": "dc:creator.foaf:Person.0.dc:identifier",
                    "authority": true,
                    "identifier": "redbox-mint.googlecode.com/parties/people/1241",
                    "relationship": "hasCollector",
                    "reverseRelationship": "isCollectorOf",
                    "broker": "tcp://localhost:9201",
                    "isCurated": true,
                    "curatedPid": "http://hdl.handle.net/10880/3"
                },
                {
                    "field": "dc:creator.foaf:Person.0.dc:identifier",
                    "authority": true,
                    "identifier": "redbox-mint.googlecode.com/parties/people/1236",
                    "relationship": "hasCollector",
                    "reverseRelationship": "isCollectorOf",
                    "broker": "tcp://localhost:9201",
                    "isCurated": true,
                    "curatedPid": "http://hdl.handle.net/10880/6"
                }
            ],



*  Relationship data (Mint): So now we need to find our record '`redbox-mint.googlecode.com/parties/people/1238`' in Mint (a basic search in the web portal should suffice), and get its object ID. Using this we can edit the file in storage (Mint Parties use the '`metadata.json`' payload), as per above, and remove the backwards relationship with the Collection. This examples shows the case where it is the last relationship:





            "relationships": [
                {
                    "identifier": "http://hdl.handle.net/10880/4",
                    "curatedPid": "http://hdl.handle.net/10880/4",
                    "broker": "tcp://localhost:9101",
                    "isCurated": true,
                    "relationship": "isCollectorOf",
                    "uniqueString": "{\"identifier\":\"http:\\/\\/hdl.handle.net\\/10880\\/4\",\"curatedPid\":\"http:\\/\\/hdl.handle.net\\/10880\\/4\",\"broker\":\"tcp:\\/\\/localhost:9101\",\"isCurated\":true,\"relationship\":\"isCollectorOf\"}"
                }
            ]
becomes:


            "relationships": [
            ]



*  Once this data is removed, we need to regenerate each record's RIF-CS, so that the out-of-date '`relatedObject`' nodes are removed. The easiest option is to use the 'ReIndex' links that admins have in the web portal on the details screen for each object. Of course want to keep an eye on your system logs in case you mucked up the files in editing (eg. bad JSON syntax).
*  Externals systems are a little bit beyond our scope really, but you do need to address them. With the RIF-CS updated, anything downstream via OAI-PMH (like RDA) may be a non-issue, but systems like VITAL will need manual attention.