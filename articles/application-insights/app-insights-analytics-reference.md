<properties 
	pageTitle="Application Insights の Analytics のリファレンス。" 
	description="Application Insights の強力な検索ツールである Analytics の正規表現のリファレンス。" 
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
	ms.date="04/18/2016" 
	ms.author="awills"/>

# Analytics のリファレンス

[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、Analytics のクエリ言語について説明します。


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]


| | | | | 
|---|---|---|---|---
|[ago](#ago)|[dayofweek](#dayofweek)|[let 句](#let-clause)|[rand](#rand)|[sum](#sum)
|[任意](#any)|[dcount](#dcount)|[limit 演算子](#limit-operator)|[range](#range)|[summarize 演算子](#summarize-operator)
|[argmax](#argmax)|[let 句の動的オブジェクト](#dynamic-objects-in-let-clauses)|[log](#log)|[range 演算子](#range-operator)|[take 演算子](#take-operator)
|[argmin](#argmin)|[exp](#exp)|[makelist](#makelist)|[reduce 演算子](#reduce-operator)|[todatetime](#todatetime)
|[算術演算子](#arithmetic-operators)|[extend 演算子](#extend-operator)|[makeset](#makeset)|[render ディレクティブ](#render-directive)|[todouble](#todouble)
|[配列とオブジェクトのリテラル](#array-and-object-literals)|[extract](#extract)|[max](#max)|[replace](#replace)|[todynamic](#todynamic)
|[arraylength](#arraylength)|[extractjson](#extractjson)|[min](#min)|[restrict 句](#restrict-clause)|[toint](#toint)
|[avg](#avg)|[floor](#floor)|[mvexpand 演算子](#mvexpand-operator)|[スカラーの比較](#scalar-comparisons)|[tolong](#tolong)
|[bin](#bin)|[getmonth](#getmonth)|[notempty](#notempty)|[sort 演算子](#sort-operator)|[tolower](#tolower)
|[ブール型リテラル](#boolean-literals)|[gettype](#gettype)|[notnull](#notnull)|[split](#split)|[top 演算子](#top-operator)
|[ブール演算子](#boolean-operators)|[getyear](#getyear)|[now](#now)|[sqrt](#sqrt)|[totimespan](#totimespan)
|[buildschema](#buildschema)|[hash](#hash)|[数値リテラル](#numeric-literals)|[startofmonth](#startofmonth)|[toupper](#toupper)
|[キャスト](#casts)|[iff](#iff)|[難読化された文字列リテラル](#obfuscated-string-literals)|[startofyear](#startofyear)|[treepath](#treepath)
|[count](#count)|[isempty](#isempty)|[parse 演算子](#parse-operator)|[stdev](#stdev)|[union 演算子](#union-operator)
|[count 演算子](#count-operator)|[isnotempty](#isnotempty)|[parsejson](#parsejson)|[strcat](#strcat)|[variance](#variance)
|[countif](#countif)|[isnotnull](#isnotnull)|[percentile](#percentile)|[文字列の比較](#string-comparisons)|[where 演算子](#where-operator)
|[countof](#countof)|[isnull](#isnull)|[percentiles](#percentiles)|[文字列リテラル](#string-literals)
|[日付と時刻の式](#date-and-time-expressions)|[join 演算子](#join-operator)|[project 演算子](#project-operator)|[strlen](#strlen)
|[日付と時刻のリテラル](#date-and-time-literals)|[JSON パス式](#json-path-expressions)|[project-away 演算子](#project-away-operator)|[substring](#substring)


## クエリおよび演算子

テレメトリに対するクエリは、ソース ストリームの参照と、それに続くフィルターのパイプラインで構成されます。次に例を示します。


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
パイプ文字 `|` が先頭に配置された各フィルターは、いくつかのパラメーターが設定される*演算子*のインスタンスです。この演算子への入力は、前のパイプラインの結果であるテーブルです。ほとんどの場合、パラメーターは入力の列に対する[スカラー式](##scalars)ですが、まれに、入力列の名前である場合や、2 つ目のテーブルである場合もあります。列と行が 1 つずつしかなくても、クエリの結果は常にテーブルです。

クエリは、単一の改行を含めることができますが、空白行で終了します。クエリでは、`//` と行末の間に注釈が含まれる場合があります。

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
> 

### count 演算子

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



### extend 演算子

     T | extend duration = stopTime - startTime

テーブルに 1 つ以上の計算列を追加します。


**構文**

    T | extend ColumnName = Expression [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 追加する列の名前。[名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "\_" 文字を使用できます。キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Expression:* 既存の列に対する計算。

**戻り値**

指定の列が追加された、入力テーブルのコピー。

**ヒント**

* 複数の列を削除するか、複数の列の名前を変更する場合は、[`project`](#project-operator) を代わりに使います。
* 長い式で使う短い名前を取得するためだけに `extend` を使うのは避けてください。`...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    テーブルのネイティブ列のインデックスは既に作成されていますが、新しい名前により、インデックスが作成されていない追加の列が定義されるため、クエリの実行速度が低下する可能性があります。

**例**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### join 演算子

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

### let 句

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

let 句は、[名前](#names)を表形式の結果、スカラー値、または関数にバインドします。この句はクエリのプレフィックスであり、バインドのスコープはそのクエリです(let では、セッションの後の方で使用するものに名前を付けることはできません)。

**構文**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *型:* `bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](#dynamic-type)
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

### limit 演算子

     T | limit 5

指定の数を上限に、入力テーブルから行を返します。どのレコードが返されるかはわかりません(特定のレコードを返すには、[`top`](#top-operator) を使います)。

**エイリアス** `take`

**構文**

    T | limit NumberOfRows


**ヒント**

`Take` は、インタラクティブに操作しているときに結果のサンプルを見るうえで、シンプルかつ効率的な手段です。ただし、特定の行を生成したり、特定の順序で生成したりすることはできません。

`take` を使用していなくても、クライアントに返される行の数には暗黙的な制限があります。この制限を引き上げるには、`notruncation` クライアント要求オプションを使います。



### mvexpand 演算子

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



### parse 演算子

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
|The Green River (Brownsville) は December 12 の 0930EST 頃に最高水位 (18.8 フィート) に達しました。(The Green River at Brownsville crested at 18.8 feet around 0930EST on December 12.)Brownsville の洪水位は 18 フィートです。(Flood stage at Brownsville is 18 feet.)小規模な氾濫がこのレベルで発生します。(Minor flooding occurs at this level.)閘門壁と一部の堤防下部に加えて、農業用の低地の一部に河川の越流が起こります。(The river overflows lock walls and some of the lower banks, along with some agricultural bottom land.)|
|The Rolling Fork River (Boston) は December 12 の 1700EST 頃に最高水位 (39.3 フィート) に達しました。(The Rolling Fork River at Boston crested at 39.3 feet around 1700EST on December 12.)Boston の洪水位は 35 フィートです。(Flood stage at Boston is 35 feet.)小規模な氾濫がこのレベルで発生し、農業用の低地の一部が冠水します。(Minor flooding occurs at this level, with some agricultural bottom land covered.)|
|The Green River (Woodbury) は December 16 の 0600EST 頃に最高水位 (36.7 フィート) に達しました。(The Green River at Woodbury crested at 36.7 feet around 0600EST on December 16.)Woodbury の洪水位は 33 フィートです。(Flood stage at Woodbury is 33 feet.)小規模な氾濫がこのレベルで発生し、Woodbury の町周辺の低地が冠水します。(Minor flooding occurs at this level, with some lowlands around the town of Woodbury covered with water.)|
|The Ohio River (Tell City) は December 18 の 7 AM EST 頃に最高水位 (39.0 フィート) に達しました。(The Ohio River at Tell City crested at 39.0 feet around 7 AM EST on December 18.)Tell City の洪水位は 38 フィートです。(Flood stage at Tell City is 38 feet.)このレベルに達すると、量水標を超えて土手に河川の越流が起こります。(At this level, the river begins to overflow its banks above the gage.)インディアナ州道 66 号線はロームとダービー間で氾濫が発生します。(Indiana Highway 66 floods between Rome and Derby.)|

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


### project 演算子

    T | project cost=price*quantity, price

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。結果の列の順序は、引数の順序によって指定されます。引数で指定された列のみが結果に含まれ、入力内のそれ以外の列は削除されます(`extend` も参照してください)。


**構文**

    T | project ColumnName [= Expression] [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 出力に表示される列の名前。*Expression* を指定しない場合は、この名前の列が入力に存在する必要があります。[名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "\_" 文字を使用できます。キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Expression:* 入力列を参照する、省略可能なスカラー式。 

    入力内の既存の列と同じ名前を持つ新しい計算列を返すことは、問題ありません。

**戻り値**

引数で指定された列と、入力テーブルと同数の行が含まれるテーブル。

**例**

次の例は、`project` 演算子を使って実行できる複数の種類の操作を示しています。入力テーブル `T` には、`int` 型の列が 3 つあります (`A`、`B`、`C`)。

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### project-away 演算子

    T | project-away column1, column2, ...

指定された列を除外します。結果には、指定した列以外のすべての入力列が含まれます。

### range 演算子

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
* *Stop:* 出力で生成される最高値 (*step* によって最高値を超過する場合は、限度内の最高値)。
* *Step:* 2 つの連続する値の差異。 

この引数は、数値、日付、または期間の値である必要があります。テーブルの列を参照することはできません(入力テーブルに基づいて範囲を計算する場合は、[range *関数*](#range)を使います。その際は、[mvexpand 演算子](#mvexpand-operator)も組み合わせて使うことになると思われます)。

**戻り値**

*ColumnName* という 1 つの列を持つテーブルです。その列の値は、*Start*、*Start* + *Step* というように続き、*Stop* までとなります。

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

### reduce 演算子

    exceptions | reduce by outerMessage

類似したレコードのグループ化を試みます。この演算子は、グループごとに、そのグループを最も適切に表していると思われる `Pattern` と、そのグループ内のレコードの `Count` を出力します。


![](./media/app-insights-analytics-queries/reduce.png)

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


### render ディレクティブ

    T | render [ table | timechart  | barchart | piechart ]

render では、テーブルの表示方法をプレゼンテーション層に指示します。パイプの最後の要素である必要があります。特定のプレゼンテーション メソッドでクエリを保存でき、ディスプレイのコントロールの代わりに使用できる便利な方法です。

### restrict 句 

以下に示す演算子で使用可能なテーブル名のセットを指定します。次に例を示します。

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### sort 演算子 

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

### summarize 演算子

入力テーブルの内容を集計したテーブルを生成します。
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

数、平均要求期間、各国の都市のセットを示すテーブルです。出力には国ごとの行が含まれます。出力列には、数、平均期間、都市、国が示されます。他のすべての入力列は無視されます。


    T | summarize count() by price_range=bin(price, 10.0)

各間隔 ([0,10.0]、 \[10.0,20.0] など) で価格を持つ項目の数を示すテーブル。この例では、数の列と価格範囲の列があります。他のすべての入力列は無視されます。


**構文**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**引数**

* *Column:* 結果列の省略可能な名前。既定値は式から派生した名前です。[名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "\_" 文字を使用できます。キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Aggregation:* 引数として列名を持つ、`count()` や `avg()` などの集計関数を呼び出します。「[集計](#aggregations)」を参照してください。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。通常は、限られた値のセットが既に指定されている列名か、引数として数値列または時間列を持つ `bin()` になります。 

`bin()` を使用せずに数値式または時間式を指定した場合、Analytics は自動的に `1h` (時間の場合) または `1.0` (数値の場合) の間隔でそれを適用します。

*GroupExpression* を指定しないと、テーブル全体が単一の出力行にまとめられます。



**戻り値**

入力列は、`by` 式の同じ値を持つグループにまとめられます。次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。結果には、`by` 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます(一部の集計関数は複数の列を返します)。

結果には、`by` 値の個別の組み合わせと同数の列が含まれます。数値の範囲をまとめる場合は、`bin()` を使って範囲を不連続値に減らしてください。

**注**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用するか、`bin()` を数値列に適用する方がより効率的です。



### take 演算子

[limit](#limit-operator) のエイリアス


### top 演算子

    T | top 5 by Name desc

指定された列で並べ替えられた、先頭の *N* 個のレコードを返します。


**構文**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**引数**

* *NumberOfRows:* 返す *T* の行の数。
* *Sort\_expression:* 行の並べ替えに使用する式。通常は単なる列名です。複数の sort\_expression を指定できます。
* 選択が実際に範囲の "下限" からのものか "上限" からのものかを制御するために、`asc` または `desc` (既定値) が表示される場合があります。


**ヒント**

`top 5 by name` は表面的には `sort by name | take 5` と同等です。しかし、こちらの方が高速で、常に並べ替えられた結果を返します。`take` にはこのような保証がありません。


### union 演算子

     Table1 | union Table2, Table3

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。

**構文**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**引数**

* *Table1*, *Table2* ...
 *  テーブルの名前 (`requests` など)、または [let 句](#let-clause)で定義されたテーブルの名前
 *  クエリ式 (`(requests | where success=="True")` など)
 *  ワイルドカードで指定されたテーブルのセット。たとえば、`e*` は、"exceptions" テーブルと共に、前述の let 句で定義された、名前が "e" で始まるすべてのテーブルの和集合を形成します。
* `kind`: 
 * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
 * `outer` - 結果には、入力のいずれかに存在するすべての列が含まれます。入力行で定義されていなかったセルは `null` に設定されます。
* `withsource=`*ColumnName:* 指定されている場合は、各行の基になっているソース テーブルを示す値が格納された列 (名前は *ColumnName*) が出力に含まれます。

**戻り値**

すべての入力テーブルに存在する行と同数の行と、入力に存在する一意の列名と同数の列を含むテーブル。

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

### where 演算子

     T | where fruit=="apple"

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

**エイリアス** `filter`

**構文**

    T | where Predicate

**引数**

* *T*: フィルター処理するレコードが含まれる表形式の入力。
* *Predicate:* *T* の列に対する `boolean` [式](#boolean)。*T* 内の各行について評価されます。

**戻り値**

*Predicate* が `true` である *T* 内の行。

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* 列名と定数の**シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。`now()` と `ago()` は適切で、[`let` 句](#let-clause)を使って割り当てられるスカラー値も同様です)。

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



## 集計

集計とは、[summarize 演算](#summarize-operator)で作成されたグループの値を結合するための関数です。たとえば、次のクエリでは、dcount() が集計関数です。

    requests | summarize dcount(name) by success

### 任意 

    any(Expression)

ランダムにグループの 1 つの行を選択し、指定された式の値を返します。

これは、いくつかの列 ("エラー テキスト" 列など) に同じような値が多数含まれており、複合グループ キーの一意の値ごとに 1 回、その列をサンプリングする場合などに便利です。

**例**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
### argmin、argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

グループ内で最小化/最大化 (*ExprToMaximize*) する行を検索し、*ExprToReturn* の値を返します (`*` の場合は、行全体を返します)。

**ヒント**: パススルーされた列の名前は自動的に変更されます。正しい名前を使用していることを確認するには、別の演算子に結果をパイプする前に `take 5` を使用して結果を調べます。

**例**

要求名ごとに、最長要求が発生した時間を表示する場合は、次のように指定します。

    requests | summarize argmax(duration, timestamp) by name

タイムスタンプだけでなく、最長要求の詳細をすべて表示する場合は、次のように指定します。

    requests | summarize argmax(duration, *) by name


タイムスタンプと他のデータと共に、各メトリックの最小値を検索する場合は、次のように指定します。

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-aggregations/argmin.png)
 


### avg

    avg(Expression)

グループ全体の*式*の平均を計算します。

### buildschema

    buildschema(DynamicExpression)

*DynamicExpression* のすべての値を許可する最小スキーマを返します。

パラメーターの列型は `dynamic` (配列またはプロパティ バッグ) である必要があります。

**例**

    exceptions | summarize buildschema(details)

結果:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

数値インデックスを使用する必要がある場所をマークするために `indexer` が使用されていることに注目してください。このスキーマでは、以下のように、いくつかの有効なパスがあります (この例のインデックスが範囲内にあると仮定した場合)。

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**例**

入力列に次の 3 つの動的値があるとします。

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


結果のスキーマは次のようになります。

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

スキーマの内容は以下のとおりです。

* ルート オブジェクトは、4 つのプロパティ (x、y、z、t) を含むコンテナーです。
* "x" プロパティは "int" 型または "string" 型のいずれかになります。
* "y" プロパティは "double" 型、または "string" 型の "w" プロパティを含む別のコンテナーになります。
* ``indexer`` キーワードは、"z" と "t" が配列であることを示します。
* "z" 配列の各項目は int または string のいずれかです。
* "t" は string の配列です。
* すべてのプロパティは暗黙的に省略可能で、配列は空である可能性があります。

##### スキーマ モデル

返されるスキーマの構文は次のとおりです。

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

これらは TypeScript 型の注釈のサブセットと同等であり、動的値としてエンコードされます。TypeScript のスキーマ例を以下に示します。

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### count

    count([ Predicate ])

*Predicate* が `true` と評価された行の数を返します。*Predicate* が指定されていない場合は、グループ内のレコードの合計数を返します。

**パフォーマンス ヒント**: `where filter | summarize count()` の代わりに `summarize count(filter)` を使用します。

> [AZURE.NOTE] 要求、例外、またはその他の発生したイベントの数を検索する場合は、count() を使用しないでください。[サンプリング](app-insights-sampling.md)の実行中のデータ ポイントの数は、実際のイベントの数より少なくなります。代わりに `summarize sum(itemCount)...` を使用してください。itemCount プロパティには、保持されている各データ ポイントで表される元のイベントの数が反映されます。

### countif

    countif(Predicate)

*Predicate* が `true` と評価された行の数を返します。

**パフォーマンス ヒント**: `where filter | summarize count()` の代わりに `summarize countif(filter)` を使用します。

> [AZURE.NOTE] 要求、例外、またはその他の発生したイベントの数を検索する場合は、countif() を使用しないでください。[サンプリング](app-insights-sampling.md)の実行中のデータ ポイントの数は、実際のイベントの数より少なくなります。代わりに `summarize sum(itemCount)...` を使用してください。itemCount プロパティには、保持されている各データ ポイントで表される元のイベントの数が反映されます。

### dcount

    dcount( Expression [ ,  Accuracy ])

グループ内にある*式*の個別の値の概数を返します (個別の値のリストを表示するには、[`makeset`](#makeset) を使用します)。

*Accuracy* を指定した場合は、速度と精度のバランスが制御されます。

 * `0` = 精度は最も低くなりますが、計算速度は最高になります。
 * `1` = 既定値です。精度と計算時間のバランスをとります。エラー率は約 0.8% です。
 * `2` = 精度は最も高くなりますが、計算速度は最低になります。エラー率は約 0.4% です。

**例**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/dcount.png)

### makelist

    makelist(Expr [ ,  MaxListSize ] )

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。

* *MaxListSize* は、返される要素の最大数に対する省略可能な整数制限です (既定値は *128*)。

### makeset

    makeset(Expression [ , MaxSetSize ] )

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します (ヒント: 単に個別の値をカウントする場合は、[`dcount`](#dcount) を使用します)。
  
*  *MaxSetSize* は、返される要素の最大数に対する省略可能な整数制限です (既定値は *128*)。

**例**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/makeset.png)

逆の処理を実行する [`mvexpand` 演算子](#mvexpand-operator)も参照してください。


### max、min

    max(Expr)

*式*の最大値を計算します。
    
    min(Expr)

*式*の最小値を計算します。

**ヒント**: これで最小値または最大値 (最高価格または最低価格など) をそれぞれ単独で計算できます。ただし、行に他の列 (最低価格を提示するサプライヤーの名前など) が必要な場合は、[argmin または argmax](#argmin-argmax) を使用します。


<a name="percentile"></a> <a name="percentiles"></a>
### percentile、percentiles

    percentile(Expression, Percentile)

グループ内にある指定されたパーセンタイルの*式*の推定値を返します。精度は、パーセンタイル リージョンの人口密度によって異なります。
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

`percentile()` に似ていますが、複数のパーセンタイル値を計算します (各パーセンタイルを個別に計算するより高速)。

**例**


以下の場合、`duration` の値は、要求名ごとに計算され、サンプル セットの 95% より大きく、サンプル セットの 5% より小さくなります。

    request 
    | summarize percentile(duration, 95)
      by name

"by..." を省略すると、テーブル全体に対して計算が行われます。

異なる要求名のいくつかのパーセンタイルを同時に計算する場合は以下のようになります。

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-aggregations/percentiles.png)

結果には、/Events/Index 要求の場合、要求の 5% の応答が 2.44 秒未満、50% の応答が 3.52 秒、5% が 6.85 秒より遅いことが示されています。


複数の統計値を計算する場合は次のようになります。

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

##### パーセンタイル単位の推定エラー

パーセンタイル集計では、[T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf) を使用して概算値を算出できます。

重要なポイントをいくつか以下に示します。

* 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。[0..100] の範囲の両端にあるパーセンタイル 0 と 100 が分布値の正確な最小値および最大値になっているのが最適な精度です。精度はスケールの中央に向かって徐々に低下します。中央値が最低で、1% が上限となります。 
* エラー限度は、値ではなく、ランクで観測されます。たとえば、percentile(X, 50) の場合は Xm の値が返されます。推定では、X の値の 49% 以上 51% 以下が Xm 未満になることが保証されます。Xm と X の実際の中央値との差には、理論上の制限はありません。

### stdev

     stdev(Expr)

グループに対する*式*の標準偏差を返します。

### variance

    variance(Expr)

グループに対する*式*の分散を返します。

### sum

    sum(Expr)

グループに対する*式*の合計を返します。


## スカラー

[キャスト](#casts) | [比較](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

サポートされている型は次のとおりです。

| 型 | その他の名前 | 同等の .NET 型 |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`、`uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### キャスト

ある型から別の型にキャストできます。一般に、変換が理にかなっている場合はうまく動作します。

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### スカラーの比較

||
---|---
`<` |小さい
`<=`|小さいまたは等しい
`>` |大きい
`>=`|大きいまたは等しい
`<>`|等しくない
`!=`|等しくない 
`in`| 右オペランドは (動的) 配列で、左オペランドがその要素のいずれかに等しい
`!in`| 右オペランドは (動的) 配列で、左オペランドがその要素のいずれとも等しくない




### gettype

**戻り値**

単一の引数の基になるストレージ型を表す文字列。これは、`dynamic` のような値がある場合に、特に便利です。この場合、`gettype()` は値がどのようにエンコードされているかを示します。

**例**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**構文**

    hash(source [, mod])

**引数**

* *source*: ハッシュの計算の基となるソース スカラー。
* *mod*: ハッシュの結果に適用される剰余値。

**戻り値**

指定したスカラーの xxhash (long) 値。特定の mod 値が指定されている場合は、その剰余。

**例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

`iff()` 関数は、最初の引数 (述語) を評価し、述語が `true` であるか `false` であるかに応じて、2 番目または 3 番目の引数の値を返します。2 番目と 3 番目の引数は、同じ型である必要があります。

**構文**

    iff(predicate, ifTrue, ifFalse)


**引数**

* *predicate:* `boolean` 値に評価される式。
* *ifTrue:* *predicate* が `true` に評価された場合に、この式が評価され、その値が関数から返されます。
* *ifFalse:* *predicate* が `false` に評価された場合に、この式が評価され、その値が関数から返されます。

**戻り値**

この関数は、*predicate* が `true` に評価された場合に *ifTrue* の値を返し、それ以外の場合に *ifFalse* の値を返します。

**例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull、isnotnull、notnull

    isnull(parsejson("")) == true

1 つの引数を受け取り、null かどうかを判定します。

**構文**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**戻り値**

値が null かどうかに応じて、True または false を返します。


|○|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**例**

    T | where isnotnull(PossiblyNull) | count

上記の結果を得る方法はほかにもあることに注意してください。

    T | summarize count(PossiblyNull)




## Boolean 

### ブール型リテラル

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### ブール演算子

	and 
    or 

    

## 数値

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### 数値リテラル

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### 算術演算子

|| |
|---|-------------|
| + | [追加] のいずれかを |
| - | 減算 | 
| * | 乗算 | 
| / | 除算 | 
| % | 剰余 | 
|| 
|`<` |小さい 
|`<=`|小さいまたは等しい 
|`>` |大きい 
|`>=`|大きいまたは等しい 
|`<>`|等しくない 
|`!=`|等しくない




### bin

値を切り捨てて、指定された bin サイズの倍数である整数にします。[`summarize by`](#summarize-operator) クエリでよく使用されます。値が分散している場合に、特定の値ごとの小さなセットにグループ化されます。

エイリアス `floor`。

**構文**

     bin(value, roundTo)

**引数**

* *value:* 数値、日付、または期間。 
* *roundTo:* "bin サイズ"。*value* を除算する数値、日付、または期間。 

**戻り値**

*value* 未満で、*roundTo* の最も近い倍数。
 
    (toint((value/roundTo)-0.5)) * roundTo

**例**

式 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


次の式は、バケットのサイズを 1 秒として、各期間のヒストグラムを計算します。

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```
### exp

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v



### floor

[`bin()`](#bin) のエイリアス。


### log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` は 0 を超える実数である必要があります。それ以外の場合、null が返されます。

### rand

乱数ジェネレーター。

* `rand()` - 0.0 から 1.0 の範囲の実数
* `rand(n)` - 0 から n-1 の範囲の整数




### sqrt

平方根関数。

**構文**

    sqrt(x)

**引数**

* *x:* 0 以上の実数。

**戻り値**

* `sqrt(x) * sqrt(x) == x` のような正の数値。
* 引数が負であるか、`real` 値に変換できない場合は `null`。 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## 日付と時刻


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### 日付と時刻のリテラル

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|時刻は常に UTC 形式です。日付を省略すると、今日の時刻になります。
`now()`|現在の時刻。
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 日
`1.5h`|1\.5 時間 
`30m`|30 分
`10s`|10 秒
`0.1s`|0\.1 秒
`100ms`| 100 ミリ秒
`10microsecond`|
`1tick`|100 ナノ秒
`time("15 seconds")`|
`time("2")`| 2 日
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### 日付と時刻の式

式 |結果
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |小さい
`<=`|小さいまたは等しい
`>` |大きい
`>=`|大きいまたは等しい
`<>`|等しくない
`!=`|等しくない 




### ago

現在の UTC 時刻から指定された期間を減算します。`now()` と同様、この関数はステートメント内で複数回使用することができます。また、参照される UTC 時刻はすべてのインスタンスで同じになります。

**構文**

    ago(a_timespan)

**引数**

* *a\_timespan*: 現在の UTC 時刻 (`now()`) から減算する期間。

**戻り値**

    now() - a_timespan

**例**

過去 1 時間以内のタイムスタンプを持つすべての行は、次のようになります。

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

前の日曜日からの日数を、`timespan` として示す整数。

**構文**

    dayofweek(a_date)

**引数**

* `a_date`: `datetime`。

**戻り値**

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に丸められます。

**例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

datetime から月 (1 ～ 12) を取得します。

**例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

datetime から年を取得します。

**例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

現在の UTC 時刻。オプションで、オフセットの期間を指定できます。この関数はステートメント内で複数回使用することができます。また、参照される時刻はすべてのインスタンスで同じになります。

**構文**

    now([offset])

**引数**

* *offset:* 現在の UTC 時刻に加算される `timespan`。既定値: 0。

**戻り値**

`datetime` 型の現在の UTC 時刻。

    now() + offset

**例**

述語によって特定されるイベント以降の期間を判断します。

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

該当月の最初の日付。

### startofyear

    startofyear(date)

該当年の最初の日付。


### todatetime

エイリアス `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

エイリアス `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### 文字列リテラル

規則は JavaScript の場合と同様です。

文字列は、単一引用符または二重引用符で囲むことがあります。

バックスラッシュ (``) は、`\t` (タブ)、`\n` (改行)、文字列を囲む引用符などの文字をエスケープするために使用されます。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### 難読化された文字列リテラル

難読化された文字列リテラルとは、文字列の出力時 (たとえば、トレース時) に Analytics によってわかりにくくされる文字列のことです。難読化プロセスでは、難読化されるすべての文字が開始 (`*`) 文字に置き換えられます。

難読化された文字列リテラルを形成するには、前に `h` または "H" を付加します。次に例を示します。

```
h'hello'
h@'world' 
h"hello"
```

### 文字列の比較

演算子|説明|大文字と小文字の区別|実際の例
---|---|---|---
`==`|等しい |あり| `"aBc" == "aBc"`
`<>`|等しくない|あり| `"abc" <> "ABC"`
`=~`|等しい |いいえ| `"abc" =~ "ABC"`
`!~`|等しくない |いいえ| `"aBc" !~ "xyz"`
`has`|右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる|いいえ| `"North America" has "america"`
`!has`|RHS が LHS に完全な単語として含まれない|なし|`"North America" !has "amer"` 
`contains` | RHS が LHS のサブシーケンスとして出現する|いいえ| `"FabriKam" contains "BRik"`
`!contains`| RHS が LHS 内に出現しない|いいえ| `"Fabrikam" !contains "xyz"`
`containscs` | RHS が LHS のサブシーケンスとして出現する|あり| `"FabriKam" contains "Kam"`
`!containscs`| RHS が LHS 内に出現しない|あり| `"Fabrikam" !contains "Kam"`
`startswith`|RHS が LHS の先頭のサブシーケンスである|いいえ|`"Fabrikam" startswith "fab"`
`matches regex`|LHS には RHS との一致が含まれている|あり| `"Fabrikam" matches regex "b.*k"`


語彙全体があるかどうかをテストしている場合は、`has` または `in` を使用します。語彙全体とは、非英数字またはフィールドの先頭か末尾によって囲まれた、記号または英数字から成る単語を意味します。`has` は、`contains` または `startswith` より速く動作します。以下のクエリでは、最初の方が処理速度は速くなります。

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

文字列内の部分文字列の出現回数をカウントします。プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**構文**

    countof(text, search [, kind])

**引数**

* *text:* 文字列。
* *search:* *テキスト*内で一致させるプレーン文字列または正規表現。
* *kind:* `"normal"|"regex"`。既定では `normal`。 

**戻り値**

コンテナー内で検索文字列が一致する回数。プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (2 ではありません)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

テキスト文字列から[正規表現](#regular-expressions)との一致を取得します。必要に応じて、抽出された部分文字列を、指定された型に変換することもできます。

**構文**

    extract(regex, captureGroup, text [, typeLiteral])

**引数**

* *regex:* [正規表現](#regular-expressions)。
* *captureGroup:* 抽出するキャプチャ グループを示す、正の `int` 定数。0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
* *text:* 検索する `string`。
* *typeLiteral:* オプションの型のリテラル (例: `typeof(long)`)。指定した場合、抽出された部分文字列はこの型に変換されます。 

**戻り値**

*regex* が *text* 内で一致を見つけた場合: 指定されたキャプチャ グループ *captureGroup* に対応する部分文字列。オプションで、*typeLiteral* に変換できます。

一致がないか、型変換が失敗した場合: `null`。

**例**

サンプル文字列 `Trace` で `Duration` の定義が検索されます。一致は `real` に変換され、時間定数 (`1s`) で乗算されて、`Duration` は `timespan` 型になります。この例では、123.45 秒と等しくなります。

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

次の例は、`substring(Text, 2, 4)` に相当します。

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty、isnotempty、notempty

    isempty("") == true

引数が空の文字列または null である場合は True です。[isnull](#isnull) も参照してください。


**構文**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**戻り値**

引数が空の文字列または null であるかどうかを示します。

|○|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**例**


    T | where isempty(fieldName) | count




### replace

正規表現のすべての一致を別の文字列に置き換えます。

**構文**

    replace(regex, rewrite, text)

**引数**

* *regex:* *text* の検索に使用する[正規表現](https://github.com/google/re2/wiki/Syntax)。複数のキャプチャ グループをかっこ内に含めることができます。 
* *rewrite:* *matchingRegex* によるすべての一致を置き換える正規表現。完全一致を参照する場合は `\0`、最初のキャプチャ グループの場合は `\1`、後続のキャプチャ グループの場合は `\2` などを使用します。
* *text:* 文字列。

**戻り値**

*regex* のすべての一致を *rewrite* の評価で置き換えた後の *text*。一致が重なり合うことはありません。

**例**

次のステートメントを実行します。

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

結果は次のとおりです。

| ○ | str | replaced|
|---|---|---|
| 1 | Number is 1.000000 | Number was: 1.000000|
| 2 | Number is 2.000000 | Number was: 2.000000|
| 3 | Number is 3.000000 | Number was: 3.000000|
| 4 | Number is 4.000000 | Number was: 4.000000|
| 5 | Number is 5.000000 | Number was: 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

指定された区切り記号に従って、指定された文字列を分割し、部分文字列が含まれている文字列配列を返します。必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

**構文**

    split(source, delimiter [, requestedIndex])

**引数**

* *source*: 指定された区切り記号に従って分割されるソース文字列。
* *delimiter*: ソース文字列を分割するために使用される区切り記号。
* *requestedIndex*: 0 から始まるインデックス `int` (オプション)。指定した場合、返される文字列配列には、要求した部分文字列が含まれます (存在する場合)。 

**戻り値**

指定された区切り記号によって分割された、指定されたソース文字列の部分文字列が含まれている文字列配列。

**例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

1 ～ 16 個の引数を連結します。引数は文字列である必要があります。

### strlen

    strlen("hello") == 5

文字列の長さ。

### substring

    substring("abcdefg", 1, 2) == "bc"

指定されたソース文字列の指定されたインデックス位置から部分文字列を抽出します。必要に応じて、要求する部分文字列の長さを指定できます。

**構文**

    substring(source, startingIndex [, length])

**引数**

* *source:* 部分文字列の取得元となるソース文字列。
* *startingIndex:* 要求する部分文字列の、0 から始まる開始文字位置。
* *length:* 要求する部分文字列の文字数を指定するために使用できるオプションのパラメーター。 

**戻り値**

指定した文字列の部分文字列。部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

**例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

文字列を小文字に変換します。

### toupper

    toupper("hello") == "HELLO"

文字列を大文字に変換します。



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## 配列、オブジェクト、および動的

[リテラル](#dynamic-literals) | [キャスト](#casting-dynamic-objects) | [演算子](#operators) | [let 句](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Application Insights の例外に対するクエリの結果を次に示します。`details` の値は配列です。

![](./media/app-insights-analytics-scalars/310.png)

**インデックス:** JavaScript と同様に、配列やオブジェクトのインデックスを作成できます。

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* ただし、`arraylength` やその他の Analytics 関数 (".length" 以外) を使用してください。

**キャスト:** 場合によっては、オブジェクトから抽出する要素をキャストする必要があります。これは、オブジェクトの型が一様ではないためです。たとえば、`summarize...to` には次のように特定の型が必要です。

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**リテラル:** 明示的な配列またはプロパティ バッグ オブジェクトを作成するには、次のように、JSON 文字列として記述し、キャストします。

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** オブジェクトのプロパティを個々の行に分解するには、次のように、mvexpand を使用します。

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-scalars/410.png)


**treepath:** 複合オブジェクト内のすべてのパスを検索するには、次のようにします。

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-scalars/420.png)

**buildschema:** テーブル内の式のすべての値を受け入れる最小限のスキーマを見つけるには、次のようにします。

    exceptions | summarize buildschema(details)

結果は次のとおりです。

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

数値インデックスを使用する必要がある場所をマークするために `indexer` が使用されていることに注目してください。このスキーマでは、以下のように、いくつかの有効なパスがあります (この例のインデックスが範囲内にあると仮定した場合)。

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### 配列とオブジェクトのリテラル

動的リテラルを作成するには、次のように、JSON 文字列引数を指定した `parsejson` (エイリアスは `todynamic`) を使用します。

* `parsejson('[43, 21, 65]')` - 数値の配列
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - 数値を含む、動的な型の単一の値
* `parsejson('"21"')` - 文字列を含む、動的な型の単一の値

JavaScript とは異なり、JSON では文字列の前後で二重引用符 (`"`) の使用が必須であることに注意してください。そのため、一般的に、JSON 形式でエンコードされた文字列リテラルを単一引用符 (`'`) で囲む方がより簡単です。

この例では、動的な値を作成した後、そのフィールドを使用します。

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


## 動的オブジェクトの関数

|||
|---|---|
| *value* `in` *array*| *array* に *value* と等しい要素がある場合は true。<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| *array* に *value* と等しい要素がない場合は true。
|[`arraylength(`array`)`](#arraylength)| 配列でない場合は null。
|[`extractjson(`path,object`)`](#extractjson)|オブジェクトに移動するためのパスを使用します。
|[`parsejson(`source`)`](#parsejson)| JSON 文字列を動的オブジェクトに変換します。
|[`range(`from,to,step`)`](#range)| 値の配列。
|[`mvexpand` listColumn](#mvexpand-operator) | リスト内の指定されたセルの各値に対して、行を複製します。
|[`summarize buildschema(`column`)`](#buildschema) |列の内容から型スキーマを推測します。
|[`summarize makelist(`column`)` ](#makelist)| 行のグループをフラット化し、列の値を配列に格納します。
|[`summarize makeset(`column`)`](#makeset) | 行のグループをフラット化し、列の値を重複しないように配列に格納します。

### let 句の動的オブジェクト


[let 句](#let-clause)には動的値が文字列として格納されるため、以下の 2 つの句は同等で、どちらも使用前に `parsejson` (または `todynamic`) が必要です。

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

動的配列内の要素数。

**構文**

    arraylength(array)

**引数**

* *array:* `dynamic` 値。

**戻り値**

*array* 内の要素数。*array* が配列ではない場合は `null`。

**例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

パス式を使用している JSON テキストから、指定された要素を取得します。必要に応じて、抽出した文字列を特定の型に変換することもできます。


**構文**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**戻り値**

この関数は、有効な JSON 文字列が含まれている dataSource への JsonPath クエリを実行します。オプションで、その値を 3 番目の引数に基づいて他の型に変換することもできます。



**例**

次のように、角かっこ ([]) 表記とドット表記は同等です。

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**パフォーマンスに関するヒント**

* `extractjson()` を使用する前に、where 句を適用します。
* 代わりに、[extract](#extract) による正規表現の一致を使用することを検討してください。こちらの方が実行速度が非常に速く、JSON がテンプレートから生成される場合に効率的です。
* JSON から複数の値を抽出する必要がある場合は、`parsejson()` を使用してください。
* 列の型が動的になるように宣言することによって、取り込み時に JSON が解析されるようにすることを検討してください。

### JSON パス式

|||
|---|---|
|`$`|ルート オブジェクト|
|`@`|現在のオブジェクト|
|`[0]`|配列インデックス|
|`.` または `[0]` | 子|

*(現在、ワイルドカード、再帰、共用体、およびスライスは実装していません。)*




### parsejson

`string` を [JSON 値](http://json.org/)として解釈し、値を `dynamic` として返します。JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、`extractjson()` を使用する方が適しています。

**構文**

    parsejson(json)

**引数**

* *json:* JSON ドキュメント。

**戻り値**

*json* によって指定された、`dynamic` 型のオブジェクト。

**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

その後、次のフラグメントがオブジェクトの `duration` スロットの値を取得し、そこから 2 つのスロット `duration.value` および `duration.min` (それぞれ `118.0` と `110.0`) を取得します。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

`range()` 関数 (`range` 演算子と混同しないようにしてください) は、一連の等間隔の値を保持する動的配列を生成します。

**構文**

    range(start, stop, step)

**引数**

* *start:* 結果として生成される配列内の最初の要素の値。 
* *stop:* 結果として生成される配列内の最後の要素の値。または、生成される配列内の最後の要素より大きく、*start* からの *step* の倍数である整数に含まれる最小値。
* *step:* 配列の連続する 2 つの要素の差異。

**例**

次の例は、`[1, 4, 7]` を返します。

```AIQL
range(1, 8, 3)
```

次の例は、2015 年のすべての日を保持する配列を返します。

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

文字列を動的な値に変換します。

### treepath

    treepath(dynamic_object)

動的オブジェクトのリーフを識別するパス式をすべて列挙します。

**戻り値**

パス式の配列。

**例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

"[0]" は配列の存在を示しますが、特定のパスで使用されるインデックスを指定しないことに注意してください。

## 名前

名前の最大長は 1024 文字です。大文字と小文字が区別され、アルファベット、数字、およびアンダースコア (`_`) を含めることができます。

名前として他の文字を含める場合やキーワードを使用する場合は、その名前を [' ... '] または [" ... "] で囲みます。次に例を示します。

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|['path\\file\\n'x''] | 文字をエスケープする場合は \\ を使用します。|
|["d-e.=/f#\\n"] | |
|[@'path\\file'] | エスケープなし - \\ はリテラルです。|
|[@"\\now & then"] | |
|[where] | 名前として言語キーワードを使用します。|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0427_2016-->