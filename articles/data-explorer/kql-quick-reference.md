---
title: KQL クイック リファレンス
description: 有用な KQL 関数およびその定義と構文例の一覧。
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: c5d331b809d3f9f7db8d8c36add8b283bf0b9e39
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664866"
---
# <a name="kql-quick-reference"></a>KQL クイック リファレンス

この記事では、Kusto クエリ言語の使用を開始するのに役立つ関数とその定義の一覧を示します。

| 演算子/関数                               | 説明                           | 構文                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**フィルター/検索/条件**                      |**_フィルターまたは検索によって関連データを検索します_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | 特定の述語でのフィルター。           | `T | where Predicate`                         |
| [where contains/has](/azure/kusto/query/whereoperator)        | `Contains`: 部分文字列の一致が検索されます。 <br> `Has`: 特定の単語が検索されます (パフォーマンスの向上)。  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator)                    | テーブル内のすべての列で値が検索されます。 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator)                        | 指定された数のレコードが返されます。 クエリのテストに使用します。<br>**_注_** : `_take`_ と `_limit`_ はシノニムです。 | `T | take NumberOfRows` |
| [case](/azure/kusto/query/casefunction)                        | 他のシステムの if/then/elseif と同様、条件ステートメントが追加されます。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | 入力テーブルの指定された列の個別の組み合わせを含むテーブルが生成されます。 | `distinct [ColumnName], [ColumnName]` |
| **日付/時刻**                                   |**_日付と時刻の関数を使用する演算_**               |                          |
|[ago](/azure/kusto/query/agofunction)                           | クエリが実行された時刻に対する時間オフセットが返されます。 たとえば、`ago(1h)` は、現在のクロックの読み取り値の 1 時間前になります。 | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | データが[さまざまな日付の形式](/azure/kusto/query/format-datetimefunction#supported-formats)で返されます。 | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | 期間内のすべての値が丸めてグループ化されます。 | `bin(value,roundTo)` |
| **列の作成/削除**                   |**_テーブル内の列が追加または削除されます_** |                                                    |
| [print](/azure/kusto/query/printoperator)                      | 1 つ以上のスカラー式を使用して単一行が出力されます。 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator)                  | 指定された順序で含める列が選択されます | `T | project ColumnName [= Expression] [, ...]` <br> または <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | 出力から除外する列を選択します。 | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator)                    | 計算列が作成され、それが結果セットに追加されます。 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **データセットの並べ替えと集計**                 |**_並べ替えまたはグループ化により、データが意味のある方法で再構築されます_**|                  |
| [sort](/azure/kusto/query/sortoperator)                        | 入力テーブルの行が 1 つ以上の列で昇順または降順に並べ替えられます。 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](/azure/kusto/query/topoperator)                          | `by` を使用してデータセットが並べ替えられた場合に、データセットの先頭の N 行が返されます。 | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator)              | `by` グループ列に従って行がグループ化され、各グループの集計が計算されます。 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | 入力テーブル内のレコード数がカウントされます (たとえば、T)。<br>この演算子は、`summarize count() ` の短縮形です。| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | 各テーブルの指定された列で値が照合され、2 つのテーブルの行がマージされ、新しいテーブルが形成されます。 結合型のすべての種類 (`flouter`、`inner`、`innerunique`、`leftanti`、`leftantisemi`、`leftouter`、`leftsemi`、`rightanti`、`rightantisemi`、`rightouter`、`rightsemi`) がサポートされます。 | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator)                      | 複数のテーブルが受け取られ、それらすべてのテーブルの行が返されます。 | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator)                      | 等差級数の値を含むテーブルが生成されます。 | `range columnName from start to stop step step` |
| **データの書式設定**                                 | **_有用な方法でデータが再構築され出力されます_** | |
| [lookup](/azure/kusto/query/lookupoperator)                    | ディメンション テーブルで検索された値が使用されて、ファクトテーブルの列が拡張されます。 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator)               | 動的配列が行に変換されます (複数値の展開)。 | `T | mv-expand Column` |
| [parse](/azure/kusto/query/parseoperator)                      | 文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 非構造化データを構造化するために使用されます。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | 指定された軸に沿って、指定された集計値の系列が作成されます。 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](/azure/kusto/query/letstatement)                         | バインドされた値を参照できる式に名前がバインドされます。 値をラムダ式にして、クエリの一部としてアドホック関数を作成することができます。 `let` を使用して、結果が新しいテーブルのように見えるテーブルに式が作成されます。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **全般**                                     | **_その他の演算と関数_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | 入力として受け取るテーブルで関数が実行されます。 | `T | invoke function([param1, param2])` |
| [evaluate pluginName](/azure/kusto/query/evaluateoperator)     | クエリ言語拡張機能 (プラグイン) が評価されます。 | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覚化**                               | **_データをグラフィック形式で表示する演算_** | |
| [render](/azure/kusto/query/renderoperator) | 結果がグラフィック出力としてレンダリングされます。 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
