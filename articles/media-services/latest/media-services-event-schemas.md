---
title: Media Services 用の Azure Event Grid スキーマ
description: Azure Event Grid で Media Services イベント用に用意されているプロパティについて説明します
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782661"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services 用の Azure Event Grid スキーマ

この記事では、Media Services イベント用のスキーマとプロパティについて説明します。

## <a name="media-services-job-state-change-event"></a>Media Services の Job 状態変更イベント

このセクションでは、Media Services の Job 状態変更イベント用のプロパティとスキーマを示します。  

### <a name="available-event-types"></a>使用可能なイベントの種類

Media Services の **Job** から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Job の状態が変更されたときに発生します。 |

### <a name="example-event"></a>イベントの例

次の例は、Job 完了状態イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| topic | 文字列 | この値は Event Grid によって指定されます。 |
| subject | 文字列 | Media Services アカウント リソースの下のリソース パス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 例: "Microsoft.Media.JobStateChange" |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | 文字列 | イベントの一意識別子。 |
| data | オブジェクト | Media Services イベント データ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| previousState | 文字列 | イベントの前のジョブの状態。 |
| state | 文字列 | このイベントで通知されるジョブの新しい状態。 例: "Queued: Job はリソースを待っています" や "Scheduled: Job は開始する準備ができています"。|

Job の状態は、次の値のいずれかが可能です。*Queued**Scheduled**Processing**Finished**Error**Canceled**Canceling*。

## <a name="next-steps"></a>次の手順

[ジョブ状態変更イベントを登録する](job-state-events-cli-how-to.md)
