---
title: "Azure Event Grid イベント スキーマ"
description: "Azure Event Grid のイベントに対して用意されているプロパティについて説明します"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid イベント スキーマ

この記事では、すべてのイベントに存在するプロパティとスキーマについて説明します。 イベントは、5 つの必須文字列プロパティと 1 つの必須データ オブジェクトで構成されます。 プロパティは、すべてのイベントに共通であり、発行元を問いません。 データ オブジェクトには、各発行元に固有のプロパティが含まれます。 システム トピックの場合、これらのプロパティは、リソース プロバイダー (Azure Storage や Azure Event Hubs など) に固有です。

イベントは、Azure Event Grid に配列で送信され、配列には複数のイベント オブジェクトを含めることができます。 1 つのイベントのみがある場合、配列の長さは 1 になります。 配列のサイズは合計で最大 1 MB です。 配列内の各イベントは 64 KB に制限されます。

## <a name="event-schema"></a>イベント スキーマ

すべてのイベント発行元に使用されているプロパティの例を次に示します。

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

たとえば、Azure BLOB ストレージ イベントに対して次のようなスキーマが発行されます。

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```
 
## <a name="event-properties"></a>イベントのプロパティ

すべてのイベントには、次に示す最上位レベルのデータが格納されます。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| トピック | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | オブジェクト | リソース プロバイダーに固有のイベント データ。 |

データ オブジェクトのプロパティの詳細については、イベント ソースを参照してください。

* [Azure サブスクリプション (管理操作)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [リソース グループ (管理操作)](event-schema-resource-groups.md)

カスタム トピックの場合、イベントの発行元がデータ オブジェクトを決定します。 最上位レベルのデータには、リソースによって定義される標準的なイベントと同じフィールドを含める必要があります。 カスタム トピックに対してイベントを発行する場合は、ルーティングとフィルタ処理を支援するために、イベントの対象をモデル化することを検討してください。

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
