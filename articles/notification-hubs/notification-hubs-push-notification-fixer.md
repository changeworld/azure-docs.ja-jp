---
title: Azure Notification Hubs の欠落した通知の診断
description: Azure Notification Hubs で欠落した通知に関する一般的な問題を診断する方法について説明します。
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 9dd6a66ea416ad61682b8e33c6163db3ac345d92
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447719"
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Notification Hubs での欠落した通知の診断

Azure Notification Hubs のお客様からよく寄せられる質問の 1 つに、アプリケーションから送信された通知がクライアント デバイスに表示されない場合のトラブルシューティングの方法があります。 お客様は、通知が欠落した場所と理由、さらに問題の解決方法を知りたいと考えます。 この記事では、通知が欠落する、つまりデバイスで受信されない理由を特定します。 根本原因を分析し、特定する方法を説明します。

最初に、Notification Hubs サービスがデバイスにどのようにプッシュ通知を送信するかを理解しておくことが重要です。

![Notification Hubs のアーキテクチャ][0]

一般的な送信通知フローでは、メッセージは*アプリケーション バックエンド*から Notification Hubs に送信されます。 Notification Hubs は、すべての登録に対して処理を行います。 この処理では、構成済みのタグとタグ式を考慮して "ターゲット" が決定されます。 ターゲットは、プッシュ通知を受け取る必要があるすべての登録です。 これらの登録は、iOS、Google、Windows、Windows Phone、Kindle、Baidu for China Android など、サポートされているプラットフォームのいずれかまたはすべてにわたる可能性があります。

ターゲットが確立されると、Notification Hubs サービスは、デバイス プラットフォームの*プッシュ通知サービス*にプッシュ通知を送信します。 例としては、Apple の Apple Push Notification Service (APNs) や、Google の Firebase Cloud Messaging (FCM) があります。 Notification Hubs のプッシュ通知は、複数の登録バッチに分割されます。 Notification Hubs は、Azure Portal の **[Configure Notification Hub]\(通知ハブを構成する\)** で設定された資格情報に基づいて、それぞれのプッシュ通知サービスを認証します。 その後、プッシュ通知サービスが各*クライアント デバイス*に通知を転送します。

通知の配信の最終段階は、プラットフォーム プッシュ通知サービスとデバイスの間で実行されます。 プッシュ通知プロセスの 4 つの主なコンポーネント (クライアント、アプリケーション バックエンド、Notification Hubs、プラットフォーム プッシュ通知サービス) のいずれかが、通知が欠落する原因となる場合があります。 Notification Hubs のアーキテクチャの詳細については、[Notification Hubs の概要]に関する記事をご覧ください。

通知の配信エラーは、初期のテストまたはステージングのフェーズで発生する可能性があります。 このフェーズでの通知の欠落は、構成に問題があることを示している可能性があります。 運用環境で通知の配信エラーが発生した場合、通知の一部またはすべてが欠落することがあります。 この場合、アプリケーションまたはメッセージングのパターンに関して、より難しい問題があることを示しています。

次のセクションでは、通知が欠落するシナリオを、一般的なケースから稀なケースまで見ていきます。

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs の構成の誤り

各プッシュ通知サービスに通知を正常に送信するには、Notification Hubs サービスが、開発者のアプリケーションのコンテキストで自身を認証する必要があります。 これを行うには、開発者は各プラットフォーム (Google、Apple、Windows など) に開発者アカウントを作成します。 次に、開発者は資格情報を取得するプラットフォームに自身のアプリケーションを登録します。

Azure Portal にプラットフォームの資格情報を追加する必要があります。 通知がデバイスに届いていない場合は、まず Notification Hubs に正しい資格情報が構成されていることを確認する必要があります。 この資格情報は、プラットフォーム固有の開発者アカウントで作成されたアプリケーションと一致している必要があります。

このプロセスを実行するための詳細な手順については、[Azure Notification Hubs の使用]に関する記事をご覧ください。

よくある構成の誤りをいくつか次に示します。

**全般:**

    * Notification Hubs 名にタイプミスがなく、次の各場所で同じであることを確認します。
        * クライアントから登録する場所
        * バックエンドから通知を送信する場所
        * プッシュ通知サービスの資格情報を構成した場所
    * クライアントとアプリケーション バックエンドで、適切な共有アクセス署名の構成文字列を使用していることを確認します。 原則として、クライアントでは **DefaultListenSharedAccessSignature** を、アプリケーション バックエンドでは **DefaultFullSharedAccessSignature** を使用する必要があります (これにより、Notification Hubs に通知を送信する権限が付与されます)。

**APNs の構成:**

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**FCM の構成:**

    1. Firebase から取得した *"サーバー キー"* が、Azure Portal で登録したサーバー キーと一致していることを確認します。

    ![Firebase サーバー キー][3]

    2. クライアントに **[プロジェクト ID]** が構成されていることを確認します。 **[プロジェクト ID]** の値は Firebase のダッシュボードから取得できます。

    ![Firebase プロジェクト ID][1]

## <a name="application-issues"></a>アプリケーションの問題

**タグとタグ式:**

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

**テンプレートの問題:**

    If you use templates, ensure that you follow the guidelines described in [Templates].

**無効な登録:**

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>プッシュ通知サービスの問題

プラットフォーム プッシュ通知サービスで通知メッセージが受け取られたら、その通知をデバイスに配信するのはプッシュ通知サービスの役割です。 この時点で、Notification Hubs サービスは無関係であり、通知がデバイスに配信されるタイミングや配信されるかどうかを制御できません。

プラットフォーム通知サービスは堅牢であるため、通知は通常数秒でプッシュ通知サービスからデバイスに届きます。 プッシュ通知サービスが調整中の場合は、Notification Hubs は指数バックオフ戦略を適用します。 プッシュ通知サービスが到達できない状態が 30 分間続いた場合には、それらのメッセージが期限切れとなり、完全に欠落するポリシーが適用されます。

プッシュ通知サービスが通知の配信を試行したがデバイスがオフラインの場合、その通知は特定の期間プッシュ通知サービスに保存されます。 この通知は、デバイスが利用可能になるとデバイスに配信されます。

アプリごとに、最新の通知が 1 つのみ保存されます。 デバイスがオフラインの間に複数の通知が送信された場合、新しい通知が配信されるたびに以前の通知が破棄されます。 最新の通知のみを維持するこの動作は、APNs では *"結合通知"* と呼ばれ、FCM では *"折りたたみ"* と呼ばれます (これには、折りたたみキーを使用します)。 デバイスが長時間オフラインのままになると、デバイスに保存されている通知は破棄されます。 詳細については、「[APNs の概要]」と [FCM メッセージについて]に関する記事をご覧ください。

Azure Notification Hubs では、汎用 SendNotification API を使用することで、HTTP ヘッダーを介して結合キーを渡すことができます。 たとえば、.NET SDK の場合、`SendNotificationAsync` を使用します。 SendNotification API では、それぞれのプッシュ通知サービスにそのまま渡される HTTP ヘッダーも使用します。

## <a name="self-diagnosis-tips"></a>自己診断のヒント

このセクションでは、Notification Hubs で通知の欠落の根本原因を診断する方法について説明します。

### <a name="verify-credentials"></a>資格情報の確認

**プッシュ通知サービス開発者ポータル:**

各プッシュ通知サービス開発者ポータル (APNs、FCM, Windows Notification Service など) で資格情報を確認します。 詳細については、[Azure Notification Hubs の使用]に関する記事をご覧ください。

**Azure portal:**

資格情報を確認し、プッシュ通知サービス開発者ポータルから取得した資格情報とマッチングするには、Azure Portal の **[アクセス ポリシー]** タブに移動します。

![Azure Portal のアクセス ポリシー][4]

### <a name="verify-registrations"></a>登録の確認

**Visual Studio:**

開発で Visual Studio を使用している場合は、サーバー エクスプローラーから Azure に接続して、Notification Hubs を含む複数の Azure サービスを表示、管理できます。 これは、特に開発/テスト環境で便利です。

![Visual Studio のサーバー エクスプローラー][9]

ハブのすべての登録を表示、管理でき、プラットフォーム、ネイティブまたはテンプレートの登録、タグ、プッシュ通知サービス識別子、登録 ID、有効期限ごとに見やすく分類されています。 このページでは、登録を編集することもできます。 これは、タグを編集する場合に特に便利です。

![Visual Studio のデバイス登録][8]

> [!NOTE]
> Visual Studio を使用した登録の編集は開発またはテスト中にのみ可能ですが、編集できる登録の数には制限があります。 登録を一括で修正する必要が生じた場合は、[登録の一括エクスポートと変更](https://msdn.microsoft.com/library/dn790624.aspx)に関する記事に説明されている、登録のエクスポート/インポート機能を使用することを検討してください。

**Service Bus Explorer:**

多くのお客様が [Service Bus Explorer] を使用して、通知ハブを表示、管理します。 Service Bus Explorer はオープンソースのプロジェクトです。 サンプルについては、[Service Bus Explorer のコード]に関する記事をご覧ください。

### <a name="verify-message-notifications"></a>メッセージ通知の確認

 **Azure portal:**

サービス バックエンドを稼働させずにクライアントにテスト通知を送信するには、**[サポート + トラブルシューティング]** から **[テスト送信]** を選択します。

![Azure のテスト送信機能][7]

**Visual Studio:**

Visual Studio からテスト通知を送信することもできます。

![Visual Studio のテスト送信機能][10]

Visual Studio サーバー エクスプローラーでの Notification Hubs の使用に関する詳細については、次の記事をご覧ください。

* [通知ハブのデバイス登録の表示]
* [詳細:Visual Studio 2013 Update 2 RC および Azure SDK 2.3]
* [Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>失敗した通知のデバッグと通知結果の確認

**`EnableTestSend` プロパティ:**

Notification Hubs 経由で通知を送信すると、その通知は処理のために Notification Hubs キューに登録されます。 Notification Hubs は適切なターゲットを判断し、その後プッシュ通知サービスに通知を送信します。 REST API やクライアント SDK を使用している場合、送信呼び出しが正常に返されても、それは Notification Hubs でメッセージが正常にキューに追加されたということでしかありません。 Notification Hubs が最終的にメッセージをプッシュ通知サービスに送信したらどうなるかについての情報はありません。

通知がクライアント デバイスに届かない場合、Notification Hubs がプッシュ通知サービスにメッセージを配信しようとしたときにエラーが発生した可能性があります。 たとえば、ペイロードのサイズがプッシュ通知サービスで許容される最大サイズを超えた、あるいは Notification Hubs に構成されている資格情報が有効でない、などです。

プッシュ通知サービス エラーに関する情報を取得するには、[EnableTestSend] プロパティを使用できます。 このプロパティは、ポータルまたは Visual Studio クライアントからテスト メッセージを送信するときに自動的に有効にされます。 このプロパティを使用して、詳細なデバッグ情報を参照できます。 このプロパティは、API を介して使用することもできます。 現時点では、.NET SDK で使用できます。 最終的には、すべてのクライアント SDK に追加される予定です。

`EnableTestSend` プロパティを REST 呼び出しで使用するには、送信呼び出しの最後に *test* というクエリ文字列パラメーターを追加します。 例: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**例 (.NET SDK):**

.NET SDK を使用してネイティブ ポップアップ (トースト) 通知を送信する例を次に示します。

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

実行の最後で、`result.State` が `Enqueued` と出力するだけです。 結果からは、プッシュ通知への処理に関する情報は得られません。

次に、`EnableTestSend` ブール値プロパティを使用できます。 `NotificationHubClient` の初期化中に `EnableTestSend` プロパティを使用すると、通知の送信時に発生したプッシュ通知サービス エラーに関する詳細な状態を取得できます。 送信呼び出しが返されるまで時間がかかります。それは、送信呼び出しが返されるのは、Notification Hubs がプッシュ通知サービスに通知を配信し、結果を判定した後だからです。

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**サンプル出力:**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

このメッセージは、Notification Hubs に無効な資格情報が構成されていること、または、ハブ内の登録に問題があること、のいずれかを示しています。 メッセージを送信する前に、この登録を削除し、クライアントで登録を再作成することをお勧めします。

> [!NOTE]
> `EnableTestSend` プロパティの使用は厳格に調整されます。 このオプションは、開発環境またはテスト環境でのみ、限られた登録で使用してください。 デバッグ通知は 10 のデバイスのみに送信されます。 また、1 分あたりに処理できるデバッグ送信は 10 件に制限されています。

### <a name="review-telemetry"></a>統計情報の確認

**Azure portal の使用:**

このポータルでは、Notification Hubs のすべてのアクティビティの概要を簡単に確認できます。

1. **[概要]** タブでは、登録、通知、およびエラーが統合されたビューを、プラットフォームごとに確認できます。

    ![Notification Hubs の概要ダッシュボード][5]

2. **[モニター]** タブでは、理解を深めるために、プラットフォーム固有のその他多数のメトリックを追加できます。 特に、Notification Hubs サービスがプッシュ通知サービスに通知を送信しようとしたときに返される、プッシュ通知サービスに関連したすべてのエラーを参照できます。

    ![Azure Portal のアクティビティ ログ][6]

3. **受信メッセージ**、**登録操作**、**正常通知**を確認することから始めます。 次に、プラットフォームごとのタブに移動して、プッシュ通知サービスに固有のエラーを確認します。

4. 通知ハブの認証設定が正しくない場合、**PNS 認証エラー**のメッセージが表示されます。 この場合は、プッシュ通知サービスの資格情報を確認してください。

* **プログラムによるアクセス**

プログラムによるアクセスの詳細については、以下の記事をご覧ください。

* [プログラムによるテレメトリへのアクセス]  
* [API サンプルを使用したテレメトリへのアクセス]

> [!NOTE]
> 登録のエクスポートおよびインポートや、API を使用したテレメトリへのアクセスなど、テレメトリに関連する一部の機能は、Standard サービス レベルでのみ使用できます。 Free または Basic サービス レベルでこれらの機能を使用しようとすると、SDK を使用している場合は例外メッセージが、REST API から直接これらの機能を使用している場合は HTTP 403 (アクセス不可) エラーが表示されます。
>
> テレメトリに関連する機能を使用するには、まず Azure Portal で、Standard サービス レベルを使用していることを確認してください。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs の使用]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs の概要]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM メッセージについて]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer のコード]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[通知ハブのデバイス登録の表示]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[詳細:Visual Studio 2013 Update 2 RC および Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio 2013 Update 3 および Azure SDK 2.4 のリリースの発表]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[プログラムによるテレメトリへのアクセス]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[API サンプルを使用したテレメトリへのアクセス]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
