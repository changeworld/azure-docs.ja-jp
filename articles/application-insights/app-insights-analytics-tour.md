<properties 
    pageTitle="Application Insights の Analytics のツアー | Microsoft Azure" 
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
    ms.date="10/03/2016" 
    ms.author="awills"/>


 

# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights の Analytics について


[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。


* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。


作業を開始するための基本的なクエリをいくつか見てみましょう。

## <a name="connect-to-your-application-insights-data"></a>Application Insights のデータに接続する

以下のように、Application Insights でアプリの [概要ブレード](app-insights-dashboards.md) から Analytics を開きます。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics-tour/001.png)

    
## <a name="[take](app-insights-analytics-reference.md#take-operator):-show-me-n-rows"></a>[take](app-insights-analytics-reference.md#take-operator): n 個の行を表示する

ユーザーの操作 (通常、Web アプリが受信する HTTP 要求) を記録するデータ ポイントは、 `requests`という名前のテーブルに格納されます。 各行は、アプリの Application Insights SDK から受信したテレメトリ データ ポイントです。

テーブルのいくつかのサンプル行を調べることから始めましょう。

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] ステートメントの任意の場所にカーソルを置いてから、[検索] をクリックします。 ステートメントは複数の行に分割できますが、1 つのステートメントに複数の空白行を配置しないでください。 空白行は、ウィンドウ内に複数のクエリを収めるのに便利です。


以下のように列を選択し、その位置を調整します。

![結果の右上にある列の選択をクリックする](./media/app-insights-analytics-tour/030.png)


詳細を表示する場合は、以下のように該当する項目を展開します。
 
![テーブルを選択し、列の構成を使用する](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] 列の先頭をクリックすると、Web ブラウザーで使用できる結果の順序を変更できます。 ただし、大きな結果セットの場合、ブラウザーにダウンロードされる行の数が制限されることに注意してください。 このように並べ替えると、実際の最高または最低の項目が表示されないことがあります。 その場合は、`top` または `sort` 演算子を使用する必要があります。 

## <a name="[top](app-insights-analytics-reference.md#top-operator)-and-[sort](app-insights-analytics-reference.md#sort-operator)"></a>[top](app-insights-analytics-reference.md#top-operator) と [sort](app-insights-analytics-reference.md#sort-operator)

`take` は結果の簡単なサンプルを取得するのに便利ですが、テーブルの行は任意の順序で表示されます。 指定した順序で表示するには、(サンプルに対して) `top` を使用するか、(テーブル全体に対して) `sort` を使用します。

特定の列で並べ替えた最初の n 個の行を表示する場合は、以下のように指定します。

```AIQL

    requests | top 10 by timestamp desc 
```

* *構文:* ほとんどの演算子に、`by` などのキーワード パラメーターがあります。
* `desc` は降順、`asc` は昇順を意味します。

![](./media/app-insights-analytics-tour/260.png)

`top...` は、`sort ... | take...` と指定するよりも効率的な方法です。 記述例を以下に示します。

```AIQL

    requests | sort by timestamp desc | take 10
```

結果は同じですが、実行時間が少し長くなります。 (`sort` のエイリアスである `order` を記述することもできます。)

テーブル ビューの列ヘッダーを使用して、画面上の結果を並べ替えることもできます。 ただし、言うまでもなく、`take` または `top` を使用してテーブルの一部のみを取得した場合、並べ替えるのは取得したレコードのみとなります。


## <a name="[project](app-insights-analytics-reference.md#project-operator):-select,-rename-and-compute-columns"></a>[project](app-insights-analytics-reference.md#project-operator): 列の選択、名前変更、計算を行う

必要な列だけを選択する場合は、次のように [`project`](app-insights-analytics-reference.md#project-operator) を使用します。

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


また、以下のように列の名前を変更し、新しい列を定義することもできます。

```AIQL

    requests 
  	| top 10 by timestamp desc 
  	| project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* `['...']` や `["..."]` のように角かっこで囲まれている場合は、[列名](app-insights-analytics-reference.md#names)にスペースまたは記号を含めることができます。
* `%` は一般的なモジュロ演算子です。 
* `1d` (数字の 1 の後に "d" が付加されている) は 1 日を意味する期間リテラルです。 期間リテラルにはこの他にも `12h`、`30m`、`10s`、`0.01s` などがあります。
* `floor` (エイリアスは `bin`) は、指定した基準値の最も近い倍数に値を切り捨てます。 したがって、 `floor(aTime, 1s)` の場合、最も近い秒数に時間を切り捨てます。

[式](app-insights-analytics-reference.md#scalars)には、一般的な演算子 (`+` や `-` など) をすべて含めることができ、各種の便利な関数があります。

    

## <a name="[extend](app-insights-analytics-reference.md#extend-operator):-compute-columns"></a>[extend](app-insights-analytics-reference.md#extend-operator): 列を計算する

単に列を既存の列に追加する場合は、以下のように [`extend`](app-insights-analytics-reference.md#extend-operator)を使用します。

```AIQL

    requests 
  	| top 10 by timestamp desc
  	| extend timeOfDay = floor(timestamp % 1d, 1s)
```

既存の列をすべて保持する場合に、[`extend`](app-insights-analytics-reference.md#extend-operator) を使用すると [`project`](app-insights-analytics-reference.md#project-operator) よりも詳細度が低くなります。


## <a name="[summarize](app-insights-analytics-reference.md#summarize-operator):-aggregate-groups-of-rows"></a>[summarize](app-insights-analytics-reference.md#summarize-operator): 行のグループをまとめる

`Summarize` を使用すると、行のグループに対して指定の *集計関数* が適用されます。 

たとえば、Web アプリが要求に対して応答するのにかかった時間が `duration`フィールドでレポートされます。 すべての要求に対する平均応答時間を見てみましょう。

![](./media/app-insights-analytics-tour/410.png)

この結果は要求名で分けることもできます。


![](./media/app-insights-analytics-tour/420.png)

`Summarize` は、ストリーム中のデータ ポイントを収集して、`by` 句によって等しく評価されるグループに分けます。 `by` 式の各値 (上記の例では各操作名) は、結果のテーブルに 1 列で表示されます。 

結果を時間帯でグループ分けすることもできます。

![](./media/app-insights-analytics-tour/430.png)

`bin` 関数 (つまり `floor`) がどのように使われているかに注目してください。 `by timestamp` のみを使用した場合、すべての入力行が小さなグループになります。 時間、数値などの連続するスカラーの場合、連続する範囲を、管理できる数の不連続値に分ける必要があります。これを行う最も簡単な方法は、`bin` を使用することです (この関数は、切り捨てのための使い慣れた `floor` 関数にすぎません)。

同じ方法で文字列の範囲を減らすことができます。


![](./media/app-insights-analytics-tour/440.png)

`name=` を使用すると、集計式または by 句の中で結果列の名前を設定できます。

## <a name="counting-sampled-data"></a>サンプリングされたデータのカウント

イベントのカウントには、`sum(itemCount)` 集計を使うことをお勧めします。 多くの場合、itemCount==1 であるため、この関数を使用すると単にグループ内の行数がカウントされます。 ただし、[サンプリング](app-insights-sampling.md)を実行中の場合、元のイベントの一部のみが Application Insights のデータ ポイントとして保持されます。そのため、表示される各データ ポイントに複数の `itemCount` イベントが存在します。 

たとえば、サンプリングによって元のイベントの 75% が破棄された場合、保持されているレコードでは itemCount == 4 になります。つまり、保持されているすべてのレコードについて、それぞれ 4 つの元のレコードが存在していました。 

アダプティブ サンプリングでは、アプリケーションの使用頻度が高い期間に itemCount が増えます。

したがって、itemCount を合計することによって、元のイベント数の正確な見積もりが得られます。


![](./media/app-insights-analytics-tour/510.png)

また、あるグループ内の行数を実際にカウントする必要がある場合のために、 `count()` 集計 (およびカウント操作) もあります。


さまざまな [集計関数](app-insights-analytics-reference.md#aggregations)が用意されています。


## <a name="charting-the-results"></a>結果のグラフ表示


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

既定では、結果はテーブルの形で表示されます。

![](./media/app-insights-analytics-tour/225.png)


テーブル ビューよりわかりやすくするために、 以下のように縦棒オプションを使用してグラフビューで結果を見てみることにします。

![グラフをクリックしてから、縦棒グラフを選択して x 軸と y 軸を割り当てる](./media/app-insights-analytics-tour/230.png)

結果を時間で並べ替えていませんが (テーブル表示でわかるように)、グラフには常に正しい順序で日時が表示されることに注目してください。


## <a name="[where](app-insights-analytics-reference.md#where-operator):-filtering-on-a-condition"></a>[where](app-insights-analytics-reference.md#where-operator): 条件に基づいてフィルター処理する

Application Insights 監視をアプリの [クライアント](app-insights-javascript.md) 側とサーバー側の両方に対して設定した場合、データベース内のテレメトリの一部はブラウザーから取得されます。

ブラウザーからレポートされた例外のみを見てみましょう。

```AIQL

    exceptions 
  	| where device_Id == "browser" 
  	|  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

`where` 演算子はブール式を使用します。 以下に重要なポイントをいくつか示します。

 * `and`、`or`: ブール演算子
 * `==`、`<>`: 等しい、等しくない
 * `=~`、`!=`: 等しい、等しくない (大文字と小文字が区別されない文字列の場合)。 文字列比較演算子にはさらに多くの種類があります。

詳細については、 [スカラー式](app-insights-analytics-reference.md#scalars)に関する記述をご覧ください。

### <a name="filtering-events"></a>イベントのフィルター処理

失敗した要求を検索する場合は、次のように指定します。

```AIQL

    requests 
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` は文字列型であるため、数値比較では [キャスト](app-insights-analytics-reference.md#casts) する必要があります。

さまざまな応答をまとめる場合は、次のように指定します。

```AIQL

    requests
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
  	| summarize count() 
      by resultCode
```

## <a name="timecharts"></a>時間グラフ

イベント数を日単位で表示する場合は、次のように指定します。

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

以下のグラフの表示オプションを選択します。

![時間グラフ](./media/app-insights-analytics-tour/080.png)

折れ線グラフの x 軸は、DateTime 型である必要があります。 

## <a name="multiple-series"></a>複数の系列 

値の組み合わせごとに別の行を作成する場合は、以下のように、 `summarize by` 句で複数の値を使用します。

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

グラフに複数の行を表示するには、 **[分割単位]** をクリックして列を選択します。

![](./media/app-insights-analytics-tour/100.png)



## <a name="daily-average-cycle"></a>日次平均サイクル

ここでは、使用量が平均的な日にどのように変わるかを見てみます。

1 日の要求数を時間モジュロ (時間単位にビン分割) でカウントする場合は、次のように指定します。

```AIQL

    requests
  	| extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
  	| summarize event_count=count() by hour
```

![平均的な日における時間単位の折れ線グラフ](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] グラフで表示する場合、現時点では期間を日時に変換する必要があることに注意してください。


## <a name="compare-multiple-daily-series"></a>複数の日次系列の比較

ここでは、使用量がさまざまな州で時間帯によってどのように変わるかを見てみます。

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

州でグラフを分割する場合は、次のように指定します。

![client_StateOrProvince 単位の分割](./media/app-insights-analytics-tour/130.png)


## <a name="plot-a-distribution"></a>分布のプロット

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


![](./media/app-insights-analytics-tour/290.png)



## <a name="[percentiles](app-insights-analytics-reference.md#percentiles)"></a>[パーセンタイル](app-insights-analytics-reference.md#percentiles)

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

![result](./media/app-insights-analytics-tour/180.png)

この結果から次のことがわかります。

* セッションの 5% の期間は 3 分 34 秒未満である。 
* セッションの 50% が 36 分以上は続かない。
* セッションの 5% は 7 日以上続く。

国別の内訳を表示する場合に必要なのは、次のように両方の summarize 演算子でそれぞれ client_CountryOrRegion 列を指定するだけです。

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

![](./media/app-insights-analytics-tour/190.png)


## <a name="[join](app-insights-analytics-reference.md#join)"></a>[Join](app-insights-analytics-reference.md#join)

要求や例外も含む、いくつかのテーブルにアクセスできます。

失敗応答が返された要求に関連する例外を検索する場合は、以下のように `session_Id`でテーブルを結合できます。

```AIQL

    requests 
  	| where toint(responseCode) >= 500 
  	| join (exceptions) on operation_Id 
  	| take 30
```


結合を実行する前に必要な列のみを選択する場合は、 `project` を使用することをお勧めします。
その場合、同じ句で、タイムスタンプ列の名前を変更します。



## <a name="[let](app-insights-analytics-reference.md#let-clause):-assign-a-result-to-a-variable"></a>[let](app-insights-analytics-reference.md#let-clause): 結果を変数に代入する

上記の式の部分を分割する場合は、 [let](./app-insights-analytics-reference.md#let-statements) を使用します。 結果は変わりません。

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
  	| join (exceptions) on session_Id 
  	| take 30
```

> ヒント: Analytics クライアントでは、このように空白行を入れないでください。 必ず、すべて間を空けずに実行してください。


## <a name="accessing-nested-objects"></a>入れ子になったオブジェクトへのアクセス

入れ子になったオブジェクトに簡単にアクセスできます。 たとえば、例外ストリームの構造化されたオブジェクトは次のようになります。

![result](./media/app-insights-analytics-tour/520.png)

関心のあるプロパティを選択することで、これをフラット化できます。

```AIQL

    exceptions | take 10
  	| extend method1 = tostring(details[0].parsedStack[1].method)
```

[キャスト](app-insights-analytics-reference.md#casts) を使用して適切な型に変換する必要があります。

## <a name="custom-properties-and-measurements"></a>カスタム プロパティと測定値

アプリケーションで[カスタム ディメンション (プロパティ) とカスタム測定値](app-insights-api-custom-events-metrics.md#properties)をイベントにアタッチする場合、これらは `customDimensions` および `customMeasurements` オブジェクトで表示されます。


たとえば、アプリに以下が含まれているものとします。

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Analytics でこれらの値を抽出するには、次のようにします。

```AIQL

    customEvents
  	| extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

## <a name="tables"></a>テーブル

アプリから受信したテレメトリのストリームは、いくつかのテーブルからアクセスできます。 各テーブルで使用できるプロパティのスキーマが、ウィンドウの左側に表示されます。

### <a name="requests-table"></a>要求テーブル

Web アプリとセグメントに対する HTTP 要求をページ名別にカウントします。

![名前別にセグメント化された要求をカウントします。](./media/app-insights-analytics-tour/analytics-count-requests.png)

最も失敗した要求を検索します。

![名前別にセグメント化された要求をカウントします。](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>カスタム イベント テーブル

[TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) を使用して独自のイベントを送信すると、このテーブルからイベントを読み取ることができます。 

アプリ コードに次の行が含まれている例を見てみましょう。

```C#

    telemetry.TrackEvent("Query", 
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

これらのイベントの頻度を表示します。
 
![カスタム イベントの表示率](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

イベントから測定値とディメンションを抽出します。

![カスタム イベントの表示率](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>カスタム メトリック テーブル

[TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) を使用して独自のメトリック値を送信すると、**customMetrics** ストリームでその結果がわかります。 次に例を示します。  

![Application Insights Analytics のカスタム メトリック](./media/app-insights-analytics-tour/analytics-custom-metrics.png)


> [AZURE.NOTE] [メトリックス エクスプローラー](app-insights-metrics-explorer.md)では、任意の種類のテレメトリにアタッチされているすべてのカスタム測定値は、`TrackMetric()` を使用して送信されたメトリックと共にメトリックス ブレードにまとめて表示されます。 一方 Analytics では、カスタム測定値は実行されたテレメトリの種類 (イベントや要求など) にまだアタッチされており、TrackMetric によって送信されたメトリックは独自のストリームに表示されます。

### <a name="performance-counters-table"></a>パフォーマンス カウンター テーブル

[パフォーマンス カウンター](app-insights-web-monitor-performance.md#system-performance-counters)は、CPU、メモリ、ネットワーク使用率などの、アプリの基本的なシステム メトリックを表示します。 独自のカスタム カウンターを含む追加のカウンターを送信するように SDK を構成することができます。

**performanceCounters** スキーマは、各パフォーマンス カウンターの `category`、`counter` 名、および `instance` 名を表示します。 カウンター インスタンス名は一部のパフォーマンス カウンターにのみ適用され、通常はカウントが関連付けられているプロセス名を示します。 各アプリケーションのテレメトリでは、そのアプリケーションのカウンターのみが確認できます。 たとえば、使用できるカウンターを表示するには次のようにします。 

![Application Insights Analytics のパフォーマンス カウンター](./media/app-insights-analytics-tour/analytics-performance-counters.png)

最近の利用可能なメモリのグラフを取得するには、次のようにします。 

![Application Insights Analytics のメモリ タイムチャート](./media/app-insights-analytics-tour/analytics-available-memory.png)


他のテレメトリと同様に、**performanceCounters** にも、アプリを実行しているホスト コンピューターの ID を示す列 `cloud_RoleInstance` があります。 たとえば、異なるコンピューター上でのアプリのパフォーマンスを比較するには、次のようにします。 


![Application Insights Analytics でロール インスタンス別にセグメント化されたパフォーマンス](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>例外テーブル

[アプリによって報告された例外](app-insights-asp-net-exceptions.md)はこのテーブルで入手できます。 

例外発生時にアプリが処理した HTTP 要求を検索するには、operation_Id で結合します。

![operation_Id で例外と要求を結合](./media/app-insights-analytics-tour/analytics-exception-request.png)


### <a name="browser-timings-table"></a>ブラウザー タイミング テーブル

`browserTimings` は、ユーザーのブラウザーが収集したページ読み込みデータを表示します。

これらのメトリックを表示するには、[クライアント側テレメトリのためにアプリをセットアップ](app-insights-javascript.md)します。 

スキーマには、[ページ読み込みプロセスのさまざまな段階の長さを示すメトリック](app-insights-javascript.md#page-load-performance)が含まれます。 (ユーザーがページを読む時間の長さは表示されません。)  

さまざまなページの人気度や、ページごとの読み込み時間を表示します。

![Analytics でのページ読み込み時間](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availbility-results-table"></a>可用性の結果テーブル

`availabilityResults` は、[Web テスト](app-insights-monitor-web-app-availability.md)の結果を表示します。 各テスト場所からのテストの実行は、それぞれ個別に報告されます。 


![Analytics でのページ読み込み時間](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>依存関係テーブル

アプリが行うデータベースや REST API への呼び出し、および TrackDependency() への他の呼び出しの結果が含まれています。

### <a name="traces-table"></a>トレース テーブル

TrackTrace() を使用してアプリが送信したテレメトリ、または [他のログ記録フレームワーク](app-insights-asp-net-trace-logs.md)が含まれています。

## <a name="try-it!"></a>試してみる

* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]





<!--HONumber=Oct16_HO2-->


