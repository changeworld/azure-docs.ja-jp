<properties 
	pageTitle="Application Insights の Analytics のツアー" 
	description="Analytics のすべてのメイン クエリの短いサンプル、Application Insights の強力な検索ツール。" 
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


 
# Application Insights の Analytics について


[Analytics](app-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、Analytics のクエリ言語について説明します。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
作業を開始するための基本的なクエリをいくつか見てみましょう。

## Application Insights のデータに接続する

以下のように、Application Insights でアプリの[概要ブレード](app-insights-dashboards.md)から Analytics を開きます。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-analytics/001.png)

## 行数の[カウント](app-analytics-aggregations.md#count)

パフォーマンス カウンターなどのメトリックは、メトリックというテーブルに格納されます。各行は、アプリの Application Insights SDK から受信したテレメトリ データ ポイントです。テーブルの大きさを説明するために、ここでは単に行数をカウントする演算子にその内容をパイプします。

```AIQL
	
    requests | count
```

> [AZURE.NOTE] ステートメントの任意の場所にカーソルを置いてから、[検索] をクリックします。複数の行にステートメントを分割できますが、1 つのステートメントに複数の空白行を配置しないでください。ウィンドウ内に複数のクエリが収まるようにするには、クエリを空白行で区切ります。

結果は次のとおりです。


![](./media/app-analytics-tour/010.png)

	
[`Count`](app-analytics-aggregations.md#count) は数ある[クエリ演算子](app-analytics-queries.md)の 1 つです。複数のステージで、データのパイプ処理、フィルター処理、リシェイプおよび結合を行うために配置できます。
	
## [take](app-analytics-aggregations.md#take): n 個の行を表示する


たとえば、以下のように 5 個の行を表示するとします。

```AIQL

	requests | take 5
```

結果は次のようになります。

![results](./media/app-analytics-tour/020.png)

以下のように列を選択し、その位置を調整します。

![結果の右上にある列の選択をクリックする](./media/app-analytics-tour/030.png)

詳細を表示する場合は、以下のように該当する項目を展開します。
 
![テーブルを選択し、列の構成を使用する](./media/app-analytics-tour/040.png)


## [top](app-analytics-aggregations.md#top) および [sort](app-analytics-aggregations.md#sort)

`take` は結果の簡単なサンプルを取得するのに便利ですが、テーブルの行は任意の順序で表示されます。指定した順序で表示するには、(サンプルに対して) `top` を使用するか、(テーブル全体に対して) `sort` を使用します。

特定の列で並べ替えた最初の n 個の行を表示する場合は、以下のように指定します。

```AIQL

	requests | top 10 by timestamp desc 
```

* *構文:* ほとんどの演算子に、`by` などのキーワード パラメーターがあります。
* `desc` は降順、`asc` は昇順を意味します。

![](./media/app-analytics-tour/260.png)

`top...` は、`sort ... | take...` と指定するよりも効率的な方法です。記述例を以下に示します。

```AIQL

	requests | sort by timestamp desc | take 10
```

結果は同じですが、実行時間が少し長くなります。(`sort` のエイリアスである `order` を記述することもできます。)

テーブル ビューの列ヘッダーを使用して、画面上の結果を並べ替えることもできます。ただし、言うまでもなく、`take` または `top` を使用してテーブルの一部のみを取得した場合、並べ替えるのは取得したレコードのみとなります。


## [project](app-analytics-aggregations.md#project): 列を選択、名前変更および計算する

必要な列だけを選択する場合は、次のように [`project`](app-analytics-aggregations.md#project) を使用します。

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


また、以下のように列の名前を変更し、新しい列を定義することもできます。

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

スカラー式の場合:

* `%` はよくあるモジュロ演算子です。 
* `1d` (数字の 1 の後に 'd' が付加されている) は 1 日を意味する期間リテラルです。期間リテラルにはこの他にも `12h`、`30m`、`10s`、`0.01s` などがあります。
* `floor` (エイリアスは `bin`) は、指定した基準値の最も近い倍数に値を切り捨てます。したがって、`floor(aTime, 1s)` の場合、最も近い秒数に時間を切り捨てます。

[式](app-analytics-scalars.md)には、よくある演算子 (`+` や `-` など) をすべて含めることができ、さまざまな便利な関数があります。

## [extend](app-analytics-aggregations.md#extend): 列を計算する

単に列を既存のものに追加する場合は、以下のように [`extend`](app-analytics-aggregations.md#extend) を使用します。

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

既存の列をすべて保持する場合に、[`extend`](app-analytics-aggregations.md#extend) を使用すると [`project`](app-analytics-aggregations.md#project) よりも詳細度が低くなります。

## [summarize](app-analytics-aggregations.md#summarize): 行のグループをまとめる

テーブルのサンプルを見ると、フィールドでさまざまなテレメトリ データがレポートされていることがわかります。たとえば、`exception | take 20` の場合、`outerExceptionType` というフィールドで例外メッセージがレポートされていることがすぐにわかります。

ただし、ここではインスタンスを 1 つずつ見ていくのではなく、例外がどの程度レポートされるかをタイプごとに見ていくことにします。

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` は、`by` 句に指定されたフィールドで同じ値を持つ行をまとめます。その場合、グループごとに単一の結果行が生成されます。したがって、この例の場合、例外タイプごとに 1 つの行が存在します。集計関数 `count()` では各グループ内の行がカウントされ、結果に列が示されます。


さまざまな[集計関数](app-analytics-aggregations.md)があり、そのいくつかを 1 つの summarize 演算子で使用して、複数の計算列を生成することができます。

たとえば、これらの例外が発生する HTTP 要求をリストするとします。ここでも例外テーブルのサンプルを見ると、`operation_Name` という名前の列で HTTP 要求パスがレポートされていることがわかります。

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

集計関数 `makeset()` では、各グループ内の指定されたすべての値のセットが作成されます。したがって、この例では、各例外を発生させる操作は 1 つだけです。


summarize の結果には以下のものが含まれます。

* `by` で指定された各列
* 集計式ごとに 1 つの列
* `by` 値の組み合わせごとに 1 つの行


## スカラー値による集計


by 句ではスカラー (数、時間、または間隔) 値を使用することができます。ただし、数値の場合、通常は連続する範囲を入力します。データ ポイントをグループ化するには、不連続値のビンに割り当てる必要があります。`bin` 関数は以下のような場合に便利です。

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` は、すべてのタイムスタンプを 1 日間隔に減らします。これは `floor` のエイリアスであり、ほとんどの言語でなじみのある関数です。指定したモジュールの最も近い倍数にすべての値を減らすだけなので、`summarize` では行を適切なサイズのグループに割り当てることができます。(これを指定しないと、ほんの一瞬ごとに結果行が生成され、データがまったくまとまらなくなります。)

ここでは、テーブル ビューよりわかりやすくするために、以下のように縦棒オプションを使用してグラフビューで結果を見てみることにします。

![グラフをクリックしてから、縦棒グラフを選択して x 軸と y 軸を割り当てる](./media/app-analytics-tour/230.png)

結果を時間で並べ替えていませんが (テーブル表示でわかるように)、グラフには常に正しい順序で日時が表示されることに注目してください。


## [Where](app-analytics-aggregations.md#where): 条件に基づいてフィルター処理する

Application Insights 監視をアプリの[クライアント](app-insights-javascript.md)側とサーバー側の両方に対して設定した場合、データベース内のテレメトリの一部はブラウザーからのものです。

ブラウザーからレポートされた例外のみを見てみましょう。

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

`where` 演算子はブール式を使用します。以下に重要なポイントをいくつか示します。

 * `and`、`or`: ブール演算子
 * `==`、`<>`: 等しい、等しくない
 * `=~`、`!=`: 等しい、等しくない (大文字と小文字が区別されない文字列の場合)。文字列比較演算子にはさらに多くの種類があります。

詳細については、[スカラー式](app-analytics-scalars.md)に関する記述を参照してください。

### イベントのフィルター処理

失敗した要求を検索する場合は、次のように指定します。

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` は文字列型であるため、数値比較では[キャスト](app-analytics-scalars.md#casts)する必要があります。

さまざまな応答をまとめる場合は、次のように指定します。

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## 時間グラフ

イベント数を日単位で表示する場合は、次のように指定します。

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

以下のグラフの表示オプションを選択します。

![時間グラフ](./media/app-analytics-tour/080.png)

折れ線グラフの x 軸は、DateTime 型である必要があります。

## 複数の系列 

値の組み合わせごとに別の行を作成する場合は、以下のように、`summarize by` 句で複数の値を使用します。

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

グラフに複数の行を表示するには、**[分割単位]** をクリックして列を選択します。

![](./media/app-analytics-tour/100.png)



## 日次平均サイクル

ここでは、使用量が平均的な日にどのように変わるかを見てみます。

1 日の要求数を時間モジュロ (時間単位にビン分割) でカウントする場合は、次のように指定します。

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![平均的な日における時間単位の折れ線グラフ](./media/app-analytics-tour/120.png)

>[AZURE.NOTE] グラフで表示する場合、現時点では期間を日時に変換する必要があることに注意してください。


## 複数の日次系列の比較

ここでは、使用量がさまざまな州で時間帯によってどのように変わるかを見てみます。

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

州でグラフを分割する場合は、次のように指定します。

![client\_StateOrProvince 単位の分割](./media/app-analytics-tour/130.png)


## 分布のプロット

ここでは、長さ単位でセッションの数を見てみます。

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

最後の行は日時に変換するために必要です。現時点では、折れ線グラフの x 軸として指定できるのは日時のみです。

`where` 句では、1 回限りのセッション (sessionDuration==0) を除外し、x 軸の長さを設定します。


![](./media/app-analytics-tour/290.png)



## [パーセンタイル](app-analytics-aggregations.md#percentiles)

ここでは、セッションのパーセンタイルごとの期間範囲を見てみます。

上記のクエリを使用しますが、最後の行は次のように置き換えます。

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

また、複数の要求ですべてのセッションを含む正しい数値を取得するために、where 句の上限を削除しています。

![result](./media/app-analytics-tour/180.png)

この結果から次のことがわかります。

* セッションの 5% の期間は 3 分 34 秒未満である。 
* セッションの 50% が 36 分以上は続かない。
* セッションの 5% は 7 日以上続く。

国別の内訳を表示する場合に必要なのは、次のように両方の summarize 演算子でそれぞれ client\_CountryOrRegion 列を指定するだけです。

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-analytics-tour/190.png)


## [Join](app-analytics-aggregations.md#join)

要求や例外も含む、いくつかのテーブルにアクセスできます。

失敗応答が返された要求に関連する例外を検索する場合は、以下のように `session_Id` でテーブルを結合できます。

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


結合を実行する前に必要な列のみを選択する場合は、`project` を使用することをお勧めします。その場合、同じ句で、タイムスタンプ列の名前を変更します。



## [let](app-analytics-aggregations.md#let): 結果を変数に代入する

上記の式の部分を分割する場合は、[let](./app-analytics-syntax.md#let-statements) を使用します。結果は変わりません。

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> ヒント: Analytics クライアントでは、このように空白行を入れないでください。必ず、すべて間を空けずに実行してください。


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->