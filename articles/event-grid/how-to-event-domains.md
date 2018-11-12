---
title: Azure Event Grid で大規模なトピック セットを管理し、イベント ドメインを使用してそれらにイベントを発行する方法
description: Azure Event Grid でトピックを作成および管理し、イベント ドメインを使用してそれらにイベントを発行する方法について説明します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d6da1ee603c85556693b145ba17d1e0cd0dfabd7
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034542"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>トピックを管理し、イベント ドメインを使用してイベントを発行する

この記事では、次の方法について説明します。

* Event Grid ドメインを作成する
* トピックをサブスクライブする
* キーのリスト
* ドメインにイベントを発行する

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>イベント ドメインを作成する

イベント ドメインは、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) の `eventgrid` 拡張機能を使用して作成することができます。 ドメインを作成したら、それを使用して大規模なトピック セットを管理することができます。

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

正常に作成された場合、次のように返されます。

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

ドメインを管理してイベントを発行するために必要であるため、`endpoint` と `id` はメモしておいてください。

## <a name="create-topics-and-subscriptions"></a>トピックとサブスクリプションを作成する

Event Grid サービスでは、ドメイン トピックのイベント サブスクリプションを作成するための呼び出しに基づいて、ドメインで対応するトピックが自動的に作成および管理されます。 ドメインでトピックを作成する別の手順はありません。 同様に、トピックの最後のイベント サブスクリプションが削除されたときに、トピックも削除されます。

ドメインのトピックをサブスクライブすることは、他のすべての Azure リソースをサブスクライブするのと同じです。

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

指定されたリソース ID は、先ほどドメインを作成するときに返されたものと同じ ID です。 サブスクライブするトピックを指定するには、リソース ID の末尾に `/topics/<my-topic>` を追加します。

ドメイン内のすべてのイベントを受信するドメイン スコープ イベント サブスクリプションを作成するには、`/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>` など、トピックを指定せずに `resource-id` としてドメインを指定します。

イベントをサブスクライブするテスト エンドポイントが必要な場合は、受信イベントを表示する[ビルド済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をいつでもデプロイすることができます。 `https://<your-site-name>.azurewebsites.net/api/updates` のテスト Web サイトにイベントを送信できます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

トピックに設定されているアクセス許可は Azure Active Directory で格納され、明示的に削除する必要があります。 ユーザーにトピックへの書き込みアクセス権限がある場合、イベント サブスクリプションを削除しても、イベント サブスクリプションを作成するためのアクセスは取り消されません。

## <a name="manage-access-to-topics"></a>トピックへのアクセスを管理する

トピックへのアクセスの管理は、[ロールの割り当て](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)を使用して行われます。 ロールの割り当てではロールベースのアクセス確認を使用して、Azure リソースに対する操作を、特定のスコープの承認されたユーザーに制限します。

Event Grid には、ドメイン内のさまざまなトピックへのアクセスを特定のユーザーに割り当てるために使用できる 2 つの組み込みロールがあります。 これらのロールは、サブスクリプションの作成と削除を許可する `EventGrid EventSubscription Contributor (Preview)` と、イベント サブスクリプションのリストのみを許可する `EventGrid EventSubscription Reader (Preview)` です。

次のコマンドでは、`alice@contoso.com` を、トピック `foo` のイベント サブスクリプションの作成と削除のみに制限します。

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

詳細については、「[Event Grid security and authentication](./security-authentication.md)」 (Event Grid のセキュリティと認証) を参照してください。

* 管理アクセス制御
* 操作の種類
* カスタム ロール定義の作成

## <a name="publish-events-to-an-event-grid-domain"></a>Event Grid ドメインにイベントを発行する

ドメインにイベントを発行することは、[カスタム トピックに発行する](./post-to-custom-topic.md)ことと同じです。 唯一の違いは、各イベントの送信先となるトピックを指定する必要があることです。 次のイベントの配列では、`"id": "1111"` のイベントが `foo` に送信され、`"id": "2222"` のイベントはトピック `bar` に送信されます。

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

ドメインのキーを取得するには、以下を使用します。

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

次に、HTTP POST を実行するための任意のメソッドを使用して、Event Grid ドメインにイベントを発行します。

## <a name="next-steps"></a>次の手順

* イベント ドメインの概念の概要と役立つ理由の詳細については [イベント ドメインの概念の概要](./event-domains.md)に関するページを参照してください。