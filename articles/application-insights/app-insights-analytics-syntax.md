<properties 
	pageTitle="Application Insights の Analytcis のステートメント" 
	description="Application Insights の強力な検索ツールである Analytics のクエリ、式、let ステートメント。" 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>

 
# Application Insights の Analytcis ステートメント

[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、Analytics のクエリ言語について説明します。

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## データ モデル

AIQL:

* *データベース*には、0 個以上の名前の付いた*テーブル*が含まれます。
* *テーブル*には次のものが含まれます。
 * 1 つ以上の名前付きの*列*。 各列には*型*があります。
 * 1 つ以上の*行*。
* 各行には 1 つ以上の*セル*があり、そのテーブルの列ごとに 1 つ存在します。
* セルには、その列の型の値、または `null` を含めることができます。


## エンティティ名

すべての列、テーブルまたはデータベースには、そのコンテナー内で一意の名前があります。

エンティティはその名前 (コンテキストが明確な場合) またはそのコンテナーで修飾された名前で参照します。たとえば、`MyColumn` は `MyTable.MyColumn` として参照することも、`MyDatabase.MyTable.MyColumn` として参照することもできます。

名前の最大長は 1024 文字です。大文字と小文字が区別され、文字、数字およびアンダースコア (`_`) を含めることができます。

さらに、名前を引用符で囲み、他の文字を含めることができます。次に例を示します。

|||
|---|---|
|`['path\\file\n\'x\'']` | 文字をエスケープする場合は `` を使用します。|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| エスケープなし - `` はリテラルです。|
|`[@"\now & then"]` | |
|`[where]` | 名前として言語キーワードを使用します。|

名前をデータベース名で修飾することもできます (「[Cross-databse queries (クロスデータベース クエリ)](#cross-database-queries)」を参照)。

```
database("MyDb").Table
```


## ステートメント

CSL のステートメントには次の 4 つの種類があります。

### データ クエリ
  
Analytics に格納されるデータに関する読み取り専用の要求です。次に例を示します。

* `event` - "event" という名前のテーブル内のすべてのレコードを返します。
* `event | count` - "event" 内のレコードの数を返します。

    
## let ステートメント

#### 概要
1 つ以上の let ステートメントでデータ クエリ ステートメントにプレフィックスを付けることができます。これにより、式に名前 (有効なエンティティ名) をバインドすることができます。その後、let ステートメントで定義された名前を、後続のデータ クエリ ステートメントで複数回使用することができます。

let ステートメントでは、
1. "スカラー
2. 表形式データ" のような式に名前をバインドできます。 

表形式データへのバインド時に、let ステートメントを "view" に昇格できます。これにより、ステートメントは "union *" 操作に参加できるようになります。

#### 構文

`let <name> = <expression>`

<expression> は、次のように 0 個、1 個、または複数の引数を使用してラムダ宣言にすることができます。

`() {...}`

`(<arg0>:<type0>, ...) {...}`

例: 次の例では、スカラー リテラル '1' に名前 'x' をバインドします。

```
let x=1;
range y from x to x step x
```

次の例では、Window という名前に 1 時間分のログをバインドしてから、Window に対して自己結合を実行します。


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

次の 2 つの例では、let ステートメントでラムダ式を使用します。


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

'view' キーワードを使用して、let ステートメントを union * 操作に参加する view に昇格させます。


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## restrict ステートメント

#### 概要
次のステートメントを使用して、クエリ アクセスをステートメント リストの範囲内に制限することができます。


```
restrict access to (<entity1, entity2, ...>);
```
 
このステートメントでは、restrict ステートメントで許可されているエンティティ (let ステートメントまたは表形式エンティティ) にのみアクセスが許可されます。
 
次に例を示します。

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## クエリの構成

*クエリ*は以下のパターンに従います。

- *ソース* | *フィルター* | *フィルター* ...

次に例を示します。


```
Logs | where Timestamp > ago(1d) | count
```

* *ソース*はデータベース テーブルの名前、または以前に定義された結果テーブルの名前です。
* 各*フィルター*は、適切なパラメーターを使用して、`where` または `count` などのクエリ演算子を呼び出します。パラメーターとして、*スカラー式*、入れ子になった*データ クエリ*、または列名を指定することができます。

次に例を示します。


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## let ステートメント

let ステートメントを使用することで、関数 (つまり、0 個以上の引数を使用して値を返す名前付き式) を定義できます。また、ステートメントで関数を "呼び出す" ことができます。

### 名前付きの値

スカラー値を表す名前は次のように定義します。


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

クエリ結果を表す名前は次のように定義します。


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

これは次のような自己結合の場合に特に便利です。


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## 名前付き関数

スカラー結果を返す関数は次のように定義します。


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

クエリ結果を返す関数は次のように定義します。


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

名前付き関数に対するパラメーターはスカラーである必要があります。





[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016------>