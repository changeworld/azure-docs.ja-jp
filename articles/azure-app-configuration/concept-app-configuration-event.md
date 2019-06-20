---
title: Azure App Configuration のキー/値イベントへの反応 | Microsoft Docs
description: App Configuration イベントをサブスクライブするには、Azure Event Grid を使用します。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735880"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure App Configuration イベントへの反応

Azure App Configuration イベントを使用すると、アプリケーションでキー/値の変化に反応できます。 これを実行するために、複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通して、[Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)、またはユーザー独自のカスタム HTTP リスナーにプッシュされ、料金は使ったものだけで済みます。

Azure App Configuration イベントは Azure Event Grid に送信されます。Event Grid Service では豊富な再試行ポリシーおよび配信不能メッセージ配信を使用してご利用のアプリケーションに信頼性の高い配信サービスが提供されます。 詳細については、「[Event Grid のメッセージの配信と再試行](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)」を参照してください。

一般的なアプリ構成イベントのシナリオには、アプリケーション構成の更新、デプロイのトリガー、または構成指向のワークフローが含まれます。 変更の頻度が低くても、即時の応答性が必要なシナリオでは、イベント ベースのアーキテクチャは特に効果的です。

簡単な例については、[Azure App Configuration イベントをカスタム Web エンドポイントにルーティングする - CLI](./howto-app-configuration-event.md) に関する記事を参照してください。 

![Event Grid モデル](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>使用できる Azure App Configuration イベント
Event Grid は、[イベント サブスクリプション](../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Azure App Configuration イベントのサブスクリプションには、2 種類のイベントを含むことができます。  

> |イベント名|説明|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|キー/値が作成または置換されたときに発生します|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|キー/値が削除されたときに発生します|

## <a name="event-schema"></a>イベント スキーマ
Azure App Configuration イベントには、データの変更に対応するために必要なすべての情報が含まれます。 アプリ構成イベントは eventType プロパティが "Microsoft.AppConfiguration" で始まっていることで識別できます。 Event Grid イベントのプロパティの使用法について詳しくは、「[Event Grid イベント スキーマ](../event-grid/event-schema.md)」をご覧ください。  

> |プロパティ|Type|説明|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|イベントを生成したアプリ構成の完全な Azure Resource Manager ID です。|
> |subject|string|イベントの対象であるキー/値の URI です。|
> |eventTime|string|イベントが生成された日時です (ISO 8601 形式)。|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" または "Microsoft.AppConfiguration.KeyValueDeleted"。|
> |Id|string|このイベントの一意識別子。|
> |dataVersion|string|データ オブジェクトのスキーマ バージョン。|
> |metadataVersion|string|最上位プロパティのスキーマ バージョン。|
> |data|object|Azure App Configuration 固有のイベント データのコレクション|
> |data.key|string|変更または削除されたキー/値のキー。|
> |data.label|string|変更または削除されたキー/値のラベル (存在する場合)。|
> |data.etag|string|`KeyValueModified` の場合、新しいキー/値の etag。 `KeyValueDeleted` の場合、削除されたキー/値の etag。|

KeyValueModified イベントの例を次に示します。
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

詳細については、[Azure App Configuration イベント スキーマ](../event-grid/event-schema-app-configuration.md)に関する記事を参照してください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法
アプリ構成イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。
> [!div class="checklist"]
> * 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションが構成されている可能性があるので、イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているアプリ構成からのものであることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * メッセージは順不同で到着したり、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。  また、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。
> * subject フィールドを使用して、変更されたキー/値にアクセスします。


## <a name="next-steps"></a>次の手順

Event Grid の詳細について理解し、Azure App Configuration イベントを試してみてください。

- [Event Grid について](../event-grid/overview.md)
- [Azure App Configuration イベントをカスタム Web エンドポイントにルーティングする](./howto-app-configuration-event.md)