This page contains a far more visual explanation of the [Curation process](documentation-system-overview-curating-linked-data) and [data model](documentation-system-overview-curating-linked-data-curation-data-model). It was written after the rest of the documentation in an attempt to create a new entry-point and/or supplemental information on what is really quite a dense topic.

#### **[]()Overview
At the very top level, the first choice implementers were faced with was 'How am I going to create persistent IDs for my objects?'... this is after all the core problem that the Curation Manager is wrapping itself around. The default setup has historically been for Mint to connect directly to the Handle network for curation, and ReDBox would integrate with VITAL, allowing the curation to occur external to the system.


[![Default setup](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670699765/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/01-default-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-01-default-curation-png?attredirects=0)(Click to enlarge)

In v1.2 the most common customisation that would be applied to this setup is to remove VITAL in ReDBox and replace with Handle integration instead:

[![Handle customisation](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670705485/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/02-handle-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-02-handle-curation-png?attredirects=0)(Click to enlarge)

However, until v1.3 there wasn't an easy way to build a test/demo system without Handle or VITAL both. With v1.3 however, we add the 'Local Curation' plugin which basically fakes the creation of a persistent ID based a template you provide in configuration:

[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670709716/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/03-local-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-03-local-curation-png?attredirects=0)
(Click to enlarge)

#### **[]()Objects - Starting Point
Zooming in however, to look at individual objects, it is perhaps useful to stop for a minute and take a look at what a DigitalObject actually looks like inside the system before any curation occurs. There is some background reading in the core Fascinator documentation that may be useful on '[The Life Cycle of a DigitalObject](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/object-life-cycle)'. I've taken the final setup of the sample media object in that document and converted it show what a ReDBox object would like in our system prior to curation:


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670715486/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/04-digital-object.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-04-digital-object-png?attredirects=0)(Click to enlarge)

It is beyond the scope of explaining all the various parts shown here (but hopefully the core documentation linked above is helpful there), but the more important area to pay attention to is the 'Properties' associated with this object, because this is the area our various curation plugins will be fiddling with.

#### **[]()Objects - Curation Examples
So this section simply contains a number of similar diagrams focusing on the specific property changes that occur with each curation plugin. Hopefully it will help to show that no matter what the plugin is doing outside the system, the interfaces within the system are very similar.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670720959/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/05--object-curation-handle.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-05--object-curation-handle-png?attredirects=0)(Click to enlarge)

So Handle (above) creates a new property on the object that the Curation Manager can see which contains the complete Handle URL. Local Curation (below) does basically the same thing, except it has create a String from a template in configuration to use as a persistent ID:


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670726892/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/06--object-curation-local.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-06--object-curation-local-png?attredirects=0)(Click to enlarge)

The (slightly) more complicated scenario is VITAL, because it has to occur in two steps... the first is to send the record to VITAL (through Fedora) as an inactive object, and record the Fedora PID we created:


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670732895/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/07--object-curation-vital.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-07--object-curation-vital-png?attredirects=0)(Click to enlarge)

But at this stage we don't have our Handle... it is VITAL's job to do this as a background process. So the system will wait and periodically check for updates on that PID until it finds a Handle, then store the result:


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670737138/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/08--object-curation-vital2.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-08--object-curation-vital2-png?attredirects=0)(Click to enlarge)

Exciting stuff, I know... but for here we probably don't need to see all those details, so let's 'zoom out' a little to a more abstract idea of some object. The samples below represent a ReDBox Collection and a Mint Party, both of them do not have a persistent ID yet (note the empty properties):


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670742234/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/09--objects-abstracted.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-09--objects-abstracted-png?attredirects=0)(Click to enlarge)

So from here, the truly interested can take a detour into a slightly more technical (but still very visual) [look at message flow](documentation-system-overview-curating-linked-data-curation-data-model-technical-digrams) through the system from the perspective of a single object. Or if the particulars don't interest you, the next section moves on to how curation events propagate through the network of objects across both systems.

#### **[]()Curating Linked Data
So the initial event is going to occur against the ReDBox Collection, since this is where data entry staff will be finishing the workflow and pushing Collections 'live'.


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670804577/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/19-linked-curation.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-19-linked-curation-png?attredirects=0)(Click to enlarge)

In our example, the Curation Manager is going to look through the form data from the Collection (follow it's configuration) and notice it has an identifier for a creator in Mint, and a Mint Activity as well as a source of grant funding. In response it will send Mint a couple of messages asking for those records to in-turn undergo curation, and also request a response when this completes. For now, the work in ReDBox is complete, since it will await a response.

When Mint receives these requests its own Curation Manager is going to look at the metadata for the two objects and start curating those. The Activity is fairly straight forward (in our example, anyway), but the Party record for the creator also has a Party record related to it (his Faculty). So a similar process continues and the Faculty Party object will start curating and the trickle effect continues on to the Faculty's related Party record for the University as a whole.

At this point, having reached the 'end' of the network, the Curation Manager finds no more links to pursue and will start sending response back 'upstream':


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1320670810831/documentation/system-overview/curating-linked-data/curation-data-model/curation-diagrams/20-curation-complete.png)
](documentation-system-overview-curating-linked-data-curation-data-model-curation-diagrams-20-curation-complete-png?attredirects=0)
(Click to enlarge)

These responses will contain all the newly curated persistent IDs, allowing each Object to update their metadata with a 'curated link' and in turn generate appropriate RIF-CS using externally visible persistent keys. Not diagrammed here is the final action; when the top-level Collection finally receives a curated PID from all downstream Objects it is going to send a general 'publish' message back downstream. This indicates that everything is ready to go and both ReDBox and Mint will release records to external parties via OAI-PMH. Until this 'publish' message is received, even a curated record would not be visible externally.