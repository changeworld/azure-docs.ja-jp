<properties 
	pageTitle="Analytics - Application Insights の強力な検索ツール" 
	description="Application Insights の強力な診断検索ツールである Analytics の概要。" 
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


# Application Insights の Analytics


[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、Analytics のクエリ言語について説明します。

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

 
一般的なクエリでは、*ソース* テーブルの後に、一連の*演算子*を `|` で区切って記載します。

例として、ハイデラバードの市民が何時に Web アプリを使用しているか調べてみましょう。この調査の際に、HTTP 要求に対してどのような結果コードが返されるか確認しましょう。

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

異なるクライアント IP アドレスをカウントし、それらを過去 30 日間にわたり 1 日の時刻ごとにまとめます。

結果を棒グラフで表示し、異なる応答コードの結果を積算してましょう。

![棒グラフ、X 軸および Y 軸を選択してからセグメント化します](./media/app-insights-analytics/020.png)

ハイデラバードでは、アプリは昼食時と就寝時に最も使用されているようです (これら 500 のコードを調べる必要があります)。


次のような、強力な統計演算もあります。

![](./media/app-insights-analytics/025.png)


言語には、以下のような多くの魅力的な機能があります。

* [フィルター](app-insights-analytics-queries.md)。カスタム プロパティとメトリックを含む、すべてのフィールドで未加工のアプリ テレメトリをフィルター処理します。
* [結合](app-insights-analytics-queries.md#join-operator)。複数のテーブルを結合します。要求をページ ビュー、依存関係の呼び出し、例外およびログ トレースに関連付けます。
* 強力な統計[集計](app-insights-analytics-aggregations.md)。
* SQL と同じように強力ですが、複雑なクエリがより容易になります。つまり、ステートメントを入れ子にするのではなく、基本演算間でデータをパイプします。
* 迅速かつ強力な視覚化。



>[AZURE.NOTE] まず、[言語ツアー](app-insights-analytics-tour.md)を参照することをお勧めします。




## Application Insights のデータに接続する


以下のように、Application Insights でアプリの[概要ブレード](app-insights-dashboards.md)から Analytics を開きます。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics/001.png)


## 制限

現時点では、クエリの結果は過去 1 週間のデータだけに制限されます。



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->