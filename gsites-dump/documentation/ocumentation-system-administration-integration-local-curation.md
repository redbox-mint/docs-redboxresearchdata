The Local Curation Transformer offers an alternative to the pre-existing [Handle](documentation-system-administration-administering-mint-handle-server) and [VITAL](documentation-system-administration-administering-redbox-vital-integration) integration options. It offers functionality useful in two situations:

* Because it requires no external integrations it is ideal for running test/dev/demo systems. Immediately following the launch of v1.2 the existing practice of disabling Handle/VITAL in these environments provided an arbitrary limit on functionality, since there was no Transformer offering curation services.
* With careful consideration, institutions may opt to use their own local URIs containing institutional domain information. This should be very carefully considered, since there are other implications, but this Transformer offers this is a basic option... assuming you don't want to integrate with other institutional systems.
Normal usage is typically expected to be the first option however.

#### **[]()Configuration
Some of the information below is also covered in the [JavaDoc](https://redbox-build.cqu.edu.au/jenkins/job/mint_doco_trunk/javadoc/index.html?com/googlecode/fascinator/redbox/plugins/local/LocalTransformer.html) for this class.

As with all Transformers, the best place to start is in the general [system configuration](documentation-system-administration-general-administration-configuration-files-system-config-json), under '`transformerDefaults`':

        "transformerDefaults": {
            "local": {
                "id": "local",
                "useIncrements": true,
                "incrementingFile": "${fascinator.home}/handle/index.txt"
            }
        },
And, as with most other Transformers, you can ignore the majority of the text here, and just focus on the two values in red:

* '`useIncrements`': Flag whether or not to use an incrementing number sequence. This is a copy of the functionality in the [Handle Transformer](documentation-system-administration-administering-mint-handle-server).

* '`incrementingFile`': If the above flag is turned on, this path is expected to contain a file where the current number in the sequence is stored.
Also, similarly to the Handle Transformer, the Local Curation Transformer is going to look under '`curation`' > '`pidProperty`' to see where the Curation Manager is expecting persistent identifiers to be stored. This value is used as the name of a metadata property to store against the Object. In the absence of this config (unexpected in the context of ReDBox or Mint) the Transformer will fallback to a default value of '`localPid`'.

Each data source is also expected to have some configuration containing the template that should be used:

        "transformerOverrides": {
            "local": {
                "template": "my.institutional.id:[[OID]]"
            }
        },
As per the [JavaDoc](https://redbox-build.cqu.edu.au/jenkins/job/mint_doco_trunk/javadoc/index.html?com/googlecode/fascinator/redbox/plugins/local/LocalTransformer.html), the template may use not only the '`[[OID]]`' placeholders, but '`[[INC]]`' can be used to tie in with the incrementing number discussed above... assuming it is configured of course.