Originally, the VITAL Transformer was written against version 2.2.4 of Fedora, but for v1.3 of ReDBox we added support for v3.5 (and earlier) to accommodate VITAL system upgrades going on with ReDBox users. The differences between the two are very minor, but enough to warrant a second version of the Fedora Client (the API was altered slightly). 
The ReDBox default now ships with v3.x Fedora turned on for VITAL integration, so this page highlights what the slight differences are, and how to swap from one to the other. 
#### **[]()What Changed?
 
From Fedora's perspective... very little. In fact only a single API change effected our integration, and this was the removal of the '`describeUser()`' method we used in Fedora2 to test that APIM interface at startup, confirming we had appropriate credentials. Now for Fedora3 we instead call '`getObjectXML()`' to retrieve the '`fedora-system:FedoraObject-3.0`' PID (present in all v3.x Fedora installations). 
Of course, this change in Fedora is what required us to change our implementation... and this is detailed below. 
#### **[]()Swapping to Fedora 2
 
The default ReDBox installation now ships with Fedora 3 turned on. Swapping to Fedora 2 requires a system rebuild with three changes: 
 1.  
We need to include a different set of dependencies in our root POM; changing this: 
 `<!-- Swap these 2 dependencies to go back to Fedora2 -->`
 `<!--`
 `<dependency>`
 `    <groupId>com.googlecode.redbox-mint</groupId>`
 `    <artifactId>redbox-vital-transformer-fedora2</artifactId>`
 `    <version>${project.version}</version>`
 `</dependency>`
 `-->`
 `<dependency>`
 `    <groupId>com.googlecode.redbox-mint</groupId>`
 `    <artifactId>redbox-vital-transformer-fedora3</artifactId>`
 `    <version>${project.version}</version>`
 `</dependency>`  
to this: 
 `<!-- Swap these 2 dependencies to go back to Fedora2 -->`
 `<dependency>`
 `    <groupId>com.googlecode.redbox-mint</groupId>`
 `    <artifactId>redbox-vital-transformer-fedora2</artifactId>`
 `    <version>${project.version}</version>`
 `</dependency>`
 `<!--`
 `<dependency>`
 `    <groupId>com.googlecode.redbox-mint</groupId>`
 `    <artifactId>redbox-vital-transformer-fedora3</artifactId>`
 `    <version>${project.version}</version>`
 `</dependency>`
 `-->`  
This will flow through the system during the rebuild, swapping out local ReDBox code, Fedora client libraries on which each depends, and ultimately the dependencies of those libraries in turn. Annoyingly, this is a fairly messy process to perform manually, so a Maven build is recommended. 
 1.  
The system configuration file should point at different foxml template, more appropriate to v3.x Fedora. The system does ship with both of these, so the config change is all that is required; this is the default: 
 `"transformerDefaults": {`
 `    "vital": {`
 `        ...`
 `        "foxmlTemplate": "${fascinator.home}/vital/foxml_template_fedora3.xml",`
 `        ...`
 `    }`
 `},`  
just change the single digit: 
 `"transformerDefaults": {`
 `    "vital": {`
 `        ...`
 `        "foxmlTemplate": "${fascinator.home}/vital/foxml_template_fedora2.xml",`
 `        ...`
 `    }`
 `},`  
 1.  
And finally, there is a housekeeping job (/portal/default/redbox/scripts/hkjobs/vital.py) that imports the Fedora client at the top. This needs changing as well; from this: 
 `## Pick appropriately`
 `# Fedora 2`
 `#from fedora.client import FedoraClient`
 `# Fedora 3`
 `from org.fcrepo.client import FedoraClient`  
to this: 
 `## Pick appropriately`
 `# Fedora 2`
 `from fedora.client import FedoraClient`
 `# Fedora 3`
 `#from org.fcrepo.client import FedoraClient`  
  
And that's it, you should have now changed everything required to connected to a Fedora v2.x repository.