---
title: 共有プライベート エンドポイントを経由する Azure Web PubSub アウトバウンド トラフィックをセキュリティで保護する
titleSuffix: Azure Web PubSub Service
description: 共有プライベート エンドポイントを経由する Azure Web PubSub アウトバウンド トラフィックをセキュリティで保護し、トラフィックが公衆ネットワークに向かうのを防ぐ方法
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 11/08/2021
ms.author: dayshen
ms.openlocfilehash: 1314165f9d3d6f47c815e811aa129f8eaf54400f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997839"
---
# <a name="secure-azure-web-pubsub-outbound-traffic-through-shared-private-endpoints"></a>共有プライベート エンドポイントを経由する Azure Web PubSub アウトバウンド トラフィックをセキュリティで保護する

Azure Web PubSub Service で[イベント ハンドラー](concept-service-internals.md#event_handler)を使用している場合は、アップストリームへのアウトバウンド トラフィックがある可能性があります。 Azure Web アプリや Azure Functions などのアップストリームは、一連の仮想ネットワークからの接続を受け入れ、公衆ネットワークからの外部接続を拒否するように構成できます。 それらのエンドポイントに到達するためのアウトバウンド [プライベート エンドポイント接続](../private-link/private-endpoint-overview.md)を作成できます。

   :::image type="content" alt-text="共有プライベート エンドポイントの概要。" source="media\howto-secure-shared-private-endpoints\shared-private-endpoint-overview.png" border="false" :::

このアウトバウンド メソッドは、次の要件を満たしていることが前提となります。

+ アップストリームが Azure Web アプリまたは Azure 関数であること。

+ Azure Web PubSub Service サービスが Standard レベルであること。

+ Azure Web アプリまたは Azure 関数が特定の SKU にあること。 [Azure Web アプリでのプライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)に関するページを参照してください。

## <a name="shared-private-link-resources-management-apis"></a>共有プライベート リンク リソース管理 API

Azure Web PubSub Service API によって作成された、セキュリティで保護されたリソースのプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 これは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)と統合されているリソース (Azure 関数など) へのアクセスを "共有" しているためです。 これらのプライベート エンドポイントは、Azure Web PubSub Service の実行環境内に作成され、直接見ることはできません。

現在、Management REST API を使用して、"*共有プライベート リンク リソース*" を作成または削除することができます。 この記事の残りの部分では、[Azure CLI](/cli/azure/) を使用して REST API 呼び出しを示します。

> [!NOTE]
> この記事の例は、次の前提に基づいています。
> * この Azure Web PubSub Service のリソース ID は _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub です。
> * アップストリーム Azure 関数のリソース ID は _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func です。

残りの例では、_contoso-webpubsub_ サービスのアップストリームの関数呼び出しが公衆ネットワークではなく、プライベート エンドポイントを経由するように構成する方法を示します。

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>手順 1: 関数への共有プライベート リンク リソースを作成する

[Azure CLI](/cli/azure/) で次の API 呼び出しを行って、共有プライベート リンク リソースを作成することができます。

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json --debug
```

API への要求本文を表す *create-pe.json* ファイルの内容は次のとおりです。

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

発信プライベート エンドポイントを作成するプロセスは長時間実行される (非同期の) 操作です。 すべての非同期 Azure 操作と同様に、`PUT` の呼び出しにより、次のような `Azure-AsyncOperation` ヘッダー値が返されます。

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

この URI を定期的にポーリングして、操作の状態を取得できます。

CLI を使用している場合は、`Azure-AsyncOperationHeader` 値を手動でクエリすることで、状態をポーリングできます。

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

状態が "Succeeded" に変わるまで待ってから、次の手順に進みます。

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>手順 2a: 関数のプライベート エンドポイント接続を承認する

> [!NOTE]
> このセクションでは、Azure portal を使用して、Azure 関数に対するプライベート エンドポイントの承認フローを順を追って説明します。 App Service プロバイダーを介して利用可能な [REST API](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection) を使用することもできます。

> [!IMPORTANT]
> プライベート エンドポイント接続の承認後は、公衆ネットワークから関数にアクセスすることができなくなります。 場合によっては、関数のエンドポイントにアクセスするための他のプライベート エンドポイントを自分の仮想ネットワークに作成する必要があります。

1. Azure portal で、関数アプリの **[ネットワーク]** タブを選択し、 **[プライベート エンドポイント接続]** に移動します。 **[Configure your private endpoint connections]\(プライベート エンドポイント接続の構成\)** をクリックします。 非同期操作が成功した後に、前の API 呼び出しからの要求メッセージを使用して、プライベート エンドポイント接続の要求を行う必要があります。

   :::image type="content" alt-text="Azure portal のスクリーンショット。[プライベート エンドポイント接続] ペインが表示されている。" source="media\howto-secure-shared-private-endpoints\portal-function-approve-private-endpoint.png" lightbox="media\howto-secure-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. Azure Web PubSub Service によって作成されたプライベート エンドポイントを選択します。 **[プライベート エンドポイント]** 列で、前の API で指定された名前でプライベート エンドポイント接続を特定し、 **[承認]** を選択します。

   プライベート エンドポイント接続が次のスクリーンショットに示すように表示されていることを確認します。 ポータルで状態が更新されるまでに 1 から 2 分かかる場合があります。

   :::image type="content" alt-text="Azure portal のスクリーンショット。[プライベート エンドポイント接続] ペインに Approved の状態が表示されている。" source="media\howto-secure-shared-private-endpoints\portal-function-approved-private-endpoint.png" lightbox="media\howto-secure-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>手順 2b:共有プライベート リンク リソースの状態を照会する

Azure Web PubSub Service に承認が反映されるまでに数分かかります。 共有プライベート リンク リソースが承認後に更新されたことを確認するために、GET API を使用して "接続状態" を取得することもできます。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

これにより、JSON が返されます。接続状態は "properties" セクションの下の "status" として表示されます。

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

リソースの "プロビジョニング状態" (`properties.provisioningState`) が `Succeeded` で、"接続状態" (`properties.status`) が `Approved` である場合は、共有プライベート リンク リソースが機能しており、Azure Web PubSub Service がプライベート エンドポイントを介して通信できることを意味します。

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>手順 3: アップストリーム呼び出しがプライベート IP からのものであることを確認する

プライベート エンドポイントが設定されたら、アップストリーム側で `X-Forwarded-For` ヘッダーを確認することにより、受信呼び出しがプライベート IP からのものであることを確認できます。

:::image type="content" alt-text="Azure portal のスクリーンショット。受信要求がプライベート IP からのものであることが示されている。" source="media\howto-secure-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>次のステップ

以下で、プライベート エンドポイントについて詳しく学習してください。

+ [プライベート エンドポイントとは](../private-link/private-endpoint-overview.md)