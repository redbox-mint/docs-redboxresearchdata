Unlike the [rest of the curation documentation](documentation-system-overview-curating-linked-data) this page is written from a purely technical perspective and is intended for a developer audience familiar with both ReDBox/Mint and the Fascinator platform on which they are built. For this reason there is a lot less 'hand holding' and certain assumptions are made about system knowledge and terminology without linking off to additional documentation. Having said that, if you are new to developing on this application and require any clarification just [ask on the mailing list](http://groups.google.com/group/redbox-repo), the brevity here is not an attempt to be dismissive of newcomers, just an attempt to avoid over-explanation for an audience of developers. 
#### **[]()The Way Things Were
 
The traditional Fascinator tool chain is implemented as a series of queue consumers in the Core library with an intrinsic knowledge of each other and a hardcoded framework for routing work based on the type of objects being ingested. It was also trying to solve a very different problem to what ReDBox and Mint are presented with. To summarise the setup: 
 * Each queue consumer would have a series of `Transformer`s wrapped in a `ConveyerBelt` class.
 * They ran objects through an `Indexer` after they had been transformed (although one queue allowed for this to be optional.
 * Some hardcoded messages were sent to the `Subscriber` for the purpose of audit logging and third party integration.
 * Routing logic in the conveyor belts was primitively configurable, but was always dictated by object MIME types.
 * The core 'problem' it was trying to solve was ingesting wide varieties of files en-mass from a system and running them through light (no transforming for unknown files), medium (basic documents) and heavy (video transcoding) transformers with priority based routing for efficient handling of the light vs. heavy workloads.
  
In earlier ReDBox work we had already run into issues with this tool chain when implementing VITAL integration. We wanted our workflow save events to trigger a trip through the tool chain (to get new RIF-CS templates) and then send our VITAL subscriber a message. However the hardcoded messages didn't contain the specific data we needed, and there wasn't an easy method of detecting that the tool chain had completed to then send the message ourselves. The end result was a very light, hacked together, version of the tool chain running at a couple of points in the ReDBox front-end. It wasn't particularly elegant, but it worked. 
#### **[]()Design Groundwork
 
The new solution we thrashed around and put forward in v1.2 was to start by adding a new queue consumer to the Fascinator core, called the `TransactionManagerQueueConsumer`. The idea of this message queue is to provide that same toolkit as the older queue consumers (although we did add some extra things based on what we've learned along the road), but to wrap the whole thing around a new type of plugin (the `TransactionManager`) designed to function as a 'brain' for the tool chain and provide complicated routing logic that specific implementations can replace as a plugin if required. 
The `TransactionManagerQueueConsumer` passes the message it receives directly to the `TransactionManager` without trying to to process it at all. The `TransactionManager` is expected to respond with a list of 'orders' for the queue consumer to carry out. The term 'order' is used throughout the code, but is of no particular importance to non-developers since users never see this stuff. The current list of orders that the `TransactionManagerQueueConsumer` understands are defined at the top of the class so that implementing `TransactionManager`s can access them for clarity of reference: 
 `public static enum OrderType {INDEXER, MESSAGE, SUBSCRIBER, TRANSFORMER}` 
The exact behaviour of each is outlined below, and they are all JSON objects:
  
* '`INDEXER`': Sends the indicated object '`oid`' through the Solr Indexer. The may have a '`forceCommit`' flag set, in which case a Solr commit will immediately follow.
 
 * '`MESSAGE`': Will send a message out via the `MessagingServices` utility. At the minimum a valid '`target`' is required and a JSON '`message`' object. An optional '`broker`' can be optionally provided to send the message to third party AMQ brokers (eg. between ReDBox and Mint).
 
 * '`SUBSCRIBER`': This is basically a specific version of a '`MESSAGE`' since the wrapped for the Subscriber plugins is just a queue consumer itself. So no '`target`' is needed, but a JSON '`message`' is required and so is an Object identifier ('`oid`') since the audit log Subscriber is going to require this anyway.
 
 * '`TRANSFORMER`': The most complicated order type, since it is replacing the work of most of the old tool chain; this order will ensure that valid configuration for the indicated object's data source is accessible in storage, retrieve and parse it so that the Transformer has it available upon execution... and of course run the Transformer. An '`oid`' is required, as is a '`target`' (expected to be the plugin ID of a configured Transformer), but a JSON '`config`' object is not required. The '`config`' object is replicating the old item level configuration used in the original tool chain, so the `TransactionManager` will need to get this ready as part of building the order. It is typical to have this config, but since it is not 100% required, we only throw a warning in the log and continue processing if it is absent. Implementers should return an empty JSON object in its place if you don't want to see the warnings in your log file.
 
 
In all of the cases above you will note the careful use of the phrase "the indicated object", as opposed to "this object". This is because each order could (in theory) relate to a different object. This is at odds with the typical thinking of the tool chain, where one object is considered to pass 'through' the tool chain, somewhat like you would imagine a physical object passing through a factory. The new setup however, allows for the possibility that a `TransactionManager` may decide to take broader action in response to an incoming message and allows each 'order' to be fully self contained in terms of which object it will be interacting with. 
At the moment, the Curation Manager (next section) in ReDBox does not make use of this functionality, but when designing a distributed curation process across two systems and many objects we wanted to allow for this flexibility. Developers should consider this option carefully in practice since you want to balance flexibility with code intuitiveness and ease of maintenance. Sending a message relating to Object A into a queue consumer and finding that it takes action against Object B is non-intuitive ***in the context of historic Fascinator behaviour***.
  
#### **[]()Curation Manager
 
The `CurationManager` plugins provided by both ReDBox and Mint are valid implementations of the `TransactionManager` interface and they make full use of the new design (you'd hope so, since it was built for them...) to implement the new curation process for both systems. ReDBox and Mint have very similar implementations and there is a significant amount of duplication in the designs (noted later), but ReDBox's plugin has some additional logic basic on the fact that it is the data entry point for staff input.
After perusing the rest of the curation documentation you would no doubt be aware that the primary building block of this whole process has been dubbed the '`task`', which is a discreet step in the overall workflow. A task is simply a message that the `CurationManager` will receive from any source with the '`task`' key found in the JSON object. Some tasks are sent from other parts of the system, like clickable links in the web portal, but most will originate from the `CurationManager` itself (or another system when ReDBox and Mint talk to each other). To do this the `CurationManager` simply adds a '`MESSAGE`' to itself as the last outgoing order. For example, the '`curation`' task would schedule a '`TRANSFORMER`' order for any curation Transformers in configuration and follow it up with a '`reharvest`' task (to update the templates), which will in turn run several of its own '`TRANSFORMER`' orders. Following this the original order queue would probably have a '`curation-confirm`' task, where the `CurationManager` will have a chance to double-check that everything executed as expected.

The most important factor in both system's design however is that after each discreet event has been processed the entire system must be ready for a brand-new event with a possibly different context. So we don't have things sitting around in memory waiting for follow-up events in the curation process, it all must be stored for later access. If you are ever planning on modifying this class, keep that aspect of the design in mind.
There isn't actually a lot more to say about the design at this point. The general documentation covers the idea of the `CurationManager` functioning as a state machine, so the key to all of this coding is simply to provide a framework in which each little part of the system can function in co-ordination with each other. Fascinator's message queueing makes the core part of this much simpler, and building the idea of 'orders' and 'tasks' on top of that provides easy ways of separating logic in to discreet chunks. The real work is making sense of the flurry of messages that start flying around once you click 'Curate' on a complicated network of linked data...
 
#### **[]()Finer Details
 Just a couple of finer points you might note in the code. These details aren't included in the more general documentation since users don't really need to know about them.

**Relationship Authority:**


Each relationship found in the metadata will either be stored with a flag of '`authority`' = '`true`' or the flag would be absent (it isn't typically stored as '`false`', but it would be effectively the same thing). This should only occur on one side of the relationship, and this is typically ReDBox (although Mint Parties consider themselves to be the authority on their Group relations). The object that is considered the authority (the parent for lack of a better term) is the one where the '`curation-request`' tasks should come from, and the non-authoritative record (the 'child') should only send '`curation-query`' requests. This is designed primarily to avoid infinite loops on events since the '`curation-query`' is a very lightweight task that does not propagate out through the network like a '`curation-request`' does.

A much clearer demonstration is for a '`publish`' task, where only parent records will send out '`publish`' tasks to their children after receiving one themselves, avoided a really basic infinite loop that would occur if children sent a '`publish`' task straight back to the parent. Instead the child records should only be ever sending '`curation-query`' tasks to their parents, which are basically saying "I need to know your curation details, and if they change please let me know, but don't take any further actions in response to this message".

The typical setup would be for a ReDBox Collection to be the authority over relationships built from its form data, such as a Mint Party record listed as the curator. The Mint Party will never try to publish or curate the ReDBox collection since it is not the authority, but it will try to do so for the associated Mint Group (the creator's faculty for example) over which is does have an authority relationship, creating a network of linked data where messages always flow outwards from the Collection.


**Additional Reharvest Tasks:**


The general documentation maps out the basic flow of curation tasks, but there some additional minor steps that occur in between many events that weren't noted there. These are simply operational chores related to ensuring that the metadata templates and the Solr Index are kept up-to-date throughout the process and appropriate entries are added to the audit log for an object. So don't be surprised if you are looking at the code and find extra calls to the harvester, indexer or subscriber.

 
#### **[]()Future Works
 Some thoughts/ideas/notes on possible future expansions and work in this area. This includes ideas that may never go anywhere, areas where more polish could be used, and danger areas that might want some more robust error handling in future:


* There is a significant amount of duplication between ReDBox and Mint's `CurationManager` classes. Consider providing a common base class that both servers can extend.
* The traditional Fascinator tool chain remains in use on the core build, and it does do a better job at multi-threading and priority routing. An amalgamation of the two approaches may be the best way forward since the new `TransactionManager` brings a lot of flexibility to the table.
* The current `CurationManager` don't really address the problems of relationships being deleted, which is typical of this community (most community members when asked will say "You shouldn't be deleting curated data"). But this should definitely be addressed, since the only options at this point involve sysadmins directly editing objects on disk to remove a 'dead' relation.
* There is a distinct danger of creating infinite loops when both sides of a relationship consider themselves to be the 'authority'. Perhaps some method of detecting this ahead of time should be implemented.
* At the moment data sources claiming to be '`alreadyCurated`' in config are expected to have their identifier in a hardcoded location ('`metadata`' > '`dc.identifier`'). This was an oversight during development and needs fixing.
* Second hand relationships (ie. ReDBox dictated to two Mint objects that they are now related) are not addressed at this stage, primarily because of time constraints. The most significant factor will be how to take the already complicated configuration and add this into the mix.
* We'd always wanted to implement some house keeping scripts to audit/monitor on-going curation activities, but didn't have time. This work will probably require some additional metadata to be stored about the process as well.
* Switching a ReDBox installation from VITAL to Handle highlighted just how entrenched some of the assumptions about method of curation are in the system. The approach of the `CurationManager` to use a configurable '`pidProperty`' should be extended throughout the system with more generic persistent identifier terminology.
* Constant context swapping of the PID acronym can get confusing. Developers internal to the system would be expecting Payload Identifier, but the eResearch community expects Persistent Identifier, which is somewhat intrinsic to the curation process. Perhaps internal to the codebase a naming convention should be drawn?
* This is a minor point, but for now the `CurationManager` classes both have dramatically elevated logging, and this can generate sizable log files when under heavy load. This is simply to provide the most debugging information as the new implementation beds down, but will no doubt be reduced later.

 
#### **[]()Some Utility Methods
 
In writing the `CurationManager` a couple of basic utility methods were put together to make sending 'orders' and 'tasks' around a lot simpler. I've reproduced them below in case they are of any interest to developers wanting to shortcut any work on their own `TransactionManager` plugin. The object "`JsonSimple response`" is pre-supposed to the list of orders that you will be sending back to the queue consumer when you are done. 
**Creating a basic order:** 
 `private JsonObject createNewOrder(JsonSimple response, String type) {`
 `    JsonObject order = response.writeObject("orders", -1);`
 `    order.put("type", type);`
 `    return order;`
 `}`
  
The '-1' parameter is important, since Fascinator's JSON Library will append to a list it finds at that path. It will create the list first if it isn't there already. 
**Creating a specific type of order:** 
 `private JsonObject newMessage(JsonSimple response, String target) {`
 `    JsonObject order = createNewOrder(response,`` TransactionManagerQueueConsumer.OrderType.MESSAGE.toString());`
 `    order.put("target", target);`
 `    order.put("message", new JsonObject());`
 `    return order;`
 `}`  
Obviously this builds on the existence of the first method, and there are a number of these for different types on orders. The example shows messaging since it relates to 'tasks'. 
**Creating a task:** 
 `private JsonObject createTask(JsonSimple response, String broker,`` String oid, String task) {`
 `    JsonObject object = newMessage(response,`` TransactionManagerQueueConsumer.LISTENER_ID);`
 `    if (broker != null) {`
 `        object.put("broker", broker);`
 `    }`
 `    JsonObject message = (JsonObject) object.get("message");`
 `    message.put("task", task);`
 `    message.put("oid", oid);`
 `    return message;`
 `}`  
So here we've added another layer on top of the earlier methods, as well as wrapping up the syntax for: 
 * Sending to another broker (like Mint).
 * Sending messages back to 'ourselves' ie. the `TransactionManagerQueueConsumer`.
 * Returning the specific '`message`' JSON object so that the caller doesn't need to worry about the details it is wrapped inside of.
  
**Breaking the rules:** 
 `task = createTask(response, broker, relatedOid, "curation-query");`
 `// We won't know OIDs for remote systems`
 `task.remove("oid") ;`
 `task.put("identifier", relatedId);`  
So building on all of these we see some usage in context, where we know that are not going to know the internal OIDs on a remote platform, but we do know one of its known identifiers and can supply that instead. We do this by modify the task's message once it has been return to us. 
**A different example:** 
 `private void email(JsonSimple response, String oid, String text) {`
 `    JsonObject object = newMessage(response,`` EmailNotificationConsumer.LISTENER_ID);`
 `    JsonObject message = (JsonObject) object.get("message");`
 `    message.put("to", emailAddress);`
 `    message.put("body", text);`
 `    message.put("oid", oid);`
 `}`  
Finally, this example is different again, in that it leverages the newMessage() method and then adds additional information. The wrapper makes sending emails via the relevant message queue quite trivial.