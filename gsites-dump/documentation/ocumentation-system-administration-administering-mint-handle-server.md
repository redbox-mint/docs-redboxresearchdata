By default, Mint is configured for integration with the [Handle System](http://www.handle.net/). The handles created are currently used as identifiers for data but do not resolve to a URL. In other words, you get an ID but not a link to a web page. 
The complete Handle Transformer is [documented elsewhere](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/handle-transformer). This page details specific configuration changes/actions required to bring your server online, or to disable Handle integration. 
### []()Configuring a new installation
 
The default Handle configuration is set in the '`transformerDefaults`' section of [system-config,json](documentation-system-administration-general-administration-configuration-files-system-config-json), so consider reading that doco if you haven't already. 
Inside the Mint configuration you will see: 
 `    "transformerDefaults": {`
 `        "handle": {`
 `            "id": "handle",`
 `            "namingAuthority": "1959.18",`
 `            "privateKeyPath": "${fascinator.home}/handle/admpriv.bin",`
 `            "passPhrase": "password",`
 `            "useIncrements": true,`
 `            "incrementingFile": "${fascinator.home}/handle/index.txt"`
 `        },`
 `        "jsonVelocity": {`
 `            "id" : "jsonVelocity",`
 `            "sourcePayload" : "metadata.json",`
 `            "templatesPath" : "${fascinator.home}/templates",`
 `            "portalId": "default"`
 `        }`
 `    },`  
You only care about altering a few nodes to achieve integration: 
* *namingAuthority*: needs to match your Handle server. Obvious this should be the naming authority you are the **authority** on.
 * *privateKeyPath*: this path needs to point at the binary private key for administering the naming authority specified above. You need to copy the key from you Handle server. Either use this exact path or alter it to a location you prefer.
 * *passPhrase*: is optional. IF your private key has a pass phrase however you will need to specify it here.
 * *incrementingFile*: the location of the file containing your auto-incrementing number. It should be there already, but you will need to edit the file to ensure the number is where you want it. At Newcastle the Handle server creates Handles for many systems using the same sequence, so if you started this at '1' you'd be waiting a long time for the system to traverse 900,000+ Handles looking for a free number.
 
### []()Turning off the Handle transformer
 
If you do not have a Handle system running you will need to remove the Handle configuration from [system-config.json](documentation-system-administration-general-administration-configuration-files-system-config-json). Taking the sample configuration from above, you'd remove the "handle" section and be left with: 
 `    "transformerDefaults": {`
 `        "jsonVelocity": {`
 `            "id" : "jsonVelocity",`
 `            "sourcePayload" : "metadata.json",`
 `            "templatesPath" : "${fascinator.home}/templates",`
 `            "portalId": "default"`
 `        }`
 `    },` 
You will also need to edit the various harvest configuration files to ensure they don't call for a Handle to be created. Within the '`home/harvest`' folder you will find various harvest configuration files - these are given the '.json' extension. Ignore the '.py' files. 
 `    "transformer": {`
 `        "curation": ["handle"],
        "metadata": ["ingest-relations", "jsonVelocity"]
``    },` 
To disable handle transformation on a harvest, just remove the "handle" option in the curation section: 
 `    "transformer": {`
 `        "curation": [],
        "metadata": ["ingest-relations", "jsonVelocity"]
``    },`