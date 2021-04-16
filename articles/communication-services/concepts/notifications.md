---
title: Azure Communication Services での通知
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services に基づいて構築されたアプリのユーザーに通知を送信します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e1b7e091fe09bc2c093cc84473bd07917347f26d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220796"
---
# <a name="communication-services-notifications"></a>Communication Services の通知

Azure Communication Services のチャットと通話の SDK により、リアルタイムのメッセージング チャネルが作成されます。これは、効率的で信頼性の高い方法で、接続されたクライアントにシグナリング メッセージをプッシュすることを可能にします。 これにより、複雑な HTTP ポーリング ロジックを実装することなく、豊富なリアルタイム通信機能をアプリケーションに組み込むことができます。 ただしモバイル アプリケーションでは、このシグナリング チャネルは、アプリケーションがフォアグラウンドでアクティブになっているときにのみ接続されたままになります。 アプリケーションがバックグラウンドで実行されているときに、ユーザーが電話の着信またはチャット メッセージを受け取るようにするには、プッシュ通知を使用する必要があります。

プッシュ通知を使用すると、アプリケーションからユーザーのモバイル デバイスに情報を送信できます。 プッシュ通知を使用すると、ダイアログを表示したり、音を鳴らしたり、電話の着信の UI を表示したりすることができます。 Azure Communication Services は [Azure Event Grid](../../event-grid/overview.md) および [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) と統合されます。これにより、アプリにプッシュ通知を追加することができます。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Azure Event Grid 経由でプッシュ通知をトリガーする

Azure Communication Services は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、信頼性が高く、スケーラブルで安全な方法でリアルタイムのイベント通知を配信します。 この統合を活用して通知サービスを作成できます。これにより、[Azure Function](../../azure-functions/functions-overview.md) または Webhook をトリガーするイベント グリッド サブスクリプションを作成して、モバイル プッシュ通知がユーザーに配信されます。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Communication Services が Event Grid とどのように統合されるかを示す図。":::

[Azure Communication Services でのイベント処理](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)の詳細を確認してください。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Azure Notification Hubs 経由でプッシュ通知を配信する

電話の着信を受け取ったときにユーザーのモバイル デバイスに自動的にプッシュ通知を送信するために、Azure Notification Hub をCommunication Services リソースに接続することができます。 これらのプッシュ通知を使用して、自分のアプリケーションをバックグラウンドから起動し、ユーザーが通話を受け入れたり拒否したりできるようにする UI を表示する必要があります。

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Communication Services が Azure Notification Hubs とどのように統合されるかを示す図。":::

Communication Services はパススルー サービスとして Azure Notification Hub を使用することで、[直接送信](/rest/api/notificationhubs/direct-send) API を使用してプラットフォーム固有のさまざまなプッシュ通知サービスと通信します。 これにより、既存の Azure Notification Hub のリソースと構成を再利用して、待ち時間が短く、信頼性の高い通話通知をアプリケーションに配信できます。

> [!NOTE]
> 現在、プッシュ通知の呼び出しのみがサポートされています。

### <a name="notification-hub-provisioning"></a>通知ハブのプロビジョニング

Notification Hubs を使用してクライアント デバイスにプッシュ通知を配信するには、Communication Services リソースと同じサブスクリプション内に[通知ハブを作成](../../notification-hubs/create-notification-hub-portal.md)します。 使用するプラットフォーム通知システム用に Azure 通知ハブを構成する必要があります。 Notification Hubs からのプッシュ通知をクライアント アプリで取得するには、[Notification Hubs の使用](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)に関する記事を参照し、ページの上部にあるドロップダウン リストでターゲット クライアント プラットフォームを選択します。

> [!NOTE]
> 現在、APN および FCM プラットフォームがサポートされています。
APNs プラットフォームは、トークン認証モードで構成する必要があります。 現在、証明書認証モードはサポートされていません。

通知ハブを構成したら、Azure Resource Manager クライアントまたは Azure portal を使用してハブの接続文字列を指定することにより、Communication Services リソースにそれを関連付けることができます。 接続文字列には `Send` アクセス許可が含まれている必要があります。 ハブ専用の `Send` のみのアクセス許可が設定された別のアクセス ポリシーを作成することをお勧めします。 [Notification Hubs のセキュリティとアクセスのポリシー](../../notification-hubs/notification-hubs-push-notification-security.md)に関する詳細を確認してください

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Azure Resource Manager クライアントを使用した通知ハブのリンク

Azure Resource Manager にログインするには、以下を実行し、ご自身の資格情報を使用してサインインします。

```console
armclient login
```

 正常にログインしたら、以下を実行して通知ハブをプロビジョニングします。

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Azure portal を使用した通知ハブのリンク

1. ポータルで、お使いの Azure Communication Services リソースに移動します。

1. Communication Services リソース内で Communication Services ページの左側のメニューから **[プッシュ通知]** を選択し、前にプロビジョニングした通知ハブを接続します。

1. **[Connect notification hub]\(通知ハブの接続\)** を選択します。 接続できる通知ハブの一覧が表示されます。
 
1. このリソースに使用する通知ハブを選択します。
 
   - 新しいハブを作成する必要がある場合は、 **[新しい通知ハブの作成]** を選択して、このリソース用にプロビジョニングされた新しいハブを取得します。

   :::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Azure portal 内のプッシュ通知の設定を示すスクリーンショット。":::

リンクした接続状態の通知ハブが表示されます。

リソースに別のハブを使用する場合は、 **[切断]** を選択し、手順を繰り返して別の通知ハブをリンクします。

> [!NOTE]
> ハブのリンク方法に関する変更は、最大 10 分以内にデータ プレーン (つまり、通知の送信時) に反映されます。 変更前に通知が送信されている **場合** は、ハブが初めてリンクされるときにこれと同じ動作が適用されます。

### <a name="device-registration"></a>デバイス登録

Communication Services にデバイス ハンドルを登録する方法については、[音声通話のクイック スタート](../quickstarts/voice-video-calling/getting-started-with-calling.md)に関する記事を参照してください。

### <a name="troubleshooting-guide-for-push-notifications"></a>プッシュ通知のトラブルシューティング ガイド

ご自分のデバイスにプッシュ通知が表示されない場合、通知が欠落した可能性がある場所は 3 つです。

- Azure Notification Hubs が、Azure Communication Services からの通知を受け取りませんでした
- プラットフォーム通知システム (APNs や FCM など) が、Azure Notification Hubs からの通知を受け取りませんでした
- プラットフォーム通知システムが、デバイスに通知を配信しませんでした。

通知が欠落した可能性がある最初の場所 (Azure Notification Hubs が、Azure Communication Services からの通知を受け取らなかった) については、以下で説明します。 他の 2 つの場所については、「[Azure Notification Hubs での欠落した通知の診断](../../notification-hubs/notification-hubs-push-notification-fixer.md)」を参照してください。

自分の Communication Services リソースから Azure Notification Hubs に通知が送信されているかどうかを確認する方法の 1 つは、リンクされている [Azure 通知ハブ メトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)の `incoming messages` メトリックを調べることです。

以下に、Azure 通知ハブが Communication Services リソースからの通知を受け取らない原因となる可能性がある一般的な構成ミスをいくつか示します。

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure 通知ハブが Communication Services リソースにリンクされていない

Azure 通知ハブを Communication Services リソースにリンクしなかった可能性があります。 これらをリンクする方法については、「[通知ハブのプロビジョニング」セクション](#notification-hub-provisioning)を参照してください。

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>リンクされた Azure 通知ハブが構成されていない

使用したいプラットフォーム (iOS や Android など) のプラットフォーム通知システムの資格情報を使用して、リンクされた通知ハブを構成する必要があります。 これを実行する方法の詳細については、「[通知ハブのプッシュ通知を設定する](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)」を参照してください。

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>リンクされた Azure 通知ハブがない

Communication Services リソースにリンクされている Azure 通知ハブが既にありません。 リンクされた通知ハブがまだあることを確認します。

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Azure 通知ハブの APNs プラットフォームが証明書認証モードで構成されている

証明書認証モードで APNs プラットフォームを使用する方法は、現在はサポートされていません。 「[通知ハブのプッシュ通知を設定する](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)」で指定されているように、トークン認証モードで APNs プラットフォームを構成する必要があります。

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>リンクされた接続文字列に `Send` アクセス許可がない

通知ハブを Communication Services リソースにリンクするために使用される接続文字列には、`Send` アクセス許可が含まれている必要があります。 新しい接続文字列を作成する方法、または Azure 通知ハブの現在の接続文字列を確認する方法の詳細については、[Notification Hubs のセキュリティとアクセス ポリシー](../../notification-hubs/notification-hubs-push-notification-security.md)に関するページを参照してください

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>リンクされた接続文字列または Azure 通知ハブのリソース ID が有効ではない

Communication Services リソースが、正しい接続文字列と Azure 通知ハブ リソース ID を使用して構成されていることを確認してください

#### <a name="the-linked-connection-string-is-regenerated"></a>リンクされた接続文字列が再生成される

リンクされた Azure 通知ハブの接続文字列を再生成した場合は、[通知ハブを再度リンクする](#notification-hub-provisioning)ことにより、接続文字列を Communication Services リソースの新しい接続文字列で更新する必要があります。

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](../../event-grid/overview.md)に関する記事を参照してください。
* Azure Notification Hub の概念の詳細については、[Azure Notification Hubs のドキュメント](../../notification-hubs/index.yml)を参照してください
