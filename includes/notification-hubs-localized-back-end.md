---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 527567ee3f3a939c7358fb6a62271cbe38e16974
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42915159"
---
テンプレート通知を送信する場合、必要なことは一連のプロパティを提供することだけです。 このシナリオでは、一連プロパティには現在のニュースのローカライズされたバージョンが格納されます。

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>C# コンソール アプリケーションを使用して通知を送信する

このセクションでは、コンソール アプリケーションを使用して通知を送信する方法について説明します。 コードは、Windows ストア デバイスと iOS デバイスの両方に通知をブロードキャストします。 次のコードを使用して、前に作成したコンソール アプリ内の `SendTemplateNotificationAsync` メソッドを変更します。

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

SendTemplateNotificationAsync メソッドは、ニュースのローカライズされた部分を、プラットフォームに関係なく、**すべて**のデバイスに配信します。 通知ハブは、正しいネイティブ ペイロードをビルドし、それを特定のタグにサブスクライブされているすべてのデバイスに配信します。

### <a name="sending-notification-with-mobile-services"></a>Mobile Services を使用した通知の送信

Mobile Services スケジューラで、次のスクリプトを使用します。

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
