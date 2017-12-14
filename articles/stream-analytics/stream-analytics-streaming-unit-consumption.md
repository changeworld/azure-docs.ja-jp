---
title: "Azure Stream Analytics: ストリーミング ユニットの理解と調整 | Microsoft Docs"
description: "Azure Stream Analytics のパフォーマンスに影響を与える要因を理解します。"
keywords: "ストリーミング ユニット, クエリ パフォーマンス"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e8812f10662ee7b571e8e353074c2537d1a3181b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="understand-and-adjust-streaming-units"></a>ストリーミング ユニットの理解と調整

Azure Stream Analytics では、ジョブを実行する際のパフォーマンスの "重さ" をストリーミング ユニット (SU) に集約しています。 SU とは、ジョブの実行に消費するコンピューティング リソースのことです。 SU は、CPU、メモリ、および読み取りと書き込みのレートを組み合わせた測定に基づいて、相対的なイベントの処理能力を記述する方法を提供します。 これにより、クエリ ロジックに集中することができ、ストレージ層でのパフォーマンスに関する考慮事項を知っておく必要がなくなります。また、ジョブに手動でメモリを割り当てたり、ジョブを時間内に実行するために必要な CPU コア数を概算したりすることもできます。

待機時間が短いストリーミング処理を実現するために、Azure Stream Analytics ジョブはすべての処理をメモリ内で実行します。 メモリが不足した場合は、ストリーミング ジョブが失敗します。 そのため、実稼働ジョブでは、ストリーミング ジョブのリソース使用状況を監視し、ジョブの稼働を 24 時間、週 7 日維持するのに十分なリソースが割り当てられていることを確認することが重要です。

メトリックは、0% から 100% の範囲のパーセンテージの数値です。 最小フットプリントのストリーミング ジョブでは、SU % 使用率メトリックは、通常は 10 ～ 20% です。 偶発的なスパイクを考慮して、メトリックを 80% 未満に維持することをお勧めします。  メトリックにはアラートを設定できます ([メトリック アラートを設定するには、こちら](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)をご覧ください)。



## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics のストリーミング ユニット (SU) を構成する
1. [Azure Portal](http://portal.azure.com/) にサインインします
2. リソースの一覧で、スケールする Stream Analytics ジョブを見つけて開きます。 
3. ジョブ ページで、[構成] の **[スケール]** をクリックします。 

    ![Azure Portal での Stream Analytics ジョブの構成][img.stream.analytics.preview.portal.settings.scale]
    
4. スライダーを使用してジョブの SU 数を設定します。 特定の SU 設定に限定されることに注意してください。 


## <a name="monitor-job-performance"></a>ジョブのパフォーマンスを監視する
Azure Portal を使用して、ジョブのスループットを追跡できます。

![Azure Stream Analytics のジョブの監視][img.stream.analytics.monitor.job]

ワークロードの予想されるスループットを計算します。 スループットが予想よりも低い場合は、入力パーティションを調整し、クエリをチューニングして、ジョブに SU を追加します。


## <a name="how-many-sus-are-required-for-a-job"></a>ジョブに必要な SU の数

特定のジョブに必要な SU の数の選択は、入力のパーティション構成と、ジョブで定義されたクエリによって異なります。 **[スケール]** ページでは、適切な SU の数を設定することができます。 SU は、必要な量より多めに割り当てておくことをお勧めします。 メモリをより多く割り当てると、Stream Analytics 処理エンジンによって待ち時間とスループットが最適化されます。

一般に、**PARTITION BY** を使用していないクエリであれば、6 個の SU から始めることがベスト プラクティスです。 そのうえで、試行錯誤により最適な数を検討します。具体的には、典型的な量のデータを渡して SU% 使用率のメトリックを確認し、SU の数を調整する作業を繰り返すことによって、最適な SU の数を割り出します。

適切な SU 数の選択について詳しくは、「[スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」をご覧ください

> [!Note]
> 特定のジョブに必要な SU 数の選択は、入力のパーティション構成と、ジョブに定義されたクエリによって異なります。 1 つのジョブについて、クォータまでの SU 数を選択できます。 既定では、各 Azure サブスクリプションには、特定のリージョン内のすべての分析ジョブを対象に最大 200 個という SU のクォータが設定されています。 このクォータを超えてサブスクリプションの SU 数を増やす場合は、[Microsoft サポート](http://support.microsoft.com)までご連絡ください。 ジョブごとの SU 数の有効な値は 1、3、6 であり、6 ずつ増加します。



## <a name="factors-increasing-su-utilization"></a>SU% 使用率が増加する要因 
### <a name="stateful-query-logic"></a>ステートフル クエリ ロジック 
Azure Stream Analytics ジョブの固有の機能の 1 つに、ウィンドウ集計、一時的な結合、一時的な分析関数などのステートフル処理の実行があります。 これらの各演算子は、一部の状態を保持します。 
#### <a name="windowed-aggregates"></a>ウィンドウ集計
ウィンドウ集計の状態サイズは、group by 演算子のグループの数 (基数) に比例します。 たとえば、次のクエリでは、clusterid に関連付けられた数字がクエリの基数です。 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


前のクエリで大きな基数によって引き起こされる問題を軽減するために、'clusterid' でパーティション分割されたイベントをイベント ハブに送信し、次の例で示すように **PARTITION BY** を使用してシステムが各入力パーティションを個別に処理できるようにすることでクエリをスケールアウトできます。


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信される clusterid 数が削減されるため、group by 演算子の基数が減少します。 

削減手順の必要性をなくすには、イベント ハブ パーティションをグループ化キーでパーティション分割する必要があります。 詳しくは、[こちら](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview)をご覧ください。 
#### <a name="temporal-join"></a>一時的な結合
一時的な結合の状態サイズは、イベント入力速度にウィグル領域サイズを乗算した、結合の一時的なウィグル領域におけるイベント数に比例します。 

結合内の不一致のイベントの数は、クエリのメモリ使用率に影響します。 次のクエリは、クリックに結びつく広告のインプレッションを検索します。

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

この例では、多数の広告が表示されても、ほとんどのユーザーがクリックしていない可能性があり、時間枠内にすべてのイベントを保持する必要があります。 消費されるメモリは、時間枠のサイズおよびイベント レートに比例します。 

これを修復するには、結合キー (この場合は id) でパーティション分割されたイベントをイベント ハブに送信し、次に示すように **PARTITION BY** を使用して各入力パーティションをシステムが個別に処理できるようにすることでクエリをスケールアウトします。

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、結合ウィンドウに保持される状態のサイズが減少します。 
#### <a name="temporal-analytic-function"></a>一時的な分析関数
一時的な分析関数の状態サイズは、期間で乗算したイベント レートに比例します。 修復は、一時的な結合と同様です。 **PARTITION BY** を使用してクエリをスケールアウトできます。 

### <a name="out-of-order-buffer"></a>誤順序バッファー 
ユーザーは、[イベント順序] 構成ウィンドウで誤順序バッファー サイズを構成できます。 バッファーは、ウィンドウの継続期間にわたって入力を保持し、それらを並べ替えるために使用されます。 バッファーのサイズは、誤順序ウィンドウのサイズを乗算したイベント入力速度に比例します。 既定のウィンドウ サイズは 0 です。 

これを修復するには、**PARTITION BY** を使用してクエリをスケールアウトします。 クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、各並べ替えバッファー内のイベント数が減少します。 

### <a name="input-partition-count"></a>入力パーティション数 
ジョブ入力の各入力パーティションにはバッファーがあります。 入力パーティション数が多いほど、ジョブで消費されるリソースが多くなります。 各 SU で、Azure Stream Analytics は約 1 MB/秒の入力を処理できるため、ASA SU 数をイベント ハブのパーティション数に一致させます。 通常、2 つのパーティションがあるイベント ハブ (イベント ハブの最小値) には 1SU ジョブで十分です。 イベント ハブにより多くのパーティションがある場合、ASA ジョブはより多くのリソースを消費しますが、イベント ハブによって提供される追加のスループットを必ずしも使用しません。 6SU ジョブの場合は、イベント ハブから 4 または 8 パーティションが必要な場合があります。 16 以上のパーティションを持つイベント ハブを 1SU ジョブで使用すると、リソースの過剰使用に寄与することが多いため、これは避ける必要があります。 

### <a name="reference-data"></a>参照データ 
ASA の参照データは、高速検索を行うためにメモリに読み込まれます。 現在の実装では、同じ参照データで複数回結合する場合でも、参照データを使用する結合操作ごとに参照データのコピーがメモリに保持されます。 **PARTITION BY** を使用したクエリでは、各パーティションに参照データのコピーが 1 つあるため、パーティションは完全に分離されます。 相乗効果により、複数のパーティションで参照データを使用して複数回結合した場合、メモリ使用量がすぐに非常に高くなることがあります。  

#### <a name="use-of-udf-functions"></a>UDF 関数の使用
UDF 関数を追加すると、Azure Stream Analytics は、JavaScript ランタイムをメモリに読み込みます。 これは SU% に影響します。


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics での並列化可能クエリの作成](stream-analytics-parallelization.md)
* [スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
