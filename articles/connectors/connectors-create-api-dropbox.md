<properties
pageTitle="Dropbox | Microsoft Azure"
description="Create Logic apps with Azure App service. Connect to Dropbox to manage your files. You can perform various actions such as upload, update, get, and delete files in Dropbox."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>


# <a name="get-started-with-the-dropbox-connector"></a>Get started with the Dropbox connector

Connect to Dropbox to manage your files. You can perform various actions such as upload, update, get, and delete files in Dropbox.

To use [any connector](./apis-list.md), you first need to create a logic app. You can get started by [creating a Logic app now](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Connect to Dropbox

Before your logic app can access any service, you first need to create a *connection* to the service. A connection provides connectivity between a logic app and another service. For example, in order to connect to Dropbox, you first need a Dropbox *connection*. To create a connection, you would need to provide the credentials you normally use to access the service you wish to connect to. So, in the Dropbox example, you would need the credentials to your Dropbox account in order to create the connection to Dropbox. [Learn more about connections]()

### <a name="create-a-connection-to-dropbox"></a>Create a connection to Dropbox

>[AZURE.INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]

## <a name="use-a-dropbox-trigger"></a>Use a Dropbox trigger

A trigger is an event that can be used to start the workflow defined in a logic app. [Learn more about triggers](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

In this example, we will use the **When a file is created** trigger. When this trigger occurs, we will call the **Get file content using path** Dropbox action. 

1. Enter *dropbox* in the search box on the Logic Apps designer, then select the **Dropbox - When a file is created** trigger.      
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
  
2. Select the folder in which you want to track file creation. Select ... (identified in the red box) and browse to the folder you wish to select for the trigger's input.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Use a Dropbox action

An action is an operation carried out by the workflow defined in a logic app. [Learn more about actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Now that the trigger has been added, follow these steps to add an action that will get the new file's content.

1. Select **+ New Step** to add the action you would like to take when a new file is created.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)

2. Select **Add an action**. This opens the search box where you can search for any action you would like to take.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)

3. Enter *dropbox* to search for actions related to Dropbox.  

4. Select **Dropbox - Get file content using path** as the action to take when a new file is created in the selected Dropbox folder. The action control block opens. You will be prompted to authorize your logic app to access your Dropbox account if you have not done so previously.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  

5. Select ... (located at the right side of the **File Path** control) and browse to the file path you would like to use. Or, use the **file path** token to speed up your logic app creation.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  

6. Save your work and create a new file in Dropbox to activate your workflow.  

## <a name="technical-details"></a>Technical details

Here are the details about the triggers, actions and responses that this connection supports:

## <a name="dropbox-triggers"></a>Dropbox triggers

The Dropbox connector has the following trigger(s):  

|Trigger | Description|
|--- | ---|
|[When a file is created](connectors-create-api-dropbox.md#when-a-file-is-created)|This operation triggers a flow when a new file is created in a folder.|
|[When a file is modified](connectors-create-api-dropbox.md#when-a-file-is-modified)|This operation triggers a flow when a file is modified in a folder.|

## <a name="dropbox-actions"></a>Dropbox actions

The Dropbox connector has the following actions:

|Action|Description|
|--- | ---|
|[Get file metadata](connectors-create-api-dropbox.md#get-file-metadata)|This operation gets the metadata for a file.|
|[Update file](connectors-create-api-dropbox.md#update-file)|This operation updates a file.|
|[Delete file](connectors-create-api-dropbox.md#delete-file)|This operation deletes a file.|
|[Get file metadata using path](connectors-create-api-dropbox.md#get-file-metadata-using-path)|This operation gets the metadata of a file using the path.|
|[Get file content using path](connectors-create-api-dropbox.md#get-file-content-using-path)|This operation gets the content of a file using the path.|
|[Get file content](connectors-create-api-dropbox.md#get-file-content)|This operation gets the content of a file.|
|[Create file](connectors-create-api-dropbox.md#create-file)|This operation creates a file.|
|[Copy file](connectors-create-api-dropbox.md#copy-file)|This operation copies a file to Dropbox.|
|[List files in folder](connectors-create-api-dropbox.md#list-files-in-folder)|This operation gets the list of files and subfolders in a folder.|
|[List files in root folder](connectors-create-api-dropbox.md#list-files-in-root-folder)|This operation gets the list of files and subfolders in the root folder.|
|[Extract archive to folder](connectors-create-api-dropbox.md#extract-archive-to-folder)|This operation extracts an archive file into a folder (example: .zip).|

### <a name="action-details"></a>Action details

Here are the details for the actions and triggers for this connector, along with their responses:


### <a name="get-file-metadata"></a>Get file metadata
This operation gets the metadata for a file. 

                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Select a file|

An * indicates that a property is required

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="update-file"></a>Update file
This operation updates a file. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Select a file|
|body*|File content|Content of the file|

An * indicates that a property is required

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="delete-file"></a>Delete file
This operation deletes a file. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Select a file|

An * indicates that a property is required




### <a name="get-file-metadata-using-path"></a>Get file metadata using path
This operation gets the metadata of a file using the path. 


|Property Name| Display Name|Description|
| ---|---|---|
|path*|File path|Select a file|

An * indicates that a property is required

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="get-file-content-using-path"></a>Get file content using path
This operation gets the content of a file using the path. 


|Property Name| Display Name|Description|
| ---|---|---|
|path*|File path|Select a file|

An * indicates that a property is required




### <a name="get-file-content"></a>Get file content
This operation gets the content of a file. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Select a file|

An * indicates that a property is required




### <a name="create-file"></a>Create file
This operation creates a file. 


|Property Name| Display Name|Description|
| ---|---|---|
|folderPath*|Folder path|Select a folder|
|name*|File name|Name of the file|
|body*|File content|Content of the file|

An * indicates that a property is required

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="copy-file"></a>Copy file
This operation copies a file to Dropbox. 


|Property Name| Display Name|Description|
| ---|---|---|
|source*|Source url|Url to source file|
|destination*|Destination file path|Destination file path, including target filename|
|overwrite|Overwrite?|Overwrites the destination file if set to 'true'|

An * indicates that a property is required

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="when-a-file-is-created"></a>When a file is created
This operation triggers a flow when a new file is created in a folder. 


|Property Name| Display Name|Description|
| ---|---|---|
|folderId*|Folder|Select a folder|

An * indicates that a property is required




### <a name="when-a-file-is-modified"></a>When a file is modified
This operation triggers a flow when a file is modified in a folder. 


|Property Name| Display Name|Description|
| ---|---|---|
|folderId*|Folder|Select a folder|

An * indicates that a property is required




### <a name="list-files-in-folder"></a>List files in folder
This operation gets the list of files and subfolders in a folder. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|Folder|Select a folder|

An * indicates that a property is required



#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="list-files-in-root-folder"></a>List files in root folder
This operation gets the list of files and subfolders in the root folder. 


There are no parameters for this call

#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### <a name="extract-archive-to-folder"></a>Extract archive to folder
This operation extracts an archive file into a folder (example: .zip). 


|Property Name| Display Name|Description|
| ---|---|---|
|source*|Source archive file path|Path to the archive file|
|destination*|Destination folder path|Path to extract the archive contents|
|overwrite|Overwrite?|Overwrites the destination files if set to 'true'|

An * indicates that a property is required



#### <a name="output-details"></a>Output Details

BlobMetadata


| Property Name | Data Type |
|---|---|
|Id|string|
|Name|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Size|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|



## <a name="http-responses"></a>HTTP responses

The actions and triggers above can return one or more of the following HTTP status codes: 

| Name | Description |
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occurred.|
|default|Operation Failed.|


## <a name="next-steps"></a>Next steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md)


<!--HONumber=Oct16_HO2-->


