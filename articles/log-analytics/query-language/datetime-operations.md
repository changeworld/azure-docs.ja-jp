---
title: Azure Log Analytics クエリでの日時値の操作 | Microsoft Docs
description: Log Analytics クエリで日時のデータを操作する方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 3a0e2b78de8cea3929ac457bab3d5e07a2b85401
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603381"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Log Analytics クエリでの日時値の操作

> [!NOTE]
> このレッスンを完了する前に、[Analytics ポータルの概要](get-started-analytics-portal.md)および[クエリの概要](get-started-queries.md)に関するチュートリアルを完了する必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

この記事では、Log Analytics クエリで日時のデータを操作する方法について説明します。


## <a name="date-time-basics"></a>日時の基本
Log Analytics クエリ言語では、主に 2 つのデータ型が (datetime と timespan) が日付と時刻に関連付けられています。 日付はすべて UTC で表されています。 複数の datetime 形式がサポートされていますが、ISO8601 形式をお勧めします。 

timespan を表現するには、10 進数の後に時間単位を続けます。

|短縮形   | 時間単位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | 分       |
|s           | 秒       |
|ms          | ミリ秒  |
|microsecond | マイクロ秒  |
|tick        | ナノ秒   |

datetime を作成するには、`todatetime` 演算子を使用して文字列をキャストします。 たとえば、特定の期間に送信される VM ハートビートを確認するには、時間の範囲の指定に便利な [between 演算子](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator)を使用できます。

```KQL
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

datetime を現在と比較するシナリオも一般的です。 たとえば、過去 2 分間のすべてのハートビートを確認するには、`now` 演算子と 2 分を示す timespan を一緒に使用します。

```KQL
Heartbeat
| where TimeGenerated > now() - 2m
```

この関数にはショートカットも使用できます。
```KQL
Heartbeat
| where TimeGenerated > now(-2m)
```

ただし、最も短く読みやすいのは、`ago` 演算子を使用する方法です。
```KQL
Heartbeat
| where TimeGenerated > ago(2m)
```

たとえば、開始時刻と終了時刻ではなく、開始時刻と期間がわかっているとします。 クエリは次のように書き換えることができます。

```KQL
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>時間単位の変換
datetime または timespan を既定以外の時間単位で表せると便利です。 たとえば、過去 30 分間のエラー イベントを確認しているときに、イベントがどのくらい前に発生したかを示す計算列が必要だとします。

```KQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

_timeAgo_ 列には、"00:09:31.5118992" などの値が含まれています。つまり、形式は hh:mm:ss.fffffff です。 これらの値を、開始時刻からの時間 (分) の _numver_ に設定する場合は、その値を "1 分" で除算するだけです。

```KQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>集計と期間でのバケット
さらに、特定の時間グレインで、特定の期間にわたる統計値を取得しなければならないシナリオも、非常によく見られます。 この場合は、`bin` 演算子を、summarize 句の一部として使用できます。

次のクエリを使用すると、過去 30 分間に発生したイベント数を 5 分ごとに取得できます。

```KQL
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

これにより次のテーブルが生成されます。  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

`startofday` などの関数を使用して、結果のバケットを作成することもできます。

```KQL
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

これにより次の結果が生成されます。

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416  |


## <a name="time-zones"></a>タイム ゾーン
すべての datetime 値が UTC で表されるため、多くの場合、これらの値をローカル タイムゾーンに変換すると便利です。 たとえば、次の計算を使用すると、UTC が PST 時間に変換されます。

```KQL
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>関連する関数

| Category | 関数 |
|:---|:---|
| データ型の変換 | [todatetime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())  [totimespan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| ビン サイズへの値を四捨五入 | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| 特定の日付または時刻を取得 | [ago](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [now](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| 値の一部を取得 | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| 値を基準にして日付を取得  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>次の手順
Log Analytics クエリ言語の使用については、他のレッスンをご覧ください。

- [文字列操作](string-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [詳細クエリ書き込み](advanced-query-writing.md)
- [結合](joins.md)
- [グラフ](charts.md)