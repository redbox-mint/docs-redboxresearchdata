[[_TOC_]]



Both ReDBox and Mint utilise the [JSON Velocity Transformer](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/json-velocity-transformer) to render their data into pre-determined metadata formats in preparation for export. This is a fairly standard process in line with how the the Transformer is intended to function (see the [Transformer doco](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/json-velocity-transformer) for further details). This page highlights how the configuration in Mint and ReDBox is setup.
### []()ReDBox - System Configuration

ReDBox only has one Transformer installed, so this is fairly simple:
            "transformerDefaults": {
                "jsonVelocity": {
                    "id": "jsonVelocity",
                    "sourcePayload": ".tfpackage",
                    "templatesPath": "${fascinator.home}/templates"
                }
            },

As per the Transformer's documentation '`.tfpackage`' being specified as the source tells the system to open the '`*.tfpackage`' payload (the file's base name will be a random number string) and run it through all of the templates in the '`home/templates`' folder.
#### **[]()Tool Chain

There is one complicating factor with ReDBox, but it relates more to the [VITAL Integration](documentation-system-administration-administering-redbox-vital-integration). Because a) ReDBox is transforming form data from the workflow, rather then object data from a data source and b) VITAL integration requires certain events to occur in sequence we couldn't make use of The Fascinator's normal [transformer tool chain](http://sites.google.com/site/fascinatorhome/home/documentation/technical/details/tool-chain).
After each workflow save event the core Fascinator platform would typically trigger a re-index of the item, but not a re-transformation. This could possibly be rectified by sending a message to one of the tool chain queues (like the render queue), but this is an asynchronous event, and we would have no knowledge of the process finishing to then notify the VITAL subscriber. In response, there are two solutions available:
1. Following a workflow save event we DO need to notify VITAL, so we manually call the Transformer plugin, then re-index, then notify VITAL:
            # Re-index the current object
            def _reIndex(self, step):
                object = self._getObject()

                # Transform the object to other datastream e.g. dublin core, rif-cs and vitro
                try:
                    jsonVelocityTransformer = PluginManager.getTransformer("jsonVelocity")
                    jsonVelocityTransformer.init(JsonSimpleConfig.getSystemFile())
                    jsonVelocityTransformer.transform(object, "{}")
                except Exception, e:
                    self.log.error("Fail to transform object : ", e)

                # Index the object
                oid = object.getId()
                try:
                    self.Services.indexer.index(oid)
                    self.Services.indexer.commit()
                except Exception, e:
                    self.log.error("Fail to transform object : ", e)

                # Notify subscribers (like VITAL)
                self.sendMessage(oid, step)
1. When focusing on the templates themselves (say you are actively developing changes) you DON'T need to keep sending the outputs to VITAL, so the administrative 'Reharvest' and 'Reindex' events have been re-purposed for ReDBox to both do the same thing. They send a message to a special message queue just for ReDBox (the 'indexer' queue) which runs the VITAL Transformer, then re-indexes the object. When developing then it is typically a case of make the changes in the '`home/templates`' folder and running 'Reharvest' on the object's detail screen. It is a good idea to watch the '`indexer.log`' file as well, in case you left errors in the template.

### []()Mint - System Configuration

Mint is a slightly different beast, because it has more then one type of object ('*Parties - People*', '*Parties - Groups*' and '*Activities*') and they each require different templates. That being said, the first place to look is still the system configuration file:
            "transformerDefaults": {
                ...
                "jsonVelocity": {
                    "id" : "jsonVelocity",
                    "sourcePayload" : "metadata.json",
                    "templatesPath" : "${fascinator.home}/templates",
                    "portalId": "default"
                }
            },
Although keep in mind that the '`templatesPath`' and '`portalId`' specified are placeholders and will be changed for each data source. The '`sourcePayload`' is signifcant however, as all the datasources share this value and will use '`metadata.json`' as the source to be transformed.
#### **[]()Data Source Configuration

In order to configure each data source you need to edit the harvester configuration file for that source. For example, '*Parties - People*' has a file in '`home/harvest/Parties_People.json`' with the following details:
            "transformer": {
                "harvestQueue": ["handle"],
                "indexOnHarvest": "false",
                "renderQueue": ["jsonVelocity"]
            },

            "transformerOverrides": {
                "handle": {
                    ...
                },
                "jsonVelocity": {
                    "templatesPath" : "${fascinator.home}/templates/people",
                    "portalId": "Parties_People"
                }
            },
There are two areas of significance highlighted above, firstly that we've configured the tool chaing to run our '`jsonVelocity`' Transformer inside the '`renderQueue`', and then also we've provided some overriding configuration for the Transormer, telling it that there is a '`home/templates/people`' folder containing the templates to use. It's a minor point, but we've also set the '`portalId`' to '`Parties_People`'. This is mainly related to OAI PMH. If anyone looks at this record in isolation it can identify the data set it belongs to.