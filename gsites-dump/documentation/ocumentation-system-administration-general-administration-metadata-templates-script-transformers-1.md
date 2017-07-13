## []()**RIF-CS**


**

**


### []()Configuration

Groovy scripts can be added as transformers in the transformer.json configuration. RIF-CS can be inserted, in no particular order, alongside say local and jsonVelocity, like so:
```
{
 "transformerDefaults": {
 "local": {
 "id": "local",
 "useIncrements": false
 },
 "rifcs": {
 "id": "scripting",
 "scriptType": "groovy",
 "scriptPath": "${fascinator.home}/scripts/tfpackageToRifcs.groovy"
 },
 "jsonVelocity": {
 "id": "jsonVelocity",
 "sourcePayload": ".tfpackage",
 "templatesPath": "${fascinator.home}/templates",
 "templatesPathExclusions": ["rif.vm"]
 }
```
```
}
```

In the above example, the RIF-CS configuration has:

* id: indicates this is work to be performed by the redbox scripting transformer. This id value must always be 'scripting' in order to run a script as a transformer.


* scriptType: indicates the script 'flavour' (in this case groovy, instead of say, python).
* scriptPath: indicates the file location of the transformer (in this case, using the variable fascinator.home as the base directory name)

### []()Excluding the rif.vm template

The example also shows that the jsonVelocity configuration has a field:```
"templatesPathExclusions": ["rif.vm"]
```

When choosing to run the RIF-CS script transformer instead of the RIF-CS velocity template, this configuration must be included. Otherwise both the rifcs transformer and the velocity template will run, with the results of the velocity template overwriting the groovy script.
(If the choice is to run the rif.vm template instead of the RIF-CS groovy script, then leave this field out or set its value to empty square brackets, as well as removing the RIF-CS 'scripting' configuration).
Of course, any of the json velocity templates which run as part of the json velocity transformer library, can be added here - in comma-delimited format - in order to be excluded.


### []()Migration

When performing a redbox upgrade and running the tf_restore.sh script, if you are planning to also allow the RIF-CS transformation to run, then you must also enable harvest remapping in migration.json.```
{
 "restoreTool": {
 "harvestRemap": {
 "enabled": true,
 "allowOlder": false,
 "failOnMissing": false
 },
 "migrationScript": "${fascinator.home}/restore/redboxMigration1.9.py"
 }
}
```

In the example, above, enabled is set to true, enabling the RIF-CS logic to run as part of harvest remapping.