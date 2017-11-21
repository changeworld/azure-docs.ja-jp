---
title: "Azure Event Grid のリソース グループ用のイベント スキーマ"
description: "Azure Event Grid のリソース グループ イベントに対して用意されているプロパティについて説明します"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: c7435d87f9aaa906c3f6758186b64f3458cb9716
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid のリソース グループ用のイベント スキーマ

この記事では、リソース グループ イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

Azure サブスクリプションとリソース グループは、同じ種類のイベントを出力します。 イベントの種類は、リソースの変更に関連しています。 主な違いは、リソース グループはリソース グループ内のリソースのイベントを出力し、Azure サブスクリプションはサブスクリプションのリソースのイベントを出力することです。 

## <a name="available-event-types"></a>使用可能なイベントの種類

リソース グループは、VM が作成されたりストレージ アカウントが削除されたりしたときに、Azure Resource Manager から管理イベントを出力します。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | リソースの作成または更新操作が成功したときに発生します。 |
| Microsoft.Resources.ResourceWriteFailure | リソースの作成または更新操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceWriteCancel | リソースの作成または更新操作が取り消されたときに発生します。 |
| Microsoft.Resources.ResourceDeleteSuccess | リソースの削除操作が成功したときに発生します。 |
| Microsoft.Resources.ResourceDeleteFailure | リソースの削除操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceDeleteCancel | リソースの削除操作が取り消されたときに発生します。 このイベントは、テンプレートのデプロイが取り消された場合に発生します。 |

## <a name="example-event"></a>イベントの例

次の例は、リソース作成スキーマを示しています。 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

リソース削除イベント スキーマも似ています。

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントには、次の最上位レベルのデータがあります。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | リソース グループ イベントのデータ。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| authorization | string | 操作の要求された承認。 |
| claims | string | 要求のプロパティ。 |
| correlationId | string | トラブルシューティング用の操作 ID。 |
| httpRequest | string | 操作の詳細。 |
| resourceProvider | string | 操作を実行しているリソース プロバイダー。 |
| resourceUri | string | 操作内のリソースの URI。 |
| operationName | string | 実行された操作。 |
| status | string | 操作の状態。 |
| subscriptionId | string | リソースのサブスクリプション ID。 |
| tenantId | string | リソースのテナント ID。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
