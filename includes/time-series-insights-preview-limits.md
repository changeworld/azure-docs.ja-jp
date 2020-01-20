---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/31/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 90e6e5c8d6d4f10e2d63f80b9c0840854424f5a7
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75616198"
---
### <a name="general-availability-and-preview-comparison"></a>一般提供とプレビューの比較

次の表に、Azure Time Series Insights の一般提供 (GA) とプレビュー インスタンスの主な違いの一部をまとめます。

| | GA | プレビュー |
| --- | --- | ---|
| 第一級オブジェクト | イベント中心 | 時系列中心 |
| セマンティック推論 | 低レベル (参照データ) | 高レベル (モデル) |
| データのコンテキスト化 | 非デバイス レベル | デバイスおよび非デバイス レベル |
| コンピューティング ロジック ストレージ | いいえ | モデルの型変数部分に格納 |
| ストレージとアクセス制御 | いいえ | モデルを使用することで有効 |
| 集計/サンプリング | いいえ | イベントの重み付けと時間の重み付け |
| シグナルの再構築 | いいえ | 補間 |
| 派生時系列の作成 | いいえ | はい、マージと結合 |
| 言語の柔軟性 | 非コンポーザブル | コンポーザブル |
| 式言語 | 述語文字列 | 時系列式 (述語文字列、値、式、および関数) |

### <a name="property-limits"></a>プロパティの制限

Time Series Insights のプロパティ制限は、GA の上限である 800 個から 1,000 個に引き上げられました。 提供されているイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Time Series Insights プレビュー エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)内で確認できます。

| SKU | 最大のプロパティ |
| --- | --- |
| プレビュー PAYG | 1,000 個のプロパティ (列) |
| GA S1 | 600 個のプロパティ (列) |
| GA S2 | 800 個のプロパティ (列) |

### <a name="event-sources"></a>イベント ソース

インスタンスごとに最大 2 つのイベント ソースがサポートされています。 

* [イベント ハブ ソースを追加する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)方法を学習します。
* [IoT ハブ ソース](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)を構成します。

既定では、[プレビュー環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress)で、**環境あたり最大で毎秒 1 メガバイト (MB/秒)** のイングレス レートをサポートできます。 お客様は、必要に応じて、最大 **16 MB/秒**のスループットまで、プレビュー環境を拡張できます。 また、パーティションごとに **0.5 MB/秒**の制限があります。 

### <a name="api-limits"></a>API の制限

Time Series Insights プレビューの REST API の制限については、[REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)を参照してください。
