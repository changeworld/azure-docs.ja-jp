<properties
pageTitle="Use the Office 365 Video connector in your Logic apps | Microsoft Azure"
description="Get started using the Office 365 Video connector in your Microsoft Azure App service Logic apps"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>


# <a name="get-started-with-the-office365-video-connector"></a>Get started with the Office365 Video connector
Connect to Office 365 Video to get information about an Office 365 video, get a list of videos, and more. The Office 365 Video connector can be used from:

- Logic apps 

>[AZURE.NOTE] This version of the article applies to logic apps 2015-08-01-preview schema version. This connector is not supported on any previous schema versions.

With Office 365 Video, you can:

- Build your business flow based on the data you get from Office 365 Video. 
- Use actions that check the video portal status, get a list of all video in a channel, and more. These actions get a response, and then make the output available for other actions. For example, you can use the Bing Search connector to search for Office 365 videos, and then use the Office 365 video connector to get information about that video. If the video meets your requirements, you can post this video on Facebook. 

To add an operation in logic apps, see [Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Triggers and actions

The Office 365 Video connector has the following actions available. There are no triggers.

| Triggers | Actions|
| --- | --- |
| None | <ul><li>Checks video portal status</li><li>Get all viewable Channels</li><li>Get playback url of the Azure Media Services manifest for a video</li><li>Get the bearer token to get access to decrypt the video</li><li>Gets information about a particular office365 video</li><li>Lists all the office365 videos present in a channel</li></ul>

All connectors support data in JSON and XML formats. 

## <a name="create-a-connection-to-office365-video-connector"></a>Create a connection to Office365 Video connector
When you add this connector to your logic apps, you must sign-in to your Office 365 Video account and allow logic apps to connect to your account.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

After you create the connection, you enter the Office 365 video properties, like the tenant name or channel ID. The **REST API reference** in this topic describes these properties.

>[AZURE.TIP] You can use this same Office 365 Video connection in other logic apps.

## <a name="swagger-rest-api-reference"></a>Swagger REST API reference
Applies to version: 1.0.

### <a name="checks-video-portal-status"></a>Checks video portal status 
Checks the video portal status to see if video services are enabled.  
```GET: /{tenant}/IsEnabled``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|



### <a name="get-all-viewable-channels"></a>Get all viewable Channels 
Gets all the channels the user has viewing access to.  
```GET: /{tenant}/Channels``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Lists all the office365 videos present in a channel 
Lists all the office365 videos present in a channel.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|
|channelId|string|yes|path|none|The channel id from which videos need to be fetched|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|




### <a name="gets-information-about-a-particular-office365-video"></a>Gets information about a particular office365 video 
Gets information about a particular office365 video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|
|channelId|string|yes|path|none|The channel id|
|videoId|string|yes|path|none|The video id|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Get playback url of the Azure Media Services manifest for a video 
Get playback url of the Azure Media Services manifest for a video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|
|channelId|string|yes|path|none|The channel id|
|videoId|string|yes|path|none|The video id|
|streamingFormatType|string|yes|query|none|Streaming format type. 1 - Smooth Streaming or MPEG-DASH. 0 - HLS Streaming|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Get the bearer token to get access to decrypt the video 
Get the bearer token to get access to decrypt the video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|tenant|string|yes|path|none|The tenant name for the directory the user is part of|
|channelId|string|yes|path|none|The channel id|
|videoId|string|yes|path|none|The video id|


#### <a name="response"></a>Response

|Name|Description|
|---|---|
|200|Operation was successful|
|400|BadRequest|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
|default|Operation Failed.|


## <a name="object-definitions"></a>Object definitions

#### <a name="channel:-channel-class"></a>Channel: Channel class

| Name | Data Type | Required|
|---|---|---|
|Id|string|no|
|Title|string|no|
|Description|string|no|


#### <a name="video"></a>Video 

| Name | Data Type |Required|
|---|---|---|
|Id|string|no|
|Title|string|no|
|Description|string|no|
|CreationDate|string|no|
|Owner|string|no|
|ThumbnailUrl|string|no|
|VideoUrl|string|no|
|VideoDuration|integer|no|
|VideoProcessingStatus|integer|no|
|ViewCount|integer|no|


## <a name="next-steps"></a>Next Steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).



<!--HONumber=Oct16_HO2-->


