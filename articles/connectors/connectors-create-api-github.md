<properties
pageTitle="GitHub | Microsoft Azure"
description="Create Logic apps with Azure App service. GitHub is a web-based Git repository hosting service. It offers all of the distributed revision control and source code management (SCM) functionality of Git as well as adding its own features."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>


# <a name="get-started-with-the-github-connector"></a>Get started with the GitHub connector

GitHub is a web-based Git repository hosting service. It offers all of the distributed revision control and source code management (SCM) functionality of Git as well as adding its own features.

>[AZURE.NOTE] This version of the article applies to logic apps 2015-08-01-preview schema version. 

You can get started by creating a Logic app now, see [Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Triggers and actions

The GitHub connector can be used as an action; it has trigger(s). All connectors support data in JSON and XML formats. 

 The GitHub connector has the following action(s) and/or trigger(s) available:

### <a name="github-actions"></a>GitHub actions
You can take these action(s):

|Action|Description|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Creates an issue|
### <a name="github-triggers"></a>GitHub triggers
You can listen for these event(s):

|Trigger | Description|
|--- | ---|
|When an issue is opened|An issue is opened|
|When an issue is closed|An issue is closed|
|When an issue is assigned|An issue is assigned|


## <a name="create-a-connection-to-github"></a>Create a connection to GitHub
To create Logic apps with GitHub, you must first create a **connection** then provide the details for the following properties: 

|Property| Required|Description|
| ---|---|---|
|Token|Yes|Provide GitHub Credentials|
After you create the connection, you can use it to execute the actions and listen for the triggers described in this article. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] You can use this connection in other logic apps.

## <a name="reference-for-github"></a>Reference for GitHub
Applies to version: 1.0

## <a name="createissue"></a>CreateIssue
Create an issue: Creates an issue 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|repositoryOwner|string|yes|path|none|Repository owner|
|repositoryName|string|yes|path|none|Repository name|
|issueBasicDetails| |yes|body|none|Issue details|

#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|OK|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occured|
|default|Operation Failed.|


## <a name="issueopened"></a>IssueOpened
When an issue is opened: An issue is opened 

```GET: /trigger/issueOpened``` 

There are no parameters for this call
#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|OK|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occured|
|default|Operation Failed.|


## <a name="issueclosed"></a>IssueClosed
When an issue is closed: An issue is closed 

```GET: /trigger/issueClosed``` 

There are no parameters for this call
#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|OK|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occured|
|default|Operation Failed.|


## <a name="issueassigned"></a>IssueAssigned
When an issue is assigned: An issue is assigned 

```GET: /trigger/issueAssigned``` 

There are no parameters for this call
#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|OK|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occured|
|default|Operation Failed.|


## <a name="object-definitions"></a>Object definitions 

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Property Name | Data Type | Required |
|---|---|---|
|title|string|Yes |
|body|string|Yes |
|assignee|string|Yes |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Property Name | Data Type | Required |
|---|---|---|
|title|string|Yes |
|body|string|Yes |
|assignee|string|Yes |
|number|string|No |
|state|string|No |
|created_at|string|No |
|repository_url|string|No |


## <a name="next-steps"></a>Next Steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md)


<!--HONumber=Oct16_HO2-->


