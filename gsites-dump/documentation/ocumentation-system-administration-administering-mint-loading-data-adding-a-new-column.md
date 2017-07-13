This document discusses how to perform a reasonably simple modification to the [data load](documentation-system-administration-administering-mint-loading-data) to add some additional data to the system. The example used is to add a biography field for each person so that the RIF-CS 'Party' record can send additional information to Research Data Australia. 
#### **[]()Altering Your Data
 
So to start we need to make the reasonably minor change to the source data. In that case I'm altering the sample records in '`home/data/Parties_People_Sample.csv`'. Take the first couple of data rows and heading for example: 
 `"ID","Honorific","Given Name","Family Name","Title","Division","School","Email"`
 `1234,"Mr","Albert","Zweistein","Associate Lecturer","Faculty of Science","Chemistry","albert.zweistein@uni.edu.au"`
 `115534,"Dr","Bo","Millett","Lecturer","Faculty of Information Technology","Information Security","bo.millet@uni.edu.au"`  
We need to add a new heading for the column and some new data as well. The last row shows that it is perfectly valid to leave columns as empty data: 
 `"ID","Honorific","Given Name","Family Name","Title","Division","School","Email","Bio"`
 `1234,"Mr","Albert","Zweistein","Associate Lecturer","Faculty of Science","Chemistry","albert.zweistein@uni.edu.au","A sample bio"` `115534,"Dr","Bo","Millett","Lecturer","Faculty of Information Technology","Information Security","bo.millet@uni.edu.au",""`  
**NOTE:** There is no space after the comma. The CSV Harvester that reads these files is sensitive to these spaces. If you had a space after the comma and before the opening quote it would not work. 
#### **[]()Configuration Tweak
 
Now we need to alter the harvester configuration to ensure that the new column is harvested. Look for the file '`home/harvest/Parties_People.json`'. We just need to add the new column name under '`includedFields`': 
 `    "harvester": {`
 `        "type": "csv",`
 `        "csv": {`
 `            "fileLocation": "${fascinator.home}/data/Parties_People_Sample.csv",`
 `            "idColumn": "ID",`
 `            "recordIDPrefix": "http://fascinator.usq.edu.au/parties/people/",`
 `            "headerRow": true,`
 `            "delimiter": ",",`
 `            "ignoredFields": [],`
 `            "includedFields" : ["ID", "Honorific", "Given Name", "Family Name", "Division", "School", "Email", "Bio"]`
 `        }`
 `    },` 
And that's it... the harvester can now be executed again the new data will be added to the system. 
#### **[]()Check Your Data
 
Once the harvester has completed execution you can check the contents of the '`metadata.json`' payload to confirm the new field is visible: 
 `{`
 `    "recordIDPrefix": "http://fascinator.usq.edu.au/parties/people/",`
 `    "data": {`
 `        "ID": "1234",`
 `        "Honorific": "Mr",`
 `        "Given Name": "Albert",`
 `        "Family Name": "Zweistein",`
 `        "Division": "Faculty of Science",`
 `        "School": "Chemistry",`
 `        "Email": "albert.zweistein@uni.edu.au",`
 `        "Bio": "A sample bio"`
 `    },`
 `    "metadata": {`
 `        "dc.identifier": "http://hdl.handle.net/1959.18/913364"`
 `    }`
 `}`  
#### **[]()Use Your Data
 
If you'd like to index the additional data in Solr you'll need to modify the indexing rules file: '`home/harvest/Parties_People.py`'. There are many, many options here, but a basic example is below: 
 `    def __metadata(self):`
 `        jsonPayload = self.object.getPayload("metadata.json")`
 `        json = self.utils.getJsonObject(jsonPayload.open())`
 `        jsonPayload.close()`
 `        `
 `        metadata = json.getObject("metadata")`
 `        self.utils.add(self.index, "dc_identifier", metadata.get("dc.identifier"))`
 `        `
 `        data = json.getObject("data")`
 `        self.utils.add(self.index, "dc_title", "%s, %s" % (data.get("Family Name"), data.get("Given Name")))`
 `        self.utils.add(self.index, "biography", data.get("Bio"))` 
As per the [existing documentation on changing templates](documentation-system-administration-general-administration-metadata-templates), the 'metadata.json' file is already available as the '`$item`' variable, so making use of the new data is reasonably trivial there as well. This is a sample modification to '`home/templates/groups/rif.vm`'. (NOTE: I'm making up the XML node here, not sure what the official RIF-CS biography field would be).
If you follow the link at the top of that page to The Fascinator's [core documentation for this transformer](http://sites.google.com/site/fascinatorhome/home/documentation/technical/documents/plugins/transformer/json-velocity-transformer), you'll find some additional explanation of this: 
 `    <registryObject group="The University of Newcastle, Australia">`
 `        <key>$util.encodeXml($util.get($item, "metadata", "dc.identifier"))</key>`
 `        <originatingSource>$urlBase</originatingSource>`
 `        <party type="person">`
 `            <identifier type="uri">$util.encodeXml($util.get($item, "metadata", "dc.identifier"))</identifier>`
 `            <name type="primary">`
 `                <namePart type="family">$util.encodeXml($util.get($item, 'data', 'Family Name'))</namePart>`
 `                <namePart type="given">$util.encodeXml($util.get($item, 'data', 'Given Name'))</namePart>`
 `            </name>`
 `            #set ($value = $util.get($item, "data", "Bio"))`
 `            #if ("$!value" != "")`
 `            <biography>$util.encodeXml($value)</biography>`
 `            #end`
 `        </party>`
 `    </registryObject>`