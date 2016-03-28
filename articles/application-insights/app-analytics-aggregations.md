<properties 
	pageTitle="Application Insights Analytics の summarize ステートメントと集計関数" 
	description="Application Insights Analytics の集計関数と summarize ステートメントのリファレンス、Application Insights の強力な検索ツール。" 
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
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Application Insights Analytics での集計

[Application Insights Analytics](app-analytics.md) は、[Application Insights](app-insights-overview.md) テレメトリ用の強力な検索エンジンです。ここでは、Application Insights Analytics クエリ言語である AIQL について説明します。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]



## summarize 演算子

入力テーブルの内容を集計したテーブルを生成します。

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-analytics-aggregations/01.png)

* 入力レコードはグループに収集されるため、各グループの `by` フィールドには特定の組み合わせの値が示されます。
* 集計式は各グループのメンバーに対して評価されます。
* 'by' 式の値の組み合わせごとに出力行があります。 
* 集計式および 'by' 式ごとに出力列があります。他のすべての入力列は削除されます。

### 構文

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**引数**

* *Column:* 結果列の省略可能な名前。既定値は式から派生した名前です。
* *Aggregation:* 引数として列名を持つ、`count()` や `avg()` などの集計関数を呼び出します。以下の集計関数のリストを参照してください。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。通常は、限られた値のセットが既に指定されている列名か、引数として数値列または時間列を持つ `bin()` になります。 

`bin()` を使用せずに数値式または時間式を指定した場合、AI Analytics は自動的に `1h` (時間の場合) または `1.0` (数値の場合) の間隔でそれを適用します。

*GroupExpression* を指定しないと、テーブル全体が単一の出力行にまとめられます。

`by` 句では、動的型ではなく、単純型を使用する必要があります。たとえば、以下の `tostring` キャストは必須です。

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### 不連続値を持つ列による集計

ここでは、さまざまな HTTP 要求への平均応答時間を示すクエリについて説明します。各要求にはさまざまな応答コードが含まれています。

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-analytics-aggregations/03.png)


* 集計式 (count や avg など) は、この記事に記載されている集計関数から形成される必要があります。引数として、任意のスカラー関数を指定できます。
* グループ化式 ('by' の後) として任意のスカラー式を指定できますが、フィールド名のみであれば、より効率的です。

### 数値列による集計

数値や時間などの連続スカラーでグループ化する場合は、`bin` (つまり、`floor`) 関数を使用して連続範囲をビン分割する必要があります。

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-analytics-aggregations/04.png)

(要求期間フィールドにはミリ秒単位の数が示されます。)
 
## ヒント

* `summarize` の前に、不要であることがわかっている行をすべて削除する場合は `where` を使用します。
 * Null 値を削除します。グループ内に Null 値が 1 つあると、集計結果は Null になります。 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* 集計式とグループ化式の両方に任意の式を指定できますが、単純なフィールド名を使用するか、`bin()` を数値フィールドに適用する方がより効率的です。





## 例

Activities テーブルで、すべてのレコードの最小タイムスタンプと最大タイムスタンプを見つけます。group by 句はないため、以下のように出力には行が 1 つしかありません。

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`



#### セッションの期間

イベント ログ内のセッションには複数のイベントがあります。各セッションの開始と終了を見つけるには、以下のように各セッションの最初と最後のイベントを検索します。

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

`extend` 操作で期間列を追加します。その場合、`bin` (つまり、`floor`) を使用して、最も近い秒数に丸めます。

![](./media/app-analytics-aggregations/minmax.png)

#### 例: 平均期間

次に、さまざまな都市のクライアントの平均セッション期間を検索します。

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

`client_City` 列を `by` 句に追加したため、最初の集計操作はパススルーします。1 つのクライアント セッションのすべてのイベントは同じ都市で発生したと仮定した場合、集計の出力数には加算されません。


![](./media/app-analytics-aggregations/durationcity.png)


#### 例: 

各クライアント都市の最もビジーな時間帯を検索します。この "最もビジー" とは、平均的な日に最大数のセッションが開始された時間帯を意味します。

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## 集計

## 任意 

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
## argmin、argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

グループ内で最小化/最大化 (*ExprToMaximize*) する行を検索し、*ExprToReturn* の値を返します (`*` の場合は、行全体を返す)。

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


![](./media/app-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

グループ全体の*式*の平均を計算します。

## buildschema

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

#### スキーマ モデル

返されるスキーマの構文は次のとおりです。

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

これらは TypeScript 型の注釈のサブセットと同等であり、AIQL 動的値としてエンコードされます。TypeScript のスキーマ例を以下に示します。

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


## count

    count([ Predicate ])

*Predicate* が `true` と評価された行の数を返します。*Predicate* が指定されていない場合は、グループ内のレコードの合計数を返します。

**パフォーマンス ヒント**: `where filter | summarize count()` の代わりに `summarize count(filter)` を使用します。
   

## dcount

    dcount( Expression [ ,  Accuracy ])

グループ内にある*式*の個別の値の概数を返します。(個別の値のリストを表示するには、[`makeset`](#makeset) を使用します。)

*精度*を指定した場合は、速度と精度のバランスが制御されます。

 * `0` = 精度は最も低くなりますが、計算速度は最高になります。
 * `1` = 既定値です。精度と計算時間のバランスをとります。エラー率は約 0.8% です。
 * `2` = 精度は最も高くなりますが、計算速度は最低になります。エラー率は約 0.4% です。

**例**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。

* *MaxListSize* は、返される要素の最大数に対する省略可能な整数制限です (既定値は *128*)。

## makeset

    makeset(Expression [ , MaxSetSize ] )

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します。(ヒント: 単に個別の値をカウントする場合は、[`dcount`](#dcount) を使用します。)
  
*  *MaxSetSize* は、返される要素の最大数に対する省略可能な整数制限です (既定値は *128*)。

**例**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/makeset.png)

逆関数に関する「[`mvexpand` operator (mvexpand 演算子)](app-analytics-queries.md#mvexpand-operator)」も参照してください。


## max、min

    max(Expr)

*式*の最大値を計算します。
    
    min(Expr)

*式*の最小値を計算します。

**ヒント**: これで最大値または最小値 (最高価格または最低価格など) をそれぞれ単独で計算できます。ただし、行に他の列 (最低価格を提示するサプライヤーの名前など) が必要な場合は、[argmin または argmax](#argmin-argmax) を使用します。


<a name="percentile"></a> <a name="percentiles"></a>
## percentile、percentiles

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

![](./media/app-analytics-aggregations/percentiles.png)

結果には、/Events/Index 要求の場合、要求の 5% の応答が 2.44 秒未満、50% の応答が 3.52 秒、5% が 6.85 秒より遅いことが示されています。


複数の統計値を計算する場合は次のようになります。

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### パーセンタイル単位の推定エラー

パーセンタイル集計では、[t-digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf) を使用して近似値を取得します。

重要なポイントをいくつか以下に示します。

* 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。[0..100] の範囲の両端にあるパーセンタイル 0 と 100 が分布値の正確な最小値および最大値になっているのが最適な精度です。精度はスケールの中央に向かって徐々に低下します。中央値が最低で、1% が上限となります。 
* エラー限度は、値ではなく、ランクで観測されます。たとえば、percentile(X, 50) の場合は Xm の値が返されます。推定では、X の値の 49% 以上 51% 以下が Xm 未満になることが保証されます。Xm と X の実際の中央値との差には、理論上の制限はありません。

## stdev

     stdev(Expr)

グループに対する*式*の標準偏差を返します。

## variance

    variance(Expr)

グループに対する*式*の分散を返します。

## sum

    sum(Expr)

グループに対する*式*の合計を返します。




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->