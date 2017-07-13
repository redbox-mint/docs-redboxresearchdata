### []()Using ORCIDs for institutional Parties in Mint

If you have [ORCIDs](http://orcid.org/) associated with your parties in your source of truth (e.g. University Office or Research Master), you can harvest them into Mint and use it as the record's identifier. To set it up:
1. Add a column for the ORCID to the Party csv file used in the harvest.
```
ID,Given_Name,Other_Names,Family_Name,Pref_Name,Honorific,Email,Job_Title,GroupID_1,GroupID_2,GroupID_3,ANZSRC_FOR_1,ANZSRC_FOR_2,ANZSRC_FOR_3,URI,NLA_Party_Identifier,ResearcherID,openID,Personal_URI,Personal_Homepage,Staff_Profile_Homepage,Description
```
```
,ORCID
```

1. 
Edit your Party people harvest configuration to include the new ORCID column in the includedFields





        {
            `"harvester"``: {`
                `"type"``: ``"csv"``,`
                `"csv"``: {`
        ...
                    `"includedFields"` `: [``"ID"``, ``"Given_Name"``, ``"Other_Names"``, ``"Family_Name"``, ``"Pref_Name"``, ``"Honorific"``, ``"Email"``, ``"Job_Title"``, ``"GroupID_1"``, ``"GroupID_2"``, ``"GroupID_3"``, ``"ANZSRC_FOR_1"``, ``"ANZSRC_FOR_2"``, ``"ANZSRC_FOR_3"``, ``"URI"``, ``"NLA_Party_Identifier"``, ``"ResearcherID"``, ``"openID"``, ``"Personal_URI"``, ``"Personal_Homepage"``, ``"Staff_Profile_Homepage"``, ``"Description"``,``"ORCID"``]`
                `}`
            `},`
        ...
        }
This will ensure the ORCIDs are recognised and stored against the party record.
1. 
Now that records are being stored, curation of parties by Mint is no longer needed and the ORCID should be provided for references. In the harvest configuration file set the alreadyCurated flag to true





        {
            `...`
         `"curation"``: {`
                `"neverPublish"``: ``false``,`
                `"alreadyCurated"``: ``true`
            `},`
        ...
        }
and change the idColumn value to the name of the new ORCID column. If the ORCID column contains the complete ORCID string (e.g. [orcid.org/0000-0000-0000-0001](http://orcid.org/0000-0000-0000-0001)), empty the recordIdPrefix value.





        {
            `"harvester"``: {`
                `"type"``: ``"csv"``,`
                `"csv"``: {`
                    `"fileLocation"``: ``"${fascinator.home}/data/People_Sample.csv"``,`
                    `"idColumn"``: ``"ORCID"``,`
                    `"recordIDPrefix"``: ``""``,`
                    `"headerRow"``: ``true``,`
                    `"delimiter"``: ``","``,`
                    `"ignoredFields"``: [],`
                    `"includedFields"` `: [``"ID"``, ``"Given_Name"``, ``"Other_Names"``, ``"Family_Name"``, ``"Pref_Name"``, ``"Honorific"``, ``"Email"``, ``"Job_Title"``, ``"GroupID_1"``, ``"GroupID_2"``, ``"GroupID_3"``, ``"ANZSRC_FOR_1"``, ``"ANZSRC_FOR_2"``, ``"ANZSRC_FOR_3"``, ``"URI"``, ``"NLA_Party_Identifier"``, ``"ResearcherID"``, ``"openID"``, ``"Personal_URI"``, ``"Personal_Homepage"``, ``"Staff_Profile_Homepage"``, ``"Description"``,``"ORCID"``]`
                `}`
            `},`
        ...
        }
### []()Referencing external researchers

From ReDBox 1.9, a search interface has been added to the existing Name lookup widget so that ORCIDs for external parties can be discovered and referenced in data collection records.

![](https://qcifltd.atlassian.net/wiki/download/attachments/110886921/Screen%20Shot%202016-11-22%20at%204.06.57%20pm.png?version=1&modificationDate=1479793275518&cacheVersion=1&api=v2)