---
title: Azure Data Explorer の時系列の異常検出と予測
description: Azure Data Explorer を使用して異常検出と予測のために時系列データを分析する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194159"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure Data Explorer での異常検出と予測

Azure Data Explorer を使用すると、クラウド サービスまたは IoT デバイスからのテレメトリ データを継続的に収集することができます。 このデータを分析することで、監視中のサービスの正常性、物理的な運用プロセス、使用状況の傾向、負荷予測など、さまざまな分析情報を得られます。 選択したメトリックの時系列の分析を実行することで、その通常の代表的なベースライン パターンと比較した偏差を調べることができます。 Azure Data Explorer には、複数の時系列を作成、操作、分析するためのネイティブ サポートが組み込まれています。 何千もの時系列を秒単位で作成および分析することができ、これによりリアルタイムに近い監視ソリューションとワークフローが可能になります。

この記事では、Azure Data Explorer の時系列の異常検出および予測の機能について詳しく説明します。 適用できる時系列関数は、よく知られている堅牢な分解モデルをベースにしています。このモデルでは、元の各時系列が季節、傾向、および残余コンポーネントに分解されます。 異常は残余コンポーネントでの外れ値によって検出され、予測は季節コンポーネントおよび傾向コンポーネントを外挿することで行われます。 Azure Data Explorer の実装では、自動的な季節性の検出、外れ値の堅牢な分析、および何千もの秒単位の時系列を処理するベクター化された実装によって基本的な分解モデルが大幅に強化されています。

## <a name="prerequisites"></a>前提条件

時系列機能の概要について、「[Azure Data Explorer の時系列分析](/azure/data-explorer/time-series-analysis)」をお読みになってください。

## <a name="time-series-decomposition-model"></a>時系列分解モデル

時系列の予測および異常検出に対応するの Azure Data Explorer ネイティブ実装では、よく知られている分解モデルが使用されます。 このモデルは、将来のメトリック値の予測および異常値の検出を行うために、サービス トラフィック、コンポーネント ハートビート、定期的な IoT 測定など、定期的および傾向的な動作を明らかにすることが期待されるメトリックの時系列に適用されます。 この回帰プロセスでは、以前から知られている季節的および傾向的な動作以外に、時系列がランダムに分散されることが前提になっています。 季節的および傾向的なコンポーネント (集合的にベースラインと呼ばれる) から将来のメトリック値を予測し、残りの部分は無視できます。 また、残りの部分のみを使用することにより、外れ値の分析に基づく異常値の検出を行うこともできます。
分解モデルを作成するには、関数 [`series_decompose()`](/azure/kusto/query/series-decomposefunction) を使用します。 `series_decompose()` 関数では、時系列のセットを受け取ると、各時系列が季節的、傾向的、残余、ベースラインの各コンポーネントを自動的に分解されます。 

たとえば、次のクエリを使用して、内部の Web サービスのトラフィックを分解することができます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![時系列分解](media/anomaly-detection/series-decompose-timechart.png)

* 元の時系列に **num** というラベル (赤色) を付けます。 
* プロセスでは、関数 [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) を使用して季節性を自動的に検出することから始まり、次に **季節** のパターン (紫色) を抽出します。
* 元の時系列から季節のパターンを差し引きます。関数 [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) を使用して線形回帰を実行することで、**傾向**コンポーネント (薄い青) を検索します。
* この関数によって傾向が差し引かれ、残りは、**残余** コンポーネント (緑色) となります。
* 最後に、この関数で季節コンポーネントと傾向コンポーネントが足し算され、**ベースライン** (青) が生成されます。

## <a name="time-series-anomaly-detection"></a>時系列の異常検出

関数 [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) を使用すると、時系列のセット上の異常なポイントを検索できます。 この関数では、`series_decompose()` を呼び出して分解モデルが作成され、次に残余コンポーネントに対して [`series_outliers()`](/azure/kusto/query/series-outliersfunction) が実行されます。 `series_outliers()` では、テューキーのフェンス テストを使用して、残余コンポーネントのポイントごとに異常スコアが算出されます。 異常スコアが 1.5 を超える場合は軽度の異常が発生したことを示し、-1.5 を下回る場合はそれが取り下げられたことを示します。 異常スコアが 3.0 を上回る場合または -3.0 を下回る場合は、重度の異常を示します。 

次のクエリでは、Web サービスの内部トラフィックでの異常を検出できます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![時系列の異常検出](media/anomaly-detection/series-anomaly-detection.png)

* 元の時系列 (赤色)。 
* ベースライン (季節 + 傾向) コンポーネント (青色)。
* 元の時系列の上に表示された異常ポイント (紫色)。 異常ポイントは、期待されるベースライン値から大幅に逸脱しています。

## <a name="time-series-forecasting"></a>時系列予測

関数 [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) では、時系列のセットの将来の値が予測されます。 この関数では `series_decompose()` を呼び出して分解モデルが作成され、さらに、時系列ごとに、将来のベースライン コンポーネントが推定されます。

次のクエリでは、次の週の Web サービス トラフィックを予測できます。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![時系列予測](media/anomaly-detection/series-forecasting.png)

* 元のメトリック (赤色)。 既定では、将来の値は存在せず、0 に設定されます。
* 次の週の値を予測するベースライン コンポーネント (青色) を推定します。

## <a name="scalability"></a>スケーラビリティ

Azure Data Explorer のクエリ言語構文では、1 回の呼び出しで複数の時系列を処理できます。 それ固有の最適化された実装では、リアルタイムに近いシナリオで何千ものカウンターを監視する場合、異常検出と予測を効果的に行うのに不可欠な高いパフォーマンスを実現できます。

次のクエリを見ると、3 つの時系列が同時に処理されているのがわかります。

**\[** [**クリックするとクエリが実行されます**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![時系列のスケーラビリティ](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>まとめ

このドキュメントでは、時系列の異常検出および予測を行うための Azure Data Explorer ネイティブ関数について詳しく説明しています。 元の各時系列は異常検出または予測を目的として季節、傾向、残余の各コンポーネントに分解されます。 これらの機能は、障害検出、予測メンテナンス、需要と負荷の予測などのリアルタイムに近い監視シナリオで使用することができます。

## <a name="next-steps"></a>次のステップ

Azure Data Explorer の[機械学習機能](/azure/data-explorer/machine-learning-clustering)について学習します。
