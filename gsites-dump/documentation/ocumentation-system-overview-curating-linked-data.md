[[_TOC_]]



With the release of ReDBox / Mint versions 1.2, a new Curation Manager replaced the traditional Fascinator [tool chain](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain). The main purpose of this component is to implement staged release steps for linked datasets of related objects (ie. RIF-CS Collections, Parties and Activities) in a coordinated fashion. 
It seeks to address the larger issue of publishing linked data in a distributed environment in such a way that: 
 1. Data is is not needlessly curated or published if it is not part of a significant relationship.
 1. All collaborating systems are aware of the need to publish in a time sensitive fashion.
 1. External parties that can see one node of a linked network can reasonably expect to see all other related nodes curated and published in a similar timeframe.
 
#### **[]()Background
 
The [original planning document](http://code.google.com/p/redbox-mint/wiki/CurationBoundaryPlanning) for this work is available, although any technical details should be ignored in favour of documentation found in this section. Remember that is was a planning document written before work started; but it does however contain background on the broad intent of the work and its place in the Australian eResearch sector. 
#### **[]()Configuration
 
Configuring curation management can seem a little awkward at first, but is basically a case of configuring each part of the larger picture into a sensible whole. There is complete page on [curation configuration](documentation-system-overview-curating-linked-data-curation-configuration) to assist you in this. 
#### **[]()The Basic Process
 
In designing the final implementation of the Curation Manager we have arrived at a setup which basically functions as a state machine. To elaborate on this I'll steal a quote from everybody's friend, [Wikipedia](http://en.wikipedia.org/wiki/Finite-state_machine): *"[A state machine] is composed of a finite number of states associated to transitions. A transition is a set of actions that starts from one state and ends in another (or the same) state. A transition is started by a trigger, and a trigger can be an event or a condition."* 
In our case it was fairly clear that the trigger is a curation/publication request; ie. a data entry user has finished curating a Collection in ReDBox and it is now ready for publication. What was not so clear was how to go about defining our 'set of actions' in such a way that they could occur across multiple systems and respond to a potentially convoluted web of linked relationships that staff have entered during curation.
The [planning document](http://code.google.com/p/redbox-mint/wiki/CurationBoundaryPlanning) proposed to leverage Fascinator's messaging system to provide a series of message processing classes implementing each part of the procedure. It was intended that this would expand on the existing core of the Fascinator's tool chain and the two areas of logic would need to interact somehow.
 After much design work and discussion however we found it was actually simpler to go even further down this design path and create a new plugin (called a [TransactionManager](https://redbox-build.cqu.edu.au/jenkins/job/tf2_doco_trunk/javadoc/index.html?com/googlecode/fascinator/api/transaction/TransactionManager.html)) and to wrap a [message queue implementation](https://redbox-build.cqu.edu.au/jenkins/job/tf2_doco_trunk/javadoc/index.html?com/googlecode/fascinator/messaging/TransactionManagerQueueConsumer.html) around it to replace the core Fascinator tool chain. This effectively replaced most message queues in the system and left us with our single 'state machine' model.

With one queue consumer (and plugin) acting as our state engine on each server, the design work then focused on creating a workflow-like process to implement the state change actions, with each message into the message queue acting as a discreet step in the process. To describe all of this we have two more detailed documents, depending on the audience/interest amongst readers:
1. The [data model](documentation-system-overview-curating-linked-data-curation-data-model) for discreet actions within the state change process. This document would be of interest from a top-level design perspective, or administrators wanting to know what step each object would go through.
1. Developers will also want to look at [implementation details](documentation-system-overview-curating-linked-data-curation-implementation) that provide more information and/or clarifications on particularly problematic parts of the above document.

#### **[]()Resources


![](http://www.google.com/chart?chc=sites&cht=d&chdp=sites&chl=%5B%5BPage+listing'%3D20'f%5Cv'a%5C%3D0'10'%3D499'0'dim'%5Cbox1'b%5CF6F6F6'fC%5CF6F6F6'eC%5C0'sk'%5C%5B%22Subpage+Listing%22'%5D'a%5CV%5C%3D12'f%5C%5DV%5Cta%5C%3D10'%3D0'%3D500'%3D297'dim'%5C%3D10'%3D10'%3D500'%3D297'vdim'%5Cbox1'b%5Cva%5CF6F6F6'fC%5CC8C8C8'eC%5C'a%5C%5Do%5CLauto'f%5C&sig=lxEPbO98PQKCTIgDHMqrwU0hJD8)



* [Curation overview presentation to ReDBox Community](https://docs.google.com/open?id=0B2ekIO0v2A7CTVRlVlRLb2ZINlk)
* [Mint-NLA workflow diagram](documentation-system-overview-curating-linked-data-mint-nla-pdf?attredirects=0) kindly submitted by Toby (UWS)