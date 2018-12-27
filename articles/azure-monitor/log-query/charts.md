---
title: Azure Log Analytics クエリからのグラフと図の作成 |Microsoft Docs
description: さまざまな方法でデータを表示できるよう Azure Log Analytics に備えられている多様な視覚化について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 049e50f5800194c4126003f7e5ff7ae60b3de768
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186227"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>Log Analytics クエリからのグラフと図の作成

> [!NOTE]
> このレッスンを完了する前に、「[Advanced aggregations in Log Analytics queries (Log Analytics クエリの高度な集計)](advanced-aggregations.md)」のレッスンを完了する必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

この記事では、さまざまな方法でデータを表示できるよう Azure Log Analytics に備えられている多様な視覚化について説明します。

## <a name="charting-the-results"></a>結果のグラフ表示
まず、過去 1 時間のオペレーティング システムあたりのコンピューター数を確認します。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

既定では、結果はテーブルの形で表示されます。

![テーブル](media/charts/table-display.png)

より見やすくするために、**[グラフ]**、**[円]** オプションの順に選択し、結果を視覚化します。

![円グラフ](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>時間グラフ
プロセッサ時間の平均、50 パーセンタイル、95 パーセンタイルを 1 時間のビンに表示します。 クエリによって複数の系列が生成されるので、土の系列を時間グラフに表示するかを選択できます。

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**[折れ線]** グラフの表示オプションを選択します。

![折れ線グラフ](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>基準線

基準線を使用すると、メトリックが特定のしきい値を超えているかどうかを簡単に識別できます。 グラフに線を追加するには、定数列を追加してデータセットを拡張します。

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![基準線](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>複数のディメンション
`summarize` の`by` 句にある複数の式により、結果に複数の行が作成されます。値の組み合わせごとに 1 行ずつです。

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

結果をグラフとして表示するときには、`by` 句の最初の列が使用されます。 次の例は、_EventID_ を使用した積み上げ縦棒グラフを示しています。 ディメンションは `string` 型である必要があるため、この例では _EventID_ が文字列にキャストされます。 

![棒グラフ EventID](media/charts/charts-and-diagrams-multiDimension1.png)

ドロップダウンで列名を選択すると、切り替えができます。 

![棒グラフ AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>次の手順
Log Analytics クエリ言語の使用については、他のレッスンをご覧ください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [詳細クエリ書き込み](advanced-query-writing.md)
- [結合](joins.md)