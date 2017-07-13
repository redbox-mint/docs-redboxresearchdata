Similar to the first [Curation Diagrams page](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams), this document is an attempt to present information on the [curation data model](documentation-system-overview-curating-linked-data-curation-data-model) in a more visual fashion. This page however, has a very specific focus on the flow of messages (or tasks) to and from Objects in the system, and it is probably worth reading the [other document](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams) first.

#### **[]()Tasks
All of these graphics have been shrunk slightly to a standard size, simply to avoid this page become far too large. Click on any graphic for an enlarged version.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670749924/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/10-message-reharvest.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-10-message-reharvest-png?attredirects=0)
The very common '`reharvest`' task is used to tell the Curation Manager that the object needs to pass through the typical tool chain Transformers. It will immediately follow this up with a '`clear-render-flag`' task being sent back to the same object (as per the more thorough explanation in [the textual doco](documentation-system-overview-curating-linked-data-curation-data-model)).


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670755167/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/11-message-workflow.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-11-message-workflow-png?attredirects=0)
**ReDBox specific message:** Every time the workflow updates the underlying metadata (eg. the user edits data in a form) a '`workflow`' event comes into the Curation Manager for the object. Interally some logging and such will occur, but of external interest is the '`reharvest`' task sent back to the Object to ensure that Transformers are run updating XML renditions and such.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670762263/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/12-message-workflow-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-12-message-workflow-curation-png?attredirects=0)
**ReDBox specific message:** A '`workflow-curation`' event is sent to the Curation Manager whenever an Object reaches the 'live' stage of the workflow... on-screen, configuration typically relabels the 'live' stage as 'Published', but the underlying code tends to avoid this term because of its loaded meaning in the curation process. There are also some occasions when the '`workflow-curation`' event will be generated for other reasons (such as two ReDBox Collections that are curated and one of them in undergoing curation and wants to send a request to the other), but importantly, the Curation Manager will always be careful and double-check the workflow data to ensure the Object is in the 'live' stage before continuing. **IF** the Curation Manager sees the object is ready it will generate a '`curation-request`' against this object.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670767142/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/13-message-curation-request.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-13-message-curation-request-png?attredirects=0)
The '`curation-request`' task is used to notify an Object that it needs to undergo curation. It may be accompanied by metadata which creates relationship information, as well information on which Objects are interested in this Object and would like to be notified when curation completes. If configuration requires it the system could send an email to an admin at this stage and wait for them to action it. If this human intervention is not required however, the Curation Manager will immediately send a '`curation`' event back to the Object.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670772009/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/14-message-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-14-message-curation-png?attredirects=0)
The '`curation`' task represents the 'real' curation event. We've passed all checks and blocks and are now ready to send our Object through the curation Transformer configured for this system. We'll follow this up with a '`reharvest`' task (to update XML templates) and a '`curation-confirm`' task to validate the outcome.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670779159/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/15-message-curation-confirm.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-15-message-curation-confirm-png?attredirects=0)As mentioned above, '`curation-confirm`' needs to validate that curation did indeed occur. Assuming that it did curate (and appropriate errors are raised if it didn't) then the next step will be to send out '`curation-request`' tasks to all downstream Objects that the Curation Manager can see in the Object's metadata (eg. ReDBox would be send them to Mint Parties and Activities and such). The object is going to be sitting here 'pending' for a while, waiting for all of the downstream objects to return a '`curation-pending`' task containing their curated persistent IDs for use. If there are no such downstream relationships this step is just skipped and a '`curation-response`' is immediately sent, just like below.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670784570/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/16-message-curation-pending.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-16-message-curation-pending-png?attredirects=0)
So as each '`curation-pending`' task is received the Curation Manager is going to be assessing whether or not all of the downstream records have responded yet. As soon as the last Object sends its persistent ID back the 'parent' Object is going to send itself a '`curation-response`' task.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670792660/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/17-message-curation-response.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-17-message-curation-response-png?attredirects=0)
When the '`curation-response`' task is received we know that everything relevant to the curation of this Object and all downstream relations is complete. This means that we can send a '`curation-pending`' task to any upstream Objects waiting on us. For ReDBox Collections, this is also the time to send themselves a '`publish`' task.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670798647/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/18-message-publish.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-18-message-publish-png?attredirects=0)
This is the end of the road. The '`publish`' task will set the '`published`' property to '`true`' for this Object, making it visible via the OAI-PMH feed in the '`published`' view. Similar '`publish`' tasks are also going to be sent to downstream relations at this point.