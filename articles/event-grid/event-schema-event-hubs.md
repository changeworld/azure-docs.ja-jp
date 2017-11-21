---
title: "Azure Event Grid の Event Hubs 用のイベント スキーマ"
description: "Azure Event Grid の Event Hubs イベントに対して用意されているプロパティについて説明します"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: tomfitz
ms.openlocfilehash: 80959ee589a1cfcf317a98c3bafd7f92c796fc2d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid の Event Hubs 用のイベント スキーマ

この記事では、Event Hubs イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

### <a name="available-event-types"></a>使用可能なイベントの種類

Event Hubs は、キャプチャ ファイルが作成されたときに、種類が **Microsoft.EventHub.CaptureFileCreated** であるイベントを出力します。

## <a name="example-event"></a>イベントの例

このサンプル イベントは、ファイルがキャプチャ機能によって保存されるときに発生する Event Hubs イベントのスキーマを示しています。 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
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
| data | object | Event Hub イベントのデータ。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| fileUrl | string | キャプチャ ファイルのパス。 |
| fileType | string | キャプチャ ファイルのファイルの種類。 |
| partitionId | string | シャード ID。 |
| sizeInBytes | integer | ファイル サイズ。 |
| eventCount | integer | ファイル内のイベントの数。 |
| firstSequenceNumber | integer | キューの最小のシーケンス番号。 |
| lastSequenceNumber | integer | キューの最後のシーケンス番号。 |
| firstEnqueueTime | string | キューの最初の時間。 |
| lastEnqueueTime | string | キューの最後の時間。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* Event Hubs イベントの処理については、「[ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)」を参照してください。