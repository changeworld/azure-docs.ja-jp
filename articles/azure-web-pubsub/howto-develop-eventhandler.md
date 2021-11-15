---
title: イベント ハンドラーの構成方法
description: Azure Web PubSub サービスを使用して開発する場合のイベント ハンドラーの概念と統合の概要に関するガイダンス。
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/31/2021
ms.openlocfilehash: 45ad4d9d6d441584a512f5666245aed4020778cb
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576398"
---
# <a name="event-handler-in-azure-web-pubsub-service"></a>Azure Web PubSub サービスのイベント ハンドラー

イベント ハンドラーでは、受信クライアント イベントが処理されます。 イベント ハンドラーは、事前にポータルまたは Azure CLI を通じて登録し構成して、クライアント イベントがトリガーされたときに、そのイベントが処理されることが期待されているかどうかをサービスが識別できるようにします。 イベント ハンドラーがサーバー側としてサポートされるようになりました。これにより、イベントがトリガーされたときに呼び出すサービスのパブリック アクセス可能なエンドポイントが公開されます。 つまり **Webhook** として機能します。 

サービスは、[CloudEvents HTTP プロトコル](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)を使用して、アップストリームの Webhook にクライアント イベントを配信します。

すべてのイベントに対して、登録されたアップストリームへの HTTP POST 要求を形成し、HTTP 応答を期待します。 

サービスからサーバーに送信されるデータは、常に CloudEvents `binary` 形式です。

:::image type="content" source="media/howto-develop-eventhandler/event-trigger.png" alt-text="Web PubSub サービス イベント トリガーのスクリーンショット":::。

## <a name="upstream-and-validation"></a>アップストリームと検証

Webhook エンドポイントを構成するときに、URL に `{event}` パラメーターを使用して URL テンプレートを定義できます。 サービスでは、クライアント要求を受信すると、Webhook URL の値が動的に計算されます。 たとえば、ハブ `/client/hubs/chat` にイベント ハンドラー URL パターン `http://host.com/api/{event}` が構成されている状態で、要求 `chat` を受信すると、クライアントは接続するときに、最初に URL `http://host.com/api/connect` に POST します。 これは、PubSub WebSocket クライアントがカスタム イベントを送信するときに役立つ可能性があり、イベント ハンドラーでは、さまざまなイベントをさまざまなアップストリームにディスパッチできます。 URL ドメイン名では `{event}` パラメーターを使用できないことに注意してください。

Azure portal または CLI を使用して、アップストリームにイベントハンドラーを設定すると、サービスによって [CloudEvents の不正使用防止](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)に従ってアップストリームの Webhook が検証されます。 このメカニズムによって、登録されているすべてのアップストリーム Webhook の URL が検証されます。 `WebHook-Request-Origin` 要求ヘッダーはサービス ドメイン名 `xxx.webpubsub.azure.com` に設定されており、これは応答にこのドメイン名または `*` を含むヘッダー `WebHook-Allowed-Origin` があることが期待されます。

検証を実行すると、`{event}` パラメーターは `validate` に解決されます。 たとえば、URL を `http://host.com/api/{event}` に設定しようとすると、サービスによって、`http://host.com/api/validate` に対する要求の **OPTIONS** が試みられ、応答が有効な場合にのみ、構成を正常に設定できます。

現時点では、[WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) と [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) はサポートされていません。

## <a name="authentication-between-service-and-webhook"></a>サービスと Webhook 間の認証

- 匿名モード
- 構成済みの Webhook URL を介してクエリ パラメーターとして `?code=<code>` が提供される単純認証。
- AAD 認証。 
   - ポータル/CLI を介して、AAD の [アプリの登録] にクライアント シークレットを追加し、[クライアント シークレット] を Azure Web PubSub に提供します。
   - ポータル/CLI を介して Azure Web PubSub に [ID](../app-service/overview-managed-identity.md?tabs=dotnet) を提供します

## <a name="configure-event-handler"></a>イベント ハンドラーの構成

### <a name="configure-through-azure-portal"></a>Azure portal を使用して構成する

**Azure portal** から Azure Web PubSub サービス検索します。 **[設定]** に移動し、ハブ名を入力します。 次に、 **[追加]** をクリックして、サーバー側の Webhook URL を構成します。 終了したら忘れずに **[保存]** をクリックください。

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="イベント ハンドラーの設定のスクリーンショット。":::

### <a name="configure-through-azure-cli"></a>Azure CLI を使用して構成する

Azure CLI の [**az webpubsub hub**](/cli/azure/webpubsub/hub) グループ コマンドを使用して、イベント ハンドラーの設定を構成します。

コマンド | 説明
--|--
create | WebPubSub サービスのハブ設定を作成します。
delete | WebPubSub サービスのハブ設定を削除します。
list   | WebPubSub サービスのすべてのハブ設定を一覧表示します。
show   | WebPubSub サービスのハブ設定を表示します。
update | WebPubSub サービスのハブ設定を更新します。

以下に、`MyWebPubSub` リソースのハブ `MyHub` 用に 2 つの Webhook URL を作成する例を示します。

```azurecli-interactive
az webpubsub hub create -n "MyWebPubSub" -g "MyResourceGroup" --hub-name "MyHub" --event-handler url-template="http://host.com" user-event-pattern="*" --event-handler url-template="http://host2.com" system-event="connected" system-event="disconnected" auth-type="ManagedIdentity" auth-resource="uri://myUri"
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]