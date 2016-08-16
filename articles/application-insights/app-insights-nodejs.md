<properties
	pageTitle="Application Insights SDK を追加して Node.js アプリを監視する | Microsoft Azure"
	description="オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="awills"/>


# Application Insights SDK を追加して Node.js アプリを監視する

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights](app-insights-overview.md) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断](app-insights-detect-triage-diagnose.md)、[アプリの使用方法の把握](app-insights-overview-usage.md)に役立ちます。Azure Web Apps に加えて、独自のオンプレミス IIS サーバーや Azure の仮想マシンでホストされているアプリに対しても機能します。



SDK では、受信 HTTP 要求レートと応答、パフォーマンス カウンター (CPU、メモリ、RPS)、ハンドルされない例外の自動収集機能が提供されます。さらに、カスタム呼び出しを追加して、依存関係、メトリック、またはその他のイベントを追跡することができます。

![Example performance monitoring charts](./media/app-insights-asp-net-manual/10-perf.png)


#### 開始する前に

必要なもの:

* Visual Studio 2013 以降より新しいバージョンが適しています。
* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。

## <a name="add"></a>Application Insights リソースの作成

[Azure ポータル][portal]にサインインし、Application Insights の新しいリソースを作成します。Azure の[リソース][roles]は、サービスのインスタンスです。このリソースでは、アプリのテレメトリが分析されて画面に表示されます。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-asp-net-manual/01-new-asp.png)

アプリケーションの種類として [その他] を選択します。アプリケーションの種類を選択すると、[リソース] ブレードの既定のコンテンツと[メトリックス エクスプローラー][metrics]に表示されるプロパティが設定されます。

#### インストルメンテーション キーのコピー

これはリソースを識別するキーです。データをリソースに送信するために SDK の後の手順でインストールします。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-asp-net-manual/02-props-asp.png)


## <a name="sdk"></a>アプリケーションに SDK をインストールする

```
npm install applicationinsights
```

## 使用法

これにより、要求の監視、ハンドルされない例外の追跡、システム パフォーマンスの監視 (CPU/Memory/RPS) が有効になります。

```javascript

var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

インストルメンテーション キーは、APPINSIGHTS\_INSTRUMENTATIONKEY 環境変数でも設定できます。この設定が済むと、`appInsights.setup()` または `appInsights.getClient()` を呼び出すときに引数が不要になります。

インストルメンテーション キーを空でない文字列に設定することで、テレメトリを送信せずに SDK を試してみることができます。


## <a name="run"></a> プロジェクトの実行

アプリケーションを実行して動作を確認します。さまざまなページを開き、テレメトリをいくつか生成します。


## <a name="monitor"></a> 利用統計情報を表示する

[Azure ポータル](https://portal.azure.com)に戻り、Application Insights のリソースを参照します。


[概要] ページでデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。For example:

![クリックしてより多くのデータを表示する](./media/app-insights-asp-net-manual/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。[メトリックの詳細についてはこちらをご覧ください。][perf]

#### データが表示されない場合

* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]](app-insights-diagnostic-search.md) タイルを開き、個々のイベントを表示します。メトリック パイプラインを経由すると、イベントの取得に少し時間がかかる場合があります。
* 数秒待機してから **[最新の情報に更新]** をクリックします。グラフは周期的に自動で更新されますが、データの表示を待機している場合、手動で更新することもできます。
* [トラブルシューティング][qna]に関するページを参照します。

## アプリケーションの発行

ここで、アプリケーションを IIS または Azure にデプロイし、データ累積を確認します。


#### サーバーに発行した後でデータはありませんか。

サーバーのファイアウォールで発信トラフィック用のこれらのポートを開きます。

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### ビルド サーバーで問題が発生した場合

[このトラブルシューティング項目](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)を参照してください。



## カスタマイズした使用状況 

### 自動収集の無効化

```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### カスタム監視

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[テレメトリ API の詳細については、こちらを参照してください](app-insights-api-custom-events-metrics.md)。

### 複数のインストルメンテーション キーの使用

```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## 例

### 依存関係の追跡

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### すべての "GET" 要求の手動要求トラッキング

```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```




<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md

<!---HONumber=AcomDC_0810_2016-->