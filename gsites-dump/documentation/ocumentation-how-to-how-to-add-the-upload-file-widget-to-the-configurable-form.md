1. Ensure that a new attachment type is configured in the uploader
The existing attachment-upload type used in the data collection form (present in the /default portal) is not generic enough to be used by other forms. To allow a widget to be used more flexibly, a new upload type and rules file called "attachment-file" was created. To your system configuration alongside the existing attachment-config (under the uploader key, [uploader.json](https://github.com/redbox-mint/redbox-build-distro/blob/master/src/main/config/home/config-include/1-main-modules/uploader.json)) configuration add:



``  ``"attachment-file": { 
       "screen-label": "Attachment", 
        "security": [ 
         "librarian", 
         "reviewer", 
         "researcher", 
         "admin" 
       ], 
      "upload-path": "${fascinator.home}/uploads/attachments", 
      "json-config": "${fascinator.home}/harvest/workflows/attachment-file.json" 
    },



## []()2. Configure your form to use the attachment-file and file-upload-table widgets

The upload file widget is actually split into two widgets so that it's layout is flexibile:

1. attachment-file: This is the widget that allows the user to select a file from the file system and upload it to the system
1. file-upload-table: This widget provides a table that shows the existing files that are attached to the record.

In your form configuration json add the following:



  {
    "component-type": "file-attachments",
    "widgetname": "**<widget_name>**",
    "attachType": "**<attachment_name>**",
    "accessRights": "public",
    "enableDescription": "true",
    "addFunction": "add**<upload_model>**Row"
  },
  {
    "component-type": "file-upload-table",
    "metadataPrefix": "**<uploadMetadataPrefix>**",
    "modelName": "**<upload_model>**",
    "tableName": " **<upload_table>**",
    "initialiseFunctionName": "**<widget_name>**_getUploadedFiles",
    "removeFunction": "**<widget_name>**_deleteFile"
  }



Where:
* **widget_name** is a unique name to identify this widget
* **attachment_name** is a unique name to identify the attachment type
* **upload_mode**l is a unique name to used to build the model (the table is rendered using [KnockoutJS](http://knockoutjs.com/) and the model is the MVC model)
* **upload_table** is a unique name to identify the table.

A working configuration example:


            {
              "component-type": "file-attachments",
              "widgetname": "storage_attachment",
              "attachType": "storage-attachments",
              "accessRights": "public",
              "enableDescription": "true",
              "addFunction": "addfileUploadModelRow"
            },
            {
              "component-type": "file-upload-table",
              "metadataPrefix": "storageFileUpload",
              "modelName": "fileUploadModel",
              "tableName": " fileUploadTable",
              "initialiseFunctionName": "storage_attachment_getUploadedFiles",
              "removeFunction": "storage_attachment_deleteFile"
 
            }


## []()Result

Once the configuration is complete, the form should render the file upload widget and the should be able to upload and remove files from a record


[![](https://sites.google.com/a/redboxresearchdata.com.au/public/_/rsrc/1491291399370/documentation/how-to/how-to-add-the-upload-file-widget-to-the-configurable-form/Screen%20Shot%202017-04-04%20at%205.04.21%20pm.png)
](documentation-how-to-how-to-add-the-upload-file-widget-to-the-configurable-form-Screen%20Shot%202017-04-04%20at%205-04-21%20pm-png?attredirects=0)