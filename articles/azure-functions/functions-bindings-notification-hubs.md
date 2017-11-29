---
title: "Azure Functions ÇRÆğÆ÷Çr³qª¾ÇËÇÒÇUÇÌÇ~ÇïÇÅ | Microsoft Docs"
description: "Azure Functions ÇN Azure Notification Hub ÇUÇÌÇ~ÇïÇÅÇy¨Ï¥ÎÇ@Çr¤èªkÇRÇKÆêÇM?©úÆıÇeÇ@¡C"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, ??, Ç~ÇÕÇïÇÄ?²z, °ÊªºÇ¯ÇïÇĞÇáÆãÇÂÇ}ÇïÇ¬, Ç±ÆãÇÌÆãÇQÆıÇUÇ|ÆãÇ©ÇÂÇ«Ç½Çß"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions ÇRÆğÆ÷Çr³qª¾ÇËÇÒÇU¥X¤OÇÌÇ~ÇïÇÅ
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

ÆùÇU°O¨ÆÇNÇV¡BAzure Functions ÇN Azure Notification Hub ÇUÇÌÇ~ÇïÇÅÇyºc¦¨ÆıÇFÇqÇ¯ÆãÇÃÇ}ÇïÇ¬ÆıÇFÇqÇ@Çr¤èªkÇRÇKÆêÇM?©úÆıÇeÇ@¡C 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

??Çy¨Ï¥ÎÆıÇM¡Bºc¦¨ÆûÇsÇF Azure Notification Hub ÇN?¦æÇUÇ¯ÆãÇÅÇy¨ÏÇJÇMÇÓÇ¿Ç³Çá³qª¾Çy°e«HÇNÆóÇeÇ@¡C ÇFÇGÆı¡BAzure Notification Hub ÇV¡B¨Ï¥ÎÇ@ÇrÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜ³qª¾Ç±ÆãÇÏÇµ (PNS) ¥ÎÇRºc¦¨Ç@Çr¥²­nÆòÆèÇqÇeÇ@¡C Azure Notification Hub ÇUºc¦¨¤èªkÇk¡B³qª¾Çy¨üÆ÷¨úÇrÇFÇhÇRµn?Ç@ÇrÇ«ÇåÇ~Ç|ÇïÇÄ Ç|ÇÓÇæÇ­ÆãÇ³ÇãÇïÇU¶}?¤èªkÇU¸Ô²ÓÇRÇKÆêÇMÇV¡B [Notification Hubs ÇU¨Ï¥Î](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) ÇR?Ç@ÇrÇÖÆãÇ´Çy?·ÓÆıÇM¡B¤W³¡ÇU¥ØªºÇUÇ«ÇåÇ~Ç|ÇïÇÄ ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇyÇ«ÇæÇ¿Ç«ÆıÇMÆõÇGÆûÆê¡C

°e«HÇNÆóÇr³qª¾ÇV¡BÇÉÇ~ÇÂÇ}ÇÒ³qª¾ÇeÇFÇVÇÂÇïÇÓÇèÆãÇÄ³qª¾ÇNÇ@¡C ÇÉÇ~ÇÂÇ}ÇÒ³qª¾ÇV¡B¥X¤OÇÌÇ~ÇïÇÅÇU `platform` ÇÓÇéÇÍÇÂÇ}ÇNºc¦¨ÆûÇsÇF¯S©wÇU³qª¾ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇy?¶HÇOÆıÇMÆêÇeÇ@¡C ÇÂÇïÇÓÇèÆãÇÄ³qª¾ÇV¡B½Æ?ÇUÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇy?¶HÇOÆıÇM¨Ï¥ÎÇNÆóÇeÇ@¡C   

## <a name="notification-hub-output-binding-properties"></a>Notification Hub ¥X¤OÇÌÇ~ÇïÇÅÇUÇÓÇéÇÍÇÂÇ}
function.json ÇÑÇ{Ç~ÇçÇV¡B¦¸ÇUÇÓÇéÇÍÇÂÇ}Çy´£¨ÑÆıÇeÇ@¡C


|ÇÓÇéÇÍÇÂÇ}  |Description  |
|---------|---------|
|**name** | ³qª¾ÇËÇÒ ÇİÇ¿Ç·ÆãÇ´ÇU??Ç¯ÆãÇÅÇN¨Ï¥ÎÆûÇsÇr??¦W¡C |
|**type** | `notificationHub` ÇR³]©wÇ@Çr¥²­nÆòÆèÇqÇeÇ@¡C |
|**tagExpression** | Ç»Ç¬¦¡¡CÆùÇsÇRÇoÇq¡BÇ»Ç¬¦¡ÇR¤@­PÇ@Çr³qª¾Çy¨ü«HÇ@ÇrÇoÆìÇRµn?ÆıÇF¤@³sÇUÇÃÇÌÇ~ÇµÇR³qª¾Çy°t«HÇ@ÇrÇoÆìÇR«ü©wÇNÆóÇeÇ@¡C  ¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[ÇçÆãÇÂÇ}ÇïÇ¬ÇOÇ»Ç¬¦¡](../notification-hubs/notification-hubs-tags-segment-push-message.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C |
|**hubName** | Azure Portal ?ÇU³qª¾ÇËÇÒ ÇæÇ¹ÆãÇµÇU¦W«e¡C |
|**connection** | ÆùÇU±µ?¤å¦r¦CÇV¡B¨Ï¥ÎÆıÇMÆêÇr³qª¾ÇËÇÒÇU *DefaultFullSharedAccessSignature* ?ÇR³]©wÆûÇsÇF**Ç|ÇÓÇæÇ­ÆãÇ³ÇãÇï³]©w**ÇU±µ?¤å¦r¦CÇNÆèÇr¥²­nÆòÆèÇqÇeÇ@¡C |
|**direction** | `out` ÇR³]©wÇ@Çr¥²­nÆòÆèÇqÇeÇ@¡C | 
|**platform** | platform ÇÓÇéÇÍÇÂÇ}ÇV¡B³qª¾ÇU?¶HÇOÇQÇr³qª¾ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇy¥ÜÆıÇeÇ@¡C ¬J©wÇNÇV¡BÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇUÇÓÇéÇÍÇÂÇ}Æò¥X¤OÇÌÇ~ÇïÇÅÆñÇp¬Ù²¤ÆûÇsÇMÆêÇr³õ¦X¡BÇÂÇïÇÓÇèÆãÇÄ³qª¾ÇV¡BAzure ³qª¾ÇËÇÒÇNºc¦¨ÆûÇsÇMÆêÇr¥ô·NÇUÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇy?¶HÇOÆıÇM¨Ï¥ÎÇNÆóÇeÇ@¡C Azure Notification Hub ÇNÇÂÇïÇÓÇèÆãÇÄÇy¨Ï¥ÎÆıÇMÇ«ÇéÇµÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇU³qª¾Çy°e«HÇ@Çr¤@¯ëªºÇQ¤èªkÇU¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Templates (ÇÂÇïÇÓÇèÆãÇÄ)](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C ³]©wÆûÇsÇMÆêÇr³õ¦X¡B_platform_ ÇRÇV¡B¦¸ÇUÆêÇAÇsÆñÇU?Çy«ü©wÇ@Çr¥²­nÆòÆèÇqÇeÇ@¡C <ul><li><code>apns</code>&mdash;Apple Push Notification Service¡C APNS ¦VÆ÷ÇR Notification Hub Çyºc¦¨Ç@Çr¤èªkÇO¡BÇ«ÇåÇ~Ç|ÇïÇÄ Ç|ÇÓÇæÇN³qª¾Çy¨ü«HÇ@Çr¤èªkÇU¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Azure Notification Hubs ÆñÇp iOS Ç_ÇUÇÓÇ¿Ç³Çá³qª¾ÇU°e«H](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging)¡C ADM ¦VÆ÷ÇR Notification Hub Çyºc¦¨Ç@Çr¤èªkÇO¡BKindle Ç|ÇÓÇæÇN³qª¾Çy¨ü«HÇ@Çr¤èªkÇU¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Notification Hubs ÇU¨Ï¥Î (Kindle Ç|ÇÓÇæ)](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/)¡C GCM ÇU·sÆıÆêÇÌÆãÇ´ÇãÇïÇNÆèÇr Firebase Cloud Messaging ÇiÇ±ÇÙÆãÇÄÆûÇsÇeÇ@¡C ¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Azure Notification Hubs ÆñÇp Android Ç_ÇUÇÓÇ¿Ç³Çá³qª¾ÇU°e«H](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C</li><li><code>wns</code>&mdash;Windows ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜ¦VÆ÷ÇU [Windows Push Notification Services](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)¡C WNS ÇNÇV Windows Phone 8.1 ¥H­°ÇiÇ±ÇÙÆãÇÄÆûÇsÇeÇ@¡C ¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Notification Hubs ÇU¨Ï¥Î (Windows ÇâÇÇÇÌÆãÇ±Çç ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜ Ç|ÇÓÇæ)](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)¡C ÆùÇUÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÇNÇV¡BWindows Phone 8 ÆğÇoÇZÇDÇs¥H«eÇU Windows Phone ÇÓÇåÇ¿ÇÄÇÑÇ¥ÆãÇÜÆòÇ±ÇÙÆãÇÄÆûÇsÇeÇ@¡C ¸Ô²ÓÇRÇKÆêÇMÇV¡B¡u[Windows Phone ÇNÇU Azure Notification Hubs Çy¨Ï¥ÎÆıÇFÇÓÇ¿Ç³Çá³qª¾ÇU°e«H](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)¡vÇy?·ÓÆıÇMÆõÇGÆûÆê¡C</li></ul> |

function.json ÇU¨Ò:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Notification Hub ÇU±µ?¤å¦r¦CÇU³]©w
Notification Hub ¥X¤OÇÌÇ~ÇïÇÅÇy¨Ï¥ÎÇ@ÇrÇRÇV¡BÇËÇÒÇU±µ?¤å¦r¦CÇy³]©wÇ@Çr¥²­nÆòÆèÇqÇeÇ@¡C ¬J¦sÇU³qª¾ÇËÇÒÇy¿ï?Ç@ÇrÆñ¡B??ÇU "*²Î¦X*" Ç»ÇÒÆñÇp·sÆıÆêÇËÇÒÇy§@¦¨ÇNÆóÇeÇ@¡C ±µ?¤å¦r¦CÇy¤â°ÊÇNºc¦¨Ç@ÇrÆùÇOÇiÇNÆóÇeÇ@¡C 

¬J¦sÇU³qª¾ÇËÇÒÇR?Ç@Çr±µ?¤å¦r¦CÇyºc¦¨Ç@ÇrÇRÇV:

1. [Azure Portal](https://portal.azure.com) ÇN³qª¾ÇËÇÒÇR²¾°ÊÆı¡B**[Ç|Ç«Ç·ÇµÇÙÇæÇ³Æã]** Çy¿ï?ÆıÇM¡B**[DefaultFullSharedAccessSignature]** ÇÙÇæÇ³ÆãÇU?ÇRÆèÇrÇ¯ÇĞÆã ÇØÇ»ÇïÇy¿ï?ÆıÇeÇ@¡C ÆùÇsÇRÇoÇq¡B*DefaultFullSharedAccessSignature* ÇÙÇæÇ³ÆãÇU±µ?¤å¦r¦CÆò³qª¾ÇËÇÒÇRÇ¯ÇĞÆãÆûÇsÇeÇ@¡C ÆùÇU±µ?¤å¦r¦CÇRÇoÇq¡B³qª¾ÇİÇ¿Ç·ÆãÇ´Çy°e«HÇ@ÇrÇFÇhÇU??Ç|Ç«Ç·Çµ?­­Æò¥IÉOÆûÇsÇeÇ@¡C 
    ![³qª¾ÇËÇÒÇU±µ?¤å¦r¦CÇyÇ¯ÇĞÆãÇ@Çr](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure Portal ÇU??Ç|ÇÓÇæÇR²¾°ÊÆı¡B**[Ç|ÇÓÇæÇ­ÆãÇ³ÇãÇï³]©w]** Çy¿ï?Æı¡B`MyHubConnectionString` ÇQÇPÇUÇ©ÆãÇy°l¥[ÆıÇeÇ@¡C¦¸ÇR¡B³qª¾ÇËÇÒ¥ÎÇRÇ¯ÇĞÆãÆûÇsÇF *DefaultFullSharedAccessSignature* Çy?ÇOÆıÇM¶KÇq¥IÆ÷ÇM¡B**[«O¦s]** ÇyÇ«ÇæÇ¿Ç«ÆıÇeÇ@¡C

ÆùÇsÇN¡B¥X¤OÇÌÇ~ÇïÇÅÇU³qª¾ÇËÇÒ±µ?Çy©w¸qÇ@Çr¡BÆùÇU¦W«e¥IÆóÇ|ÇÓÇæÇ­ÆãÇ³ÇãÇï³]©wÇy¨Ï¥ÎÇNÆóÇeÇ@¡C

## <a name="apns-native-notifications-with-c-queue-triggers"></a>C# Ç©ÇáÆã ÇÄÇæÇ¨ÆãÇy¨Ï¥ÎÆıÇF APNS ÇÉÇ~ÇÂÇ}ÇÒ³qª¾
¦¸ÇU¨ÒÇNÇV¡B[Microsoft Azure Notification Hubs ÇåÇ~ÇÒÇåÇæ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)ÇR©w¸qÆûÇsÇFÇ»Ç~ÇÓÇy¨Ï¥ÎÆıÇMÇÉÇ~ÇÂÇ}ÇÒÇU APNS ³qª¾Çy°e«HÇ@Çr¤èªkÇy¥ÜÆıÇeÇ@¡C 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>C# Ç©ÇáÆã ÇÄÇæÇ¨ÆãÇy¨Ï¥ÎÆıÇF GCM ÇÉÇ~ÇÂÇ}ÇÒ³qª¾
¦¸ÇU¨ÒÇNÇV¡B[Microsoft Azure Notification Hubs ÇåÇ~ÇÒÇåÇæ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)ÇR©w¸qÆûÇsÇFÇ»Ç~ÇÓÇy¨Ï¥ÎÆıÇMÇÉÇ~ÇÂÇ}ÇÒÇU GCM ³qª¾Çy°e«HÇ@Çr¤èªkÇy¥ÜÆıÇeÇ@¡C 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>C# Ç©ÇáÆã ÇÄÇæÇ¨ÆãÇy¨Ï¥ÎÆıÇF WNS ÇÉÇ~ÇÂÇ}ÇÒ³qª¾
¦¸ÇU¨ÒÇNÇV¡B[Microsoft Azure Notification Hubs ÇåÇ~ÇÒÇåÇæ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)ÇR©w¸qÆûÇsÇFÇ»Ç~ÇÓÇy¨Ï¥ÎÆıÇMÇÉÇ~ÇÂÇ}ÇÒÇU WNS ÇÄÆãÇµÇÄ³qª¾Çy°e«HÇ@Çr¤èªkÇy¥ÜÆıÇeÇ@¡C 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js Ç»Ç~ÇÚÆã ÇÄÇæÇ¨ÆãÇUÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
¦¸ÇU¨ÒÇNÇV¡B`location` ÇO `message` Çy§tÇg[ÇÂÇïÇÓÇèÆãÇÄµn?](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)ÇU³qª¾Çy°e«HÆıÇeÇ@¡C

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# Ç»Ç~ÇÚÆã ÇÄÇæÇ¨ÆãÇUÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
¦¸ÇU¨ÒÇNÇV¡B`location` ÇO `message` Çy§tÇg[ÇÂÇïÇÓÇèÆãÇÄµn?](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)ÇU³qª¾Çy°e«HÆıÇeÇ@¡C

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>out ÇÍÇåÇİÆãÇ»ÆãÇy¨Ï¥ÎÆıÇFÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
¦¸ÇU¨ÒÇNÇV¡BÇÂÇïÇÓÇèÆãÇÄÇR `message` ÇÓÇèÆãÇµ Ç×ÇçÇ¼ÆãÇy§tÇg[ÇÂÇïÇÓÇèÆãÇÄµn?](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)ÇU³qª¾Çy°e«HÆıÇeÇ@¡C

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>«D¦P´Á??Çy¨Ï¥ÎÇ@ÇrÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
«D¦P´ÁÇ¯ÆãÇÅÇy¨Ï¥ÎÆıÇMÆêÇr³õ¦XÇV¡Bout ÇÍÇåÇİÆãÇ»ÆãÇy¨Ï¥ÎÇNÆóÇeÇBÇz¡C ÇDÇU³õ¦XÇV¡B`IAsyncCollector` Çy¨Ï¥ÎÆıÇMÇÂÇïÇÓÇèÆãÇÄ³qª¾ÇyªğÆıÇeÇ@¡C ¦¸ÇUÇ¯ÆãÇÅÇNÇV¡B¤W°OÇUÇ¯ÆãÇÅÇU«D¦P´ÁÇU¨ÒÇy¥ÜÆıÇeÇ@¡C 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>JSON Çy¨Ï¥ÎÆıÇFÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
¦¸ÇU¨ÒÇNÇV¡B¦³?ÇQ JSON ¤å¦r¦CÇy¨Ï¥ÎÆıÇM¡BÇÂÇïÇÓÇèÆãÇÄÇR `message` ÇÓÇèÆãÇµ Ç×ÇçÇ¼ÆãÇy§tÇg[ÇÂÇïÇÓÇèÆãÇÄµn?](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)ÇU³qª¾Çy°e«HÆıÇeÇ@¡C

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Notification Hubs ÇåÇ~ÇÒÇåÇæÇUÇ»Ç~ÇÓÇy¨Ï¥ÎÆıÇFÇÂÇïÇÓÇèÆãÇÄÇU¨Ò
¦¸ÇU¨ÒÇNÇV¡B[Microsoft Azure Notification Hubs ÇåÇ~ÇÒÇåÇæ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)ÇR©w¸qÆûÇsÇFÇ»Ç~ÇÓÇU¨Ï¥Î¤èªkÇy¥ÜÆıÇeÇ@¡C 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>¦¸ÇUÇµÇÂÇ¿ÇÓ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

