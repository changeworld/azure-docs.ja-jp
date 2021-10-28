---
title: Event Grid ソースとしての API Management
description: この記事では、Event Grid イベント ソースとして Azure API Management を使用する方法について説明します。 スキーマと、操作方法に関する記事へのリンクを提供します。
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 07/12/2021
ms.openlocfilehash: 87684e6a4bc2c349a2b65faefe825c455be413d5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215523"
---
# <a name="azure-api-management-as-an-event-grid-source-preview"></a>Event Grid ソースとしての Azure API Management (プレビュー)

この記事では、[Azure API Management](../api-management/index.yml) イベントのプロパティとスキーマについて説明します。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](./event-schema.md)」を参照してください。 また、イベント ソースとして API Management を使用するための記事へのリンクも示します。

## <a name="available-event-types"></a>使用可能なイベントの種類

API Management から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.ApiManagement.UserCreated | ユーザーが作成されたときに発生します。 |
| Microsoft.ApiManagement.UserUpdated | ユーザーが更新されたときに発生します。 |
| Microsoft.ApiManagement.UserDeleted | ユーザーが削除されたときに発生します。 |
| Microsoft.ApiManagement.APICreated | API が作成されたときに発生します。 |
| Microsoft.ApiManagement.APIUpdated | API が更新されたときに発生します。 |
| Microsoft.ApiManagement.APIDeleted | API が削除されたときに発生します。 |
| Microsoft.ApiManagement.ProductCreated | 製品が作成されたときに発生します。 |
| Microsoft.ApiManagement.ProductUpdated | 製品が更新されたときに発生します。 |
| Microsoft.ApiManagement.ProductDeleted | 製品が削除されたときに発生します。 |
| Microsoft.ApiManagement.ReleaseCreated | API リリースが作成されたときに発生します。 |
| Microsoft.ApiManagement.ReleaseUpdated | API リリースが更新されたときに発生します。 |
| Microsoft.ApiManagement.ReleaseDeleted | API リリースが削除されたときに発生します。 |
| Microsoft.ApiManagement.SubscriptionCreated | サブスクリプションが作成されたときに発生します。 |
| Microsoft.ApiManagement.SubscriptionUpdated | サブスクリプションが更新されたときに発生します。 |
| Microsoft.ApiManagement.SubscriptionDeleted | サブスクリプションが削除されたときに発生します。 |

## <a name="example-event"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
次の例は、製品作成イベントのスキーマを示しています。 他の API Management リソース作成イベントのスキーマも同様です。

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "eventType": "Microsoft.ApiManagement.ProductCreated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、製品作成イベントのスキーマを示しています。 他の API Management リソース作成イベントのスキーマも同様です。 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "Type": "Microsoft.ApiManagement.ProductCreated",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
次の例は、ユーザー削除イベントのスキーマを示しています。 他の API Management リソース削除イベントのスキーマも同様です。

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "eventType": "Microsoft.ApiManagement.UserDeleted",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、ユーザー削除イベントのスキーマを示しています。 他の API Management リソース削除イベントのスキーマも同様です。 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "Type": "Microsoft.ApiManagement.UserDeleted",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、API 更新イベントのスキーマを示しています。 他の API Management リソース更新イベントのスキーマも同様です。 
```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "eventType": "Microsoft.ApiManagement.APIUpdated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-12T23:13:44.9048323Z"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、API 更新イベントのスキーマを示しています。 他の API Management リソース更新イベントのスキーマも同様です。 

```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "Type": "Microsoft.ApiManagement.APIUpdated",
  "Time": "2021-07-12T23:13:44.9048323Z",
  "specversion":1.0
}]
```
---

## <a name="event-properties"></a>イベントのプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | API Management イベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | API Management イベント データ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `resourceUri` | string | コンプライアンス状態の変更の対象となるリソースの完全修飾 ID。リソース名とリソースの種類が含まれます。 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/Microsoft.ApiManagement/service/<ServiceName>/<ResourceType>/<ResourceName>` の形式が使用されます。 |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法

|タイトル  |説明  |
|---------|---------|
| [API Management から Event Grid にイベントを送信する](../api-management/how-to-event-grid.md)| Event Grid を使用して API Management イベントをサブスクライブする方法。 |

## <a name="next-steps"></a>次のステップ

- Azure Event Grid の概要については、[Event Grid の紹介](./overview.md)に関する記事を参照してください。
- Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](./subscription-creation-schema.md)に関する記事を参照してください。


