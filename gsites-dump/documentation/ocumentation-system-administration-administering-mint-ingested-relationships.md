Aside from having the most fashionable name known to Java Classes worldwide, the `IngestedRelationshipsTransformer` is the new Transformer added to Mint in the version 1.2 release. It has a very specific purpose, which is to bridge the gap between [ingesting flat CSV data into Mint](documentation-system-administration-administering-mint-loading-data) and [curating a linked data network](documentation-system-overview-curating-linked-data). To do this the new Transformer needs to take the flat data containing link information in the form of basic IDs with narrow scope and build the network ready to be curated using full system-wide identifiers. 
#### **[]()Identifiers
 
This doesn't make a great deal of sense unless you've looked at the columns used to ingest [group data](documentation-system-administration-administering-mint-loading-data-loading-group-data) and [people data](documentation-system-administration-administering-mint-loading-data-loading-people-data). You'll notice that we ingest an ID column for each Party, and that people have links to Groups and that Groups have links to other Groups, but what is not immediately apparent is that the ID columns are (typically) going to contain basic IDs that came from another institutional data source (commonly a database). These IDs really could be anything (like '34' for example), but if they did come from a database it would not be uncommon for there to be duplication of the basic values because of the way databases use foreign keys. So in that light we need a way to account for Person 34 and Group 34 being distinctly separate. 
This is one of the problems the Transformer addresses; in resolving the relationships it will convert these basic IDs into system-wide identifiers. It is the job of the [Curation Manager](documentation-system-overview-curating-linked-data) to later convert them into curated/persistent identifiers that are ready for publication. 
The solution is pretty simple, we are just going to prepend a given prefix for each data source (something the CSV Harvester already does) and then configure our new Transformer to be aware of the different prefixes and where to find our linking data. For the sake of thoroughness we'll also allow the configuration to specify the nature of the relationship, and what the other party should know about the object being transformed.
#### **[]()System Configuration

This is pretty trivial, since there is no real configuration there:
        "transformerDefaults": {
            "ingest-relations": {
                "id": "ingest-relations"
            },
            ...
        },
We are just telling the system to load our plugin with ID '`ingest-relations`', and to keep things nice and easy we will use the same value for the key (targeted in our per data source config).
#### **[]()Data Source Configuration

This is where the 'real' configuration occurs. We need to tell the Curation Manager to send records from specific data sources through our Transformer, and then tell the Transformer what to do about it. Starting with routing:
        "transformer": {
            "curation": ["handle"],
            "metadata": ["ingest-relations", "jsonVelocity"]
        },
This is fairly standard, as per [Curation Manager documentation](documentation-system-overview-curating-linked-data-curation-configuration). Next though we get to the specific configuration for our Transformer, in this case the example is for a Mint 'Parties_People' record:
        "transformerOverrides": {
            "ingest-relations": {
                "sourcePid": "metadata.json",
                "sourcePath": ["data"],
                "relations": {
                    "GroupID_1": {
                        "prefix": "redbox-mint.googlecode.com/parties/group/",
                        "relation": "isMemberOf",
                        "reverseRelation": "hasMember"
                    },
                    "GroupID_2": {
                        "prefix": "redbox-mint.googlecode.com/parties/group/",
                        "relation": "isMemberOf",
                        "reverseRelation": "hasMember"
                    },
                    "GroupID_3": {
                        "prefix": "redbox-mint.googlecode.com/parties/group/",
                        "relation": "isMemberOf",
                        "reverseRelation": "hasMember"
                    }
                }
            },
            ...
        },
So this configuration has only three top-level keys:**Key**

**Notes**

        sourcePid
The source Payload to get our metadata from. Must be in JSON format.
        sourcePath
A list of keys building a JSON path to our metadata inside the above Payload's parsed JSON data.
        relations
A JSON object defining relationships. Each key is expected to be a field name found on the above path. Individual objects are described in the next table.

Each Object in the '`relations`' Map is going to be processed in turn in order to build a relationship, as per the properties below:**Property**

**Notes**

        prefix
The ID prefix to prepend to the value found at this key. This satisfies the earlier discussion about ID uniqueness.
        relation
Specifies the nature of the relationship this record has to the other record. This value (out-of-the-box) comes from the [ANDS Controlled Vocabulary](http://services.ands.org.au/documentation/rifcs/1.2.0/schema/vocabularies.html).
        reverseRelation
Specifies the nature of the relationship the **other record** has to this record. As above.

So you can see from the above example that this is a fairly basic way of making sense of the ingested relationships that People have to their Group memberships. Another example in use in Mint is below as well, which is how Groups define their hierarchy:


        "transformerOverrides": {
            "ingest-relations": {
                "sourcePid": "metadata.json",
                "sourcePath": ["data"],
                "relations": {
                    "Parent_Group_ID": {
                        "prefix": "redbox-mint.googlecode.com/parties/group/",
                        "relation": "isPartOf",
                        "reverseRelation": "hasPart"
                    }
                }
            },
            ...
        },
Once you have all of these records ingested an process together, this allows linked networks like: "Mr Cosmo Costanza" `isMemberOf` "School of Fine Arts" `isPartOf` "Faculty of Arts" `isPartOf` "University of Examples". And once the Curation Manager is finished it's work each record will have a proper persistent identifier and be ready for publication along with any record that linked to any part of the network.