---
title: "Azure Time Series Insights でパフォーマンスを監視し、調整を軽減し、待機時間を防止する方法 | Microsoft Docs"
description: "この記事では、Azure Time Series Insights の待機時間と調整の原因になるパフォーマンスの問題を監視、診断、および緩和する方法について説明します。"
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 5af5589922ded802703b9ba8f3b0aa8c5524bbad
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="improve-performance-reduce-latency-and-mitigate-throttling-in-azure-time-series-insights"></a>Azure Time Series Insights のパフォーマンスの向上、待機時間の軽減、調整の緩和
着信データの量が環境の構成を超えると、Azure Time Series Insights で待機時間や調整が発生する可能性があります。

分析するデータ量に合わせて環境を正しく構成することによって待機時間と調整を回避できます。

次の操作をしたときに待機時間と調整が発生する可能性が最も高くなります。

- 割り当てられた受信レートを超えるかもしれない古いデータを含むイベント ソースを追加した (Time Series Insights はキャッチアップする必要があります)。
- 環境にイベント ソースを追加した結果、追加イベントによるスパイクが発生した (環境の容量を超えた可能性があります)。
- 大量の履歴イベントをイベント ソースにプッシュした結果、ラグが発生した (Time Series Insights はキャッチアップする必要があります)。
- 参照データをテレメトリと結合した結果、イベントのサイズが大きくなった。  調整の観点から、パケット サイズが 32 KB の受信データ パケットは、それぞれ 1 KB のサイズの 32 個のイベントとして扱われます。 イベントの最大許容サイズは 32 KB です。32 KB を超えるデータ パケットは切り捨てられます。


## <a name="monitor-latency-and-throttling-with-alerts"></a>アラートを使用した待機時間と調整の監視

アラートは、環境に起因する待機時間の問題の診断および緩和に役立ちます。 

1. Azure Portal で **[メトリック]** をクリックします。 

   ![メトリック](media/environment-mitigate-latency/add-metrics.png)

2. **[メトリック アラートの追加]** をクリックします。  

    ![メトリック アラートを追加する](media/environment-mitigate-latency/add-metric-alert.png)

そこから、次のメトリックを使用してアラートを構成できます。

|メトリック  |Description  |
|---------|---------|
|**受信バイトの受信**     | イベント ソースから読み取られた生バイト数。 通常、生バイト数にはプロパティの名前と値が含まれます。  |  
|**無効な受信メッセージの受信**     | すべての Azure Event Hubs または Azure IoT Hub イベント ソースから読み取られた無効なメッセージの数。      |
|**受信メッセージの受信**   | すべての Event Hubs または IoT Hub イベント ソースから読み取られたメッセージの数。        |
|**保存済みバイトの受信**     | クエリ用に保存済みで利用できるイベントの合計サイズ。 サイズはプロパティ値のみに基づいて計算されます。        |
|**保存済みイベントの受信**     |   クエリ用に保存済みで利用できるフラット化されたイベントの数。      |

![待機時間](media/environment-mitigate-latency/latency.png)

1 つの方法は、2 時間の環境の合計容量より若干低いしきい値以上に**保存済みイベントの受信**アラートを 設定することです。  このアラートは、頻繁に容量に達しているかどうかを理解するのに役立ちます。そのような状況は、待機時間が発生する可能性が高いことを示しています。  

たとえば、3 つの S1 ユニットをプロビジョニング済み (または 1 分間の受信容量あたり 2100 イベント) の場合、**保存済みイベントの受信**アラートを 2 時間に対して 1900 イベント以上に設定できます。 このしきい値を頻繁に超えてアラートがトリガーされる場合、プロビジョニング不足である可能性が高いです。  

また、調整されている疑いがある場合、**受信メッセージの受信**をイベント ソースの送信メッセージと比較できます。  Event Hub への受信が**受信メッセージの受信**より大きい場合、Time Series Insights は調整されている可能性が高いです。

## <a name="improving-performance"></a>パフォーマンスの向上 
調整や待機時間を軽減するための最善の修正方法は、環境の容量を増やすことです。 

分析するデータ量に合わせて環境を正しく構成することによって待機時間と調整を回避できます。 環境内に容量を追加する方法の詳細については、「[環境のスケーリング](time-series-insights-how-to-scale-your-environment.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- 追加のトラブルシューティング手順は、「[Time Series Insights 環境の問題を診断して解決する](time-series-insights-diagnose-and-solve-problems.md)」を参照してください。
- 追加の支援については、[MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) でメッセージの交換を始めてください。 サポート オプションについては、[Azure サポート](https://azure.microsoft.com/support/options/)までお問い合わせいただくこともできます。
