---
title: Azure Communication Services での通知
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services に基づいて構築されたアプリのユーザーに通知を送信します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3e68e65a5c2ed73a8fb6d8e5d01c645e05ca5157
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320718"
---
# <a name="communication-services-notifications"></a>Communication Services の通知

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services のチャットと通話のクライアント ライブラリにより、リアルタイムのメッセージング チャネルが作成されます。これは、効率的で信頼性の高い方法で、接続されたクライアントにシグナリング メッセージをプッシュすることを可能にします。 これにより、複雑な HTTP ポーリング ロジックを実装することなく、豊富なリアルタイム通信機能をアプリケーションに組み込むことができます。 ただしモバイル アプリケーションでは、このシグナリング チャネルは、アプリケーションがフォアグラウンドでアクティブになっているときにのみ接続されたままになります。 アプリケーションがバックグラウンドで実行されているときに、ユーザーが電話の着信またはチャット メッセージを受け取るようにするには、プッシュ通知を使用する必要があります。

プッシュ通知を使用すると、アプリケーションからユーザーのモバイル デバイスに情報を送信できます。 プッシュ通知を使用すると、ダイアログを表示したり、音を鳴らしたり、電話の着信の UI を表示したりすることができます。 Azure Communication Services は [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) および [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) と統合されます。これにより、アプリにプッシュ通知を追加することができます。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Azure Event Grid 経由でプッシュ通知をトリガーする

Azure Communication Services は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、信頼性が高く、スケーラブルで安全な方法でリアルタイムのイベント通知を配信します。 この統合を活用して通知サービスを作成できます。これにより、[Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-overview) または Webhook をトリガーするイベント グリッド サブスクリプションを作成して、モバイル プッシュ通知がユーザーに配信されます。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Communication Services が Event Grid とどのように統合されるかを示す図。":::

[Azure Communication Services でのイベント処理](./event-handling.md)の詳細を確認してください。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Azure Notification Hubs 経由でプッシュ通知を配信する

電話の着信を受け取ったときにユーザーのモバイル デバイスに自動的にプッシュ通知を送信するために、Azure Notification Hub をCommunication Services リソースに接続することができます。 これらのプッシュ通知を使用して、アプリケーションをバックグラウンドからウェイク アップし、ユーザーが通話を受け入れたり拒否したりできるようにする UI を表示してください。 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Communication Services が Event Grid とどのように統合されるかを示す図。":::

Communication Services はパススルー サービスとして Azure Notification Hub を使用することで、[直接送信](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) API を使用してプラットフォーム固有のさまざまなプッシュ通知サービスと通信します。 これにより、既存の Azure Notification Hub のリソースと構成を再利用して、待ち時間が短く、信頼性の高い通話通知をアプリケーションに配信できます。

> [!NOTE]
> 現在、プッシュ通知の呼び出しのみがサポートされています。

### <a name="notification-hub-provisioning"></a>通知ハブのプロビジョニング 

Notification Hubs を使用してクライアント デバイスにプッシュ通知を配信するには、Communication Services リソースと同じサブスクリプション内に[通知ハブを作成](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal)します。 Microsoft Azure Notification Hubs は、使用するプラットフォーム通知サービス用に構成する必要があります。 Notification Hubs からのプッシュ通知をクライアント アプリで取得するには、[Notification Hubs の使用](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started)に関する記事を参照し、ページの上部にあるドロップダウン リストでターゲット クライアント プラットフォームを選択します。

> [!NOTE]
> 現在、APN および FCM プラットフォームがサポートされています。

通知ハブを構成したら、Azure Resource Manager クライアントまたは Azure portal を使用してハブの接続文字列を指定することにより、Communication Services リソースにそれを関連付けることができます。 接続文字列には "送信" アクセス許可が含まれている必要があります。 ハブ専用の "送信" のみのアクセス許可を持つ別のアクセス ポリシーを作成することをお勧めします。 [Notification Hubs のセキュリティとアクセスのポリシー](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)に関する詳細を確認してください

> [!IMPORTANT]
> Apple Push Notification Service VOIP の通知を有効にするには、通知ハブの名前を、`.voip` サフィックスを持つアプリケーション バンドル ID に設定する必要があります。 「[Notification Hubs 経由で APNS VOIP を使用する](https://docs.microsoft.com/azure/notification-hubs/voip-apns)」を参照してください。

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Azure Resource Manager クライアントを使用して通知ハブを構成する

Azure Resource Manager にログインするには、以下を実行し、ご自身の資格情報を使用してサインインします。

```console
armclient login
```

 正常にログインしたら、以下を実行して通知ハブをプロビジョニングします。

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Azure portal を使用して通知ハブを構成する

ポータルで、Azure Communication Services リソースに移動します。 Communication Services リソース内で Communication Services ページの左側のメニューから [プッシュ通知] を選択し、前にプロビジョニングした通知ハブを接続します。 ここでは、接続文字列とリソース ID を指定する必要があります。

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Communication Services が Event Grid とどのように統合されるかを示す図。":::

> [!NOTE]
> Azure Notification ハブの接続文字列が更新されている場合は、Communication Services リソースも更新する必要があります。

#### <a name="device-registration"></a>デバイス登録 

Communication Services にデバイス ハンドルを登録する方法については、[音声通話のクイック スタート](../quickstarts/voice-video-calling/getting-started-with-calling.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](https://docs.microsoft.com/azure/event-grid/overview)に関する記事を参照してください。
* Azure Notification Hub の概念の詳細については、[Azure Notification Hubs のドキュメント](https://docs.microsoft.com/azure/notification-hubs/)を参照してください
