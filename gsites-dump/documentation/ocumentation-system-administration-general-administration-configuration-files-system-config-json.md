In your installation's '`home`' directory is the system configuration file: '`system-config.json`'. The file is in [JSON format](http://en.wikipedia.org/wiki/JSON), and contains all of the system wide configuration for the server. 
A considerable portion of the file is of no interest to an administrator... it just works, but some import sections relating to key features will require changing. These sections are detailed in other sections of this wiki: 
* [Handle integration](documentation-system-administration-administering-mint-handle-server)
 * [VITAL integration](documentation-system-administration-administering-redbox-vital-integration)
 * OAI-PMH exports
 * [Transformer configuration](documentation-system-administration-general-administration-metadata-templates)
 
 
The purpose of **this** page is to provide information relevant to the configuration file as a whole. 
#### **[]()Handy Hint
 
JSON has fairly exact [syntax requirements](http://www.json.org/), but the most common problems are incorrect placement of quotes and commas (particularly leaving extraneous trailing commas). Make your life easier and paste your config in a [JSON validator](http://www.jsonlint.com/) if you have made any significant changes. Let the validator find your mistakes before you try and start the system, since the startup will fail if it can't read its configuration file. 
#### **[]()Placeholders
 
Throughout the configuration file you will find placeholders like this: `"path": "${fascinator.home}/security/users.properties"` 
The value of '`${fascinator.home}`' will be substituted with the Java system property '`fascinator.home`'. This avoids needless repetition of commonly used values, and is why we provide a path in the [environment script](documentation-system-administration-general-administration-configuration-files-control-scripts): 
 `export PROJECT_HOME="C:/tf2/mint-bare"` 
When the JVM is started we will ensure the appropriate system property is set: 
 `export TF_HOME="$PROJECT_HOME/home"`
 `...`
 `-Dfascinator.home=$TF_HOME`
  
#### **[]()Commenting
 
JSON has no support for commenting, but you can always change values to 'hide' them, or add nodes with nonsense values and put comments in them. For example, say I wanted to temporarily remove the Handle configuration (but not delete it entirely), I could change this: 
 `    "transformerDefaults": {`
 `         "handle": {`
 `             "id": "handle",`
 `             "namingAuthority": "1959.18",`
 `             "privateKeyPath": "${fascinator.home}/handle/admpriv.bin",`
 `             "passPhrase": "password",`
 `             "useIncrements": true,`
 `             "incrementingFile": "${fascinator.home}/handle/index.txt"`
 `         }`
 `     },`
  
 to something below. It's still valid JSON, but it's not something our system uses for configuration: 
 `    "transformerDefaults": {`
 `         "handle-comment": "I've disabled this for now, because the server is broken",`
 `         "handle-disabled": {`
 `             "id": "handle",`
 `             "namingAuthority": "1959.18",`
 `             "privateKeyPath": "${fascinator.home}/handle/admpriv.bin",`
 `             "passPhrase": "password",`
 `             "useIncrements": true,`
 `             "incrementingFile": "${fascinator.home}/handle/index.txt"`
 `         }`
 `     },`