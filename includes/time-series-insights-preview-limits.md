---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287417"
---
### <a name="property-limits"></a>プロパティの制限

Azure Time Series Insights のプロパティ制限は、Gen1 の上限である 800 個から 1,000 個に引き上げられました。 提供されているイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Azure Time Series Insights Gen2 エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)内で確認できます。

| SKU | 最大のプロパティ |
| --- | --- |
| Gen2 (L1) | 1,000 個のプロパティ (列) |
| Gen1 (S1) | 600 個のプロパティ (列) |
| Gen1 (S2) | 800 個のプロパティ (列) |

### <a name="streaming-ingestion"></a>ストリーミング インジェスト

* 環境ごとの[イベント ソース](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md)の数は、最大で 2 つです。

* イベント ソースのベストプラクティスと一般的なガイダンスについては、[こちら](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)を参照してください

* 既定では、Azure Time Series Insights Gen2 では **Azure Time Series Insights Gen2 環境ごとに最大 1 MB/秒 (MBps)** の速度で受信データを取り込むことができます。 [ハブのパーティションごとに](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)追加の制限があります。 Azure portal を介してサポート チケットを提出することで、最大 8 MBps の速度が提供されます。 詳細については、「[ストリーミング インジェストのスループットの制限](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)」を参照してください。

### <a name="api-limits"></a>API の制限

Azure Time Series Insights Gen2 の REST API の制限については、[REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)を参照してください。
