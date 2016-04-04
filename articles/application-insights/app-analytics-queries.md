<properties 
	pageTitle="Application Insights Analytics の演算子とクエリ" 
	description="Application Insights の強力な検索ツールである Analytics でクエリを作成するために使われる演算子のリファレンス。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>



# Analytics でのクエリ


[Analytics](app-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、Analytics のクエリ言語について説明します。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

テレメトリに対するクエリは、ソース ストリームの参照と、それに続くフィルターのパイプラインで構成されます。次に例を示します。


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
パイプ文字 `|` が先頭に配置された各フィルターは、いくつかのパラメーターが設定される*演算子*のインスタンスです。この演算子への入力は、前のパイプラインの結果であるテーブルです。ほとんどの場合、パラメーターは入力の列に対する[スカラー式](app-analytics-scalars.md)ですが、まれに、入力列の名前である場合や、2 つ目のテーブルである場合もあります。列と行が 1 つずつしかなくても、クエリの結果は常にテーブルです。

クエリの前には 1 つ以上の [let 句](#let-clause)が配置されることがあります。これは、クエリ内で使用できるスカラー、テーブル、関数を定義するものです。

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> 次のクエリの例では、前のパイプラインまたはソース テーブルを示すために、`T` が使用されています。

## count 演算子

`count` 演算子は、入力レコード セットのレコード (行) の数を返します。

**構文**

    T | count

**引数**

* *T*: カウントするレコードが含まれる表形式のデータ。

**戻り値**

この関数は、1 つのレコードと `long` 型の列を含むテーブルを返します。唯一のセルの値は、*T* 内のレコードの数です。

**例**

```AIQL
requests | count
```



## extend 演算子

     T | extend duration = stopTime - startTime

テーブルに 1 つ以上の計算列を追加します。


**構文**

    T | extend ColumnName = Expression [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 追加する列の名前。 
* *Expression:* 既存の列に対する計算。

**戻り値**

指定の列が追加された、入力テーブルのコピー。

**ヒント**

* ソース列を削除するか、ソース列の名前を変更する場合は、[`project`](#project-operator) を代わりに使います。
* 長い式で使う短い名前を取得するためだけに `extend` を使うのは避けてください。`...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    テーブルのネイティブ列のインデックスは既に作成されていますが、新しい名前により、インデックスが作成されていない追加の列が定義されるため、クエリの実行速度が低下する可能性があります。

**例**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## join 演算子

    Table1 | join (Table2) on CommonColumn

指定された列の値を照合して 2 つのテーブルの行をマージします。


**構文**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**引数**

* *Table1* - 結合の "左側"。
* *Table2* - 結合の "右側"。テーブルを出力する入れ子のクエリ式にすることができます。
* *CommonColumn* - 2 つのテーブルで同じ名前を持つ列。
* *Kind* - 2 つのテーブルの行を照合する方法を指定します。

**戻り値**

次の要素が含まれるテーブル:

* 照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。名前が競合している場合は、右側の列の名前が自動的に変更されます。
* 入力テーブル間でのすべての一致に対応する行。もう一方のテーブルとすべての `on` フィールドの値が同じであるテーブルから選択された行です。 

* `Kind` が未指定の場合

    左側の 1 つの行のみが `on` キーの各値に対して照合されます。出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。

* `Kind=inner`
 
     出力には、左右の一致行のすべての組み合わせに対応する行が含まれます。

* `kind=leftouter` (あるいは `kind=rightouter` または `kind=fullouter`)

     内部の一致のほかに、左側と右側のどちらか一方または両方のすべての行に対応する行が含まれます (一致するものがない場合も)。その場合、一致しない出力セルには null が含まれます。

* `kind=leftanti`

     右側との一致が存在しない、左側のすべてのレコードを返します。結果のテーブルには、左側の列のみが含まれます。
 
それらのフィールドの値が同じである行が複数存在する場合は、そのすべての組み合わせの行が返されます。

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* 入力テーブルの行と列の数を減らすには、`join` の前に `where` と `project` を使います。 
* 一方のテーブルがもう一方よりも常に小さい場合は、それを結合の左側 (パイプされる側) として使います。
* 一致した場合に結合する列は、同じ名前を持つ必要があります。いずれかのテーブルの列の名前を変更する必要がある場合は、project 演算子を使います。

**例**

アクティビティの開始と終了がマークされているエントリが含まれるログから、延長されたアクティビティを取得します。

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

[join の種類についてはこちらを参照してください](app-analytics-samples.md#join-flavors)。

## let 句

**表形式の let - テーブルに名前を付ける**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**スカラーの let - 値に名前を付ける**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**ラムダの let - 関数に名前を付ける**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

let 句は、名前を表形式の結果、スカラー値、または関数にバインドします。この句はクエリのプレフィックスであり、バインドのスコープはそのクエリです(let では、セッションの後の方で使用するものに名前を付けることはできません)。

**構文**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *型:* `bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](app-analytics-scalars.md#dynamic-type)
* *plain\_query:* let 句のプレフィックスが付いていないクエリ。

**例**




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


自己結合:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

## limit 演算子

     T | limit 5

指定の数を上限に、入力テーブルから行を返します。どのレコードが返されるかはわかりません(特定のレコードを返すには、[`top`](#top-operator) を使います)。

**エイリアス** `take`

**構文**

    T | limit NumberOfRows


**ヒント**

`Take` は、インタラクティブに操作しているときに結果のサンプルを見るうえで、シンプルかつ効率的な手段です。ただし、特定の行を生成したり、特定の順序で生成したりすることはできません。

`take` を使用していなくても、クライアントに返される行の数には暗黙的な制限があります。この制限を引き上げるには、`notruncation` クライアント要求オプションを使います。



## mvexpand 演算子

    T | mvexpand listColumn 

動的に型指定された (JSON) セルからリストを展開して、エントリごとに別の行になるようにします。展開された行内のその他すべてのセルは複製されます

(逆の処理を実行する [`summarize makelist`](#summarize-operator) も参照してください)。

**例**

次のような入力テーブルを想定します。

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

結果は次のとおりです。

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**構文**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**引数**

* *ColumnName:* 結果では、指定された列内の配列が複数の行に展開されます。 
* *ArrayExpression:* 配列を生成する式。この形式を使用した場合は、新しい列が追加され、既存の列は保持されます。
* *Name:* 新しい列の名前。
* *Typename:* 展開された式を特定の型にキャストします。
* *RowLimit:* 元の各行から生成される行の最大数。既定値は 128 です。

**戻り値**

指定された列の配列か、配列の式の各値に対応する複数の行。

展開された列は常に動的な型を持ちます。値を計算または集計する場合は、`todatetime()` や `toint()` などのキャストを使います。

2 つのモードのプロパティ バッグの展開がサポートされています。

* `bagexpansion=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。これが既定の展開です。
* `bagexpansion=array`: プロパティ バッグは 2 要素 (`[`*key*`,`*value*`]`) の配列構造に展開されるため、キーと値への一貫したアクセスが可能です (プロパティ名での個別のカウントの集計など)。 

**例**


    exceptions | take 1 
    | mvexpand details[0]

例外のレコードを詳細フィールドの項目ごとに行に分割します。

[一定時間におけるライブ アクティビティのカウントのグラフ](app-analytics-samples.md#concurrent-activities)を参照してください。


## parse 演算子

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

文字列から値を抽出します。単純な照合または正規表現の照合を使用できます。

`with` 句内の要素は、ソース文字列に対して順番に照合されます。この要素ごとに、ソース テキストのチャンクが処理されます。これがプレーンな文字列である場合、照合カーソルはその一致の範囲内で移動します。型名を持つ列である場合、カーソルは指定された型を解析できる範囲内で移動します(文字列の照合は、次の要素への一致が見つかるまで行われます)。 正規表現である場合、正規表現が照合されます (結果の列は常に文字列型となります)。

**構文**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**引数**

* *T:* 入力テーブル。
* *kind:* simple または regex。既定値は simple です。
* *StringExpression:* 文字列に評価されるか、文字列に変換できる式。
* *SimpleMatch:* テキストの次の部分と照合される文字列。
* *Column:* 一致したものを割り当てる新しい列を指定します。
* *Type:* ソース文字列の次の部分を解析する方法を指定します。
* *Regex:* 文字列の次の部分を照合するための正規表現。 

**戻り値**

列のリストに応じて展開された入力テーブル。


**例**

`parse` 演算子を使えば、同じ `string` 式に対して複数の `extract` アプリケーションを使えるため、テーブルの `extend` を合理的に行えます。これが最も役に立つのは、個別の列 (開発者のトレース ("`printf`"/"`Console.WriteLine`") ステートメントによって生成された列など) に分割したい複数の値が格納された `string` 列がテーブルに存在する場合です。

以下の例では、テーブル `StormEvents` の列 `EventNarrative` に `{0} at {1} crested at {2} feet around {3} on {4} {5}` という形式の文字列が含まれているものとします。以下の操作では、2 つの列 (`SwathSize` と `FellLocation`) を持つテーブルを展開します。


|EventNarrative|
|---|
|The Green River at Brownsville crested at 18.8 feet around 0930EST on December 12.Flood stage at Brownsville is 18 feet.Minor flooding occurs at this level.The river overflows lock walls and some of the lower banks, along with some agricultural bottom land.|
|The Rolling Fork River at Boston crested at 39.3 feet around 1700EST on December 12.Flood stage at Boston is 35 feet.Minor flooding occurs at this level, with some agricultural bottom land covered.|
|The Green River at Woodbury crested at 36.7 feet around 0600EST on December 16.Flood stage at Woodbury is 33 feet.Minor flooding occurs at this level, with some lowlands around the town of Woodbury covered with water.|
|The Ohio River at Tell City crested at 39.0 feet around 7 AM EST on December 18.Flood stage at Tell City is 38 feet.At this level, the river begins to overflow its banks above the gage.Indiana Highway 66 floods between Rome and Derby.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|Location (場所)|高さ|Time|月|日|
|---|---|---|---|---|---|
|The Green River | Woodbury |36\.7| 0600EST | December|16|
|The Rolling Fork River | ボストン |39\.3| 1700EST | December|12|
|The Green River | Brownsville |18\.8| 0930EST | December|12|
|The Ohio River | Tell City |39| 7 AM EST | December|18|

正規表現を使って照合することもできます。以下のコードでも同じ結果が生成されますが、結果の列はすべて文字列型になります。

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## project 演算子

    T | project cost=price*quantity, price

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。結果の列の順序は、引数の順序によって指定されます。引数で指定された列のみが結果に含まれ、入力内のそれ以外の列は削除されます(`extend` も参照してください)。


**構文**

    T | project ColumnName [= Expression] [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 出力に表示される列の名前。*Expression* を指定しない場合は、この名前の列が入力に存在する必要があります。 
* *Expression:* 入力列を参照する、省略可能なスカラー式。 

    入力内の既存の列と同じ名前を持つ新しい計算列を返すことは、問題ありません。

**戻り値**

引数で指定された列と、入力テーブルと同数の行が含まれるテーブル。

**例**

次の例は、`project` 演算子を使って実行できる何種類かの操作を示しています。入力テーブル `T` には、`int` 型の列が 3 つあります (`A`、`B`、`C`)。

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[その他の例についてはこちらを参照してください](app-analytics-samples.md#activities)。


## range 演算子

    range LastWeek from ago(7d) to now() step 1d

値が含まれる 1 列のテーブルを生成します。パイプラインの入力はないことに注目してください。

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**構文**

    range ColumnName from Start to Stop step Step

**引数**

* *ColumnName:* 出力テーブル内の 1 つの列の名前。
* *Start:* 出力の最小値。
* *Stop:* 出力で生成される最高値 (*step* でこの値をステップオーバーする場合は、最高値へのバインド)。
* *Step:* 2 つの連続する値の差異。 

この引数は、数値、日付、または期間の値である必要があります。テーブルの列を参照することはできません(入力テーブルに基づいて範囲を計算する場合は、[range *関数*](app-analytics-scalars.md#range)を使います。その際は、[mvexpand 演算子](#mvexpand-operator)も組み合わせて使うことになると思われます)。

**戻り値**

*ColumnName* という 1 つの列を持つテーブルです。その列の値は、*Start*、*Start* + *Step* というぐあいに続き、*Stop* までとなります。

**例**

```AIQL
range Steps from 1 to 8 step 3
```

`Steps` という 1 つの列を持つテーブルです。その列の型は `long`、値は `1`、`4`、`7` です。

**例**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

過去 7 日間の深夜のテーブルです。bin (floor) 関数により、各時刻が 1 日の開始時刻になっています。

**例**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

`range` 演算子を使って小規模でアドホックなディメンション テーブルを作成する方法を示しています。さらに、このテーブルは、ソース データに値がない場合にゼロを取り込むために使用されています。

## reduce 演算子

    exceptions | reduce by outerMessage

類似したレコードのグループ化を試みます。この演算子は、グループごとに、そのグループを最も適切に表していると思われる `Pattern` と、そのグループ内のレコードの `Count` を出力します。


![](./media/app-analytics-queries/reduce.png)

**構文**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**引数**

* *ColumnName:* 調べる列。文字列型である必要があります。
* *Threshold:* 範囲 {0..1} 内の値。既定値は 0.001 です。入力のサイズが大きい場合は、しきい値を小さくする必要があります。 

**戻り値**

`Pattern` と `Count` の 2 つの列。多くの場合、Pattern は列の完全な値になります。一部のケースでは、共通するタームを識別し、変数の部分を '*' に置き換えることもできます。

たとえば、`reduce by city` の結果には次のものが含まれます。

|パターン | カウント |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| パリ | 27163 |


## render ディレクティブ

    T | render [ table | timechart  | barchart | piechart ]

render では、テーブルの表示方法をプレゼンテーション層に指示します。パイプの最後の要素である必要があります。特定のプレゼンテーション メソッドでクエリを保存でき、ディスプレイのコントロールの代わりに使用できる便利な方法です。


## sort 演算子 

    T | sort by country asc, price desc

入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

**エイリアス** `order`

**構文**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**引数**

* *T:* 並べ替えるテーブルの入力。
* *Column:* 並べ替えに使用する *T* の列。値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc`: 昇順で (小さい値から大きい値へ) 並べ替えます。既定値は `desc` で、降順 (大きい値から小さい値へ) です。

**例**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
特定の `ActivityId` を持つ Traces テーブルのすべての行。タイムスタンプの順で並べ替えられています。

## summarize 演算子

入力テーブルの内容を集計したテーブルを生成します。
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

数、平均要求期間、各国の都市のセットを示すテーブルです。出力には国ごとの行が含まれます。出力列には、数、平均期間、都市、国が示されます。他のすべての入力列は無視されます。


    T | summarize count() by price_range=bin(price, 10.0)

各間隔 ([0,10.0]、[10.0,20.0] など) で価格を持つ項目の数を示すテーブル。この例では、数の列と価格範囲の列があります。他のすべての入力列は無視されます。

[その他の例についてはこちらを参照してください](app-analytics-aggregations.md)。



**構文**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**引数**

* *Column:* 結果列の省略可能な名前。既定値は式から派生した名前です。
* *Aggregation:* 引数として列名を持つ、`count()` や `avg()` などの[集計関数](app-analytics-aggregations.md)を呼び出します。[集計関数のリスト](app-analytics-aggregations.md)を参照してください。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。通常は、限られた値のセットが既に指定されている列名か、引数として数値列または時間列を持つ `bin()` になります。 

`bin()` を使用せずに数値式または時間式を指定した場合、Analytics は自動的に `1h` (時間の場合) または `1.0` (数値の場合) の間隔でそれを適用します。

*GroupExpression* を指定しないと、テーブル全体が単一の出力行にまとめられます。



**戻り値**

入力列は、`by` 式の同じ値を持つグループにまとめられます。次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。結果には、`by` 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます(一部の集計関数は複数の列を返します)。

結果には、`by` 値の個別の組み合わせと同数の列が含まれます。数値の範囲をまとめる場合は、`bin()` を使って範囲を不連続値に減らしてください。

**注**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用するか、`bin()` を数値列に適用する方がより効率的です。



## take 演算子

[limit](#limit-operator) のエイリアス


## top 演算子

    T | top 5 by Name desc

指定された列で並べ替えられた、先頭の *N* 個のレコードを返します。


**構文**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**引数**

* *NumberOfRows:* 返す *T* の行の数。
* *Sort\_expression:* 行の並べ替えに使用する式。通常は単なる列名です。複数の sort\_expression を指定できます。
* 選択が範囲の "下限" からのものか "上限" からのものかを制御するため、`asc` または `desc` (既定値) が表示される場合があります。


**ヒント**

`top 5 by name` は表面的には `sort by name | take 5` と同等です。しかし、こちらの方が高速で、常に並べ替えられた結果を返します。`take` にはこのような保証がありません。


## union 演算子

     Table1 | union Table2, Table3

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。

**構文**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**引数**

* *Table1*, *Table2* ...
 *  `events` などのテーブルの名前
 *  または `(events | where id==42)` などのクエリ式
 *  ワイルドカードで指定されたテーブルのセット。たとえば、`E*` は、名前が `E` から始まるデータベース内の全テーブルを結合します。
* `kind`: 
 * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
 * `outer` - 結果には、入力のいずれかに存在するすべての列が含まれます。入力行で定義されていなかったセルは `null` に設定されます。
* `withsource=`*ColumnName:* 指定されている場合は、各行の基になっているソース テーブルを示す値が格納された列 (名前は *ColumnName*) が出力に含まれます。

**戻り値**

すべての入力テーブルに存在する行と同数の行を含むテーブル。

**例**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
名前が "tt" から始まるすべてのテーブルの和集合。


**例**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
過去 1 日で `exceptions` イベントまたは `traces` イベントを発生させた個別のユーザーの数。結果の "SourceTable" 列は "Query" または "Command" を指します。

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

より効率的なこのバージョンでも同じ結果が生成されます。和集合を作成する前に各テーブルをフィルター処理します。

## where 演算子

     T | where fruit=="apple"

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

**エイリアス** `filter`

**構文**

    T | where Predicate

**引数**

* *T*: フィルター処理するレコードが含まれる表形式の入力。
* *Predicate:* *T* の列に対する `boolean` [式](app-analytics-scalars.md#boolean)。*T* 内の各行について評価されます。

**戻り値**

*Predicate* が `true` である *T* 内の行。

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* 列名と定数の**シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。`now()` と `ago()` は適切で、[`let` ステートメント](app-analytics-syntax.md#let-statements)を使って割り当てられるスカラー値です)。

    たとえば、`where floor(Timestamp, 1d) == ago(1d)` よりも `where Timestamp >= ago(1d)` の方が適切です。

* **最もシンプルな語句を先頭に配置する**: `and` で連結された複数の句がある場合は、関係する列が 1 つしかない句を先頭に配置します。そのため、`Timestamp > ago(1d) and OpId == EventId` の方が、逆の順序にするよりも適切です。


**例**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

同じ値の 2 列を持ち、"Kuskus" というソースから取得された、1 時間以内のレコードです。

2 つの列の比較を最後に配置していることに注目してください。これは、インデックスを使用できず、スキャンを強制するためです。





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->