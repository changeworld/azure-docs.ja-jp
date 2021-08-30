---
title: Event Grid ソースとしての Azure Kubernetes Service (プレビュー)
description: この記事では、Event Grid イベント ソースとして Azure Kubernetes Service を使用する方法について説明します。 スキーマと、チュートリアルおよび操作方法に関する記事へのリンクを提供します。
author: zr-msft
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: 16b82ea59e69457475966fecfb8a0ca8d7dd20ce
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734114"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>Event Grid ソースとしての Azure Kubernetes Service (AKS) (プレビュー)

この記事では、AKS イベントのプロパティとスキーマについて説明します。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 また、AKS をイベント ソースとして使用するためのクイック スタートとチュートリアルの一覧も示されています。

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>使用可能なイベントの種類

AKS から出力されるイベントの種類は次のとおりです

|    イベントの種類                                             |    説明                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | 使用可能な Kubernetes バージョンの一覧が更新されたときにトリガーされます。 |

## <a name="properties-common-to-all-events"></a>すべてのイベントに共通のプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。
このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。 各イベントのトップレベルのデータを次に示します。

|     プロパティ          |     Type     |     説明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。                                      |
|    `subject`            |    string    |    発行元が定義したイベントの対象のパス。                                                                                              |
|    `eventType`          |    string    |    このイベント ソース用に登録されたイベントの種類のいずれか。                                                                                  |
|    `eventTime`          |    string    |    プロバイダーの UTC 時刻に基づくイベントの生成時刻。                                                                         |
|    `id`                 |    string    |    イベントの一意識別子。                                                                                                            |
|    `data`               |    object    |    Blob Storage イベントのデータ。                                                                                                                    |
|    `dataVersion`        |    string    |    データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。                                                          |
|    `metadataVersion`    |    string    |    イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。    |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。
このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。 各イベントのトップレベルのデータを次に示します。

|     プロパティ          |     Type     |     説明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。                                      |
|    `subject`            |    string    |    発行元が定義したイベントの対象のパス。                                                                                              |
|    `type`          |    string    |    このイベント ソース用に登録されたイベントの種類のいずれか。                                                                                  |
|    `time`          |    string    |    プロバイダーの UTC 時刻に基づくイベントの生成時刻。                                                                         |
|    `id`                 |    string    |    イベントの一意識別子。                                                                                                            |
|    `data`               |    object    |    Blob Storage イベントのデータ。                                                                                                                    |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

## <a name="example-events"></a>イベントの例

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```
# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```

---

データ オブジェクトには次のプロパティが含まれます。

|    プロパティ                        | Type   | 説明                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | string | 使用可能な Kubernetes のサポートされている最新バージョン。        |
| `latestStableKubernetesVersion`    | string | 使用可能な Kubernetes のサポートされている最新の安定バージョン。 |
| `lowestMinorKubernetesVersion`     | string | 使用可能な Kubernetes のサポートされている最小バージョン。        |
| `latestPreviewKubernetesVersion`   | string | 使用可能な Kubernetes の最新のプレビュー バージョン。          |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、「[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)」を参照してください。