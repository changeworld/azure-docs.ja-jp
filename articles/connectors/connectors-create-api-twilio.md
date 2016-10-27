<properties
pageTitle="Add the Twilio Connector in your Logic apps| Microsoft Azure"
description="Overview of the Twilio Connector with REST API parameters"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="09/19/2016"
ms.author="mandia"/>


# <a name="get-started-with-the-twilio-connector"></a>Get started with the Twilio connector

Connect to Twilio to send and receive global SMS, MMS, and IP messages.

>[AZURE.NOTE] This version of the article applies to logic apps 2015-08-01-preview schema version.

With Twilio, you can:

- Build your business flow based on the data you get from Twilio. 
- Use actions that get a message, list messages, and more. These actions get a response, and then make the output available for other actions. For example, when  you get a new Twilio message, you can take this message and use it a Service Bus workflow. 

To add an operation in logic apps, see [Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Triggers and actions
The Twilio Connector includes the following actions. There are no triggers. 

| Triggers | Actions|
| --- | --- |
|None| <ul><li>Get Message</li><li>List Messages</li><li>Send Message</li></ul>|

All connectors support data in JSON and XML formats. 

## <a name="create-a-connection-to-twilio"></a>Create a connection to Twilio
When you add this Connector to your logic apps, enter the following Twilio values:

|Property| Required|Description|
| ---|---|---|
|Account ID|Yes|Enter your Twilio account ID|
|Access Token|Yes|Enter your Twilio access token|

>[AZURE.INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)] 

If you don't have one, see [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) to create an access token.


>[AZURE.TIP] You can use this same Twilio connection in other Logic apps.

## <a name="swagger-rest-api-reference"></a>Swagger REST API reference
#### <a name="this-documentation-is-for-version:-1.0"></a>This documentation is for version: 1.0

### <a name="get-message"></a>Get Message
Returns a single message specified by the provided Message ID.  
```GET: /Messages/{MessageId}.json```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|MessageId|string|yes|path|none|Message ID|

### <a name="response"></a>Response
|Name|Description|
|---|---|
|200|Operation successful|
|400|Bad Request|
|404|Message not found|
|500|Internal Server Error. Unknown error occurred|
|default|Operation Failed.|


### <a name="list-messages"></a>List Messages
Returns a list of messages associated with your account.  
```GET: /Messages.json```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|To|string|no|query|none|To phone number|
|From|string|no|query|none|From phone number|
|DateSent|string|no|query|none|Only show messages sent on this date (in GMT format), given as YYYY-MM-DD. Example: DateSent=2009-07-06. You can also specify inequality, such as DateSent<=YYYY-MM-DD for messages that were sent on or before midnight on a date, and DateSent>=YYYY-MM-DD for messages sent on or after midnight on a date.|
|PageSize|integer|no|query|50|How many resources to return in each list page. Default is 50.|
|Page|integer|no|query|0|Page number. Default is 0.|

### <a name="response"></a>Response
|Name|Description|
|---|---|
|200|Operation successful|
|400|Bad Request|
|500|Internal Server Error. Unknown error occured|
|default|Operation Failed.|



### <a name="send-message"></a>Send Message
Send a new message to a mobile number.  
```POST: /Messages.json```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|sendMessageRequest| |yes|body|none|Message To Send|

### <a name="response"></a>Response
|Name|Description|
|---|---|
|200|Operation successful|
|400|Bad Request|
|500|Internal Server Error. Unknown error occurred|
|default|Operation Failed.|


## <a name="object-definitions"></a>Object definitions

#### <a name="sendmessagerequest:-request-model-for-send-message-operation"></a>SendMessageRequest: Request model for Send Message operation

|Property Name | Data Type | Required|
|---|---|---|
|from|string|yes|
|to|string|yes|
|body|string|yes|
|media_url|array|no|
|status_callback|string|no|
|messaging_service_sid|string|no|
|application_sid|string|no|
|max_price|string|no|


#### <a name="message:-model-for-message"></a>Message: Model for Message

|Property Name | Data Type |Required|
|---|---|---|
|body|string|no|
|from|string|no|
|to|string|no|
|status|string|no|
|sid|string|no|
|account_sid|string|no|
|api_version|string|no|
|num_segments|string|no|
|num_media|string|no|
|date_created|string|no|
|date_sent|string|no|
|date_updated|string|no|
|direction|string|no|
|error_code|string|no|
|error_message|string|no|
|price|string|no|
|price_unit|string|no|
|uri|string|no|
|subresource_uris|array|no|
|messaging_service_sid|string|no|

#### <a name="messagelist:-response-model-for-list-messages-operation"></a>MessageList: Response model for List Messages operation

|Property Name | Data Type |Required|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page_size|integer|no|
|num_pages|integer|no|
|uri|string|no|
|first_page_uri|string|no|
|next_page_uri|string|no|
|total|integer|no|
|previous_page_uri|string|no|

#### <a name="incomingphonenumberlist:-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: Response model for List Messages operation

|Property Name | Data Type |Required|
|---|---|---|
|incoming_phone_numbers|array|no|
|page|integer|no|
|page_size|integer|no|
|num_pages|integer|no|
|uri|string|no|
|first_page_uri|string|no|
|next_page_uri|string|no|


#### <a name="addincomingphonenumberrequest:-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: Request model for Add Incoming Number operation

|Property Name | Data Type |Required|
|---|---|---|
|PhoneNumber|string|yes|
|AreaCode|string|no|
|FriendlyName|string|no|


#### <a name="incomingphonenumber:-incoming-phone-number"></a>IncomingPhoneNumber: Incoming Phone Number

|Property Name | Data Type |Required|
|---|---|---|
|phone_number|string|no|
|friendly_name|string|no|
|sid|string|no|
|account_sid|string|no|
|date_created|string|no|
|date_updated|string|no|
|capabilities|not defined|no|
|status_callback|string|no|
|status_callback_method|string|no|
|api_version|string|no|


#### <a name="capabilities:-phone-number-capabilities"></a>Capabilities: Phone Number Capabilities

|Property Name | Data Type |Required|
|---|---|---|
|mms|boolean|no|
|sms|boolean|no|
|voice|boolean|no|

#### <a name="availablephonenumbers:-available-phone-numbers"></a>AvailablePhoneNumbers: Available Phone Numbers

|Property Name | Data Type |Required|
|---|---|---|
|phone_number|string|no|
|friendly_name|string|no|
|lata|string|no|
|latitude|string|no|
|longitude|string|no|
|postal_code|string|no|
|rate_center|string|no|
|region|string|no|
|MMS|boolean|no|
|SMS|boolean|no|
|voice|boolean|no|


#### <a name="usagerecords:-usage-records-class"></a>UsageRecords: Usage Records class

|Property Name | Data Type |Required|
|---|---|---|
|category|string|no|
|usage|string|no|
|usage_unit|string|no|
|description|string|no|
|price|number|no|
|price_unit|string|no|
|count|string|no|
|count_unit|string|no|
|start_date|string|no|
|end_date|string|no|


## <a name="next-steps"></a>Next Steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md)



<!--HONumber=Oct16_HO2-->


