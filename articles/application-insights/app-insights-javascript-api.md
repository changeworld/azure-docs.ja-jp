<properties 
	pageTitle="Application Insights SDK JavaScript API" 
	description="リファレンス ドキュメント" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights SDK JavaScript API

JavaScript SDK は、[Application Insights](https://azure.microsoft.com/services/application-insights/) で [Web ページの追跡](app-insights-javascript.md)を設定するときに、Web ページに読み込まれます。

[API の概要と例](app-insights-api-custom-events-metrics.md)

## AppInsights クラス

テレメトリを Application Insights に送信する SDK の主要要素です。

[Web ページの追跡を設定](app-insights-javascript.md)した Web ページでは、`appInsights` インスタンスを使用することができます。次に例を示します。
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

ページまたは類似のコンテナーがユーザーに表示されたことを記録します。

 | | 
---|---|---
`name` | `? string` | ポータルのページを識別するために使用する名前。既定値はドキュメント タイトルです。
`url` | `? string` | ページまたは類似の項目を識別する相対パスまたは絶対 URL。既定値はウィンドウの位置です。
`properties` | `? {[string]:string}` | ポータルのページとメトリックをフィルター処理するために使用する追加のデータ。既定値は空です。
`measurements` | `? {[string]:number}` | ポータルのメトリックス エクスプローラーに表示される、このページに関連付けられているメトリック。既定値は空です。


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

ユーザーの操作などのイベントを記録します。

ポータルでは、イベントを名前で選択して、[イベントをカウントするグラフや関連付けられている測定値を表示](app-insights-metrics-explorer.md)できます。

また、検索して[個々のイベントを表示](app-insights-diagnostic-search.md)することもできます。

 | | 
---|---|---
 `name` | `string` | イベントを識別します。同じ名前を持つイベントをカウントして、[メトリックス エクスプローラー](app-insights-metrics-explorer.md)でグラフ化できます。
`properties` | `? {[string]:string}` | ポータルのページとメトリックをフィルター処理するために使用する追加のデータ。既定値は空です。
`measurements` | `? {[string]:number}` | ポータルのメトリックス エクスプローラーに表示される、このページに関連付けられているメトリック。既定値は空です。


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


特定のイベントに関連付けられていない数値を記録します。通常、パフォーマンス指標の標準レポートを送信するために使用されます。

ポータルで、メトリックを名前で選択して、[時間の経過と共にその値をグラフ化](app-insights-metrics-explorer.md)できます。個々の trackMetric の呼び出しを検索したり表示したりすることはできません。

1 つの測定値を送信するには、最初の 2 つのパラメーターのみを使用します。非常に頻繁に測定する場合、複数の測定値を集計し、その結果の平均値を周期的に送信することで、テレメトリの帯域幅を減らすことができます。

 | | 
---|---|---
`name` | `string` | メトリックを識別する文字列。ポータルでは、表示するメトリックを名前で選択できます。
`average` | ` number` | 1 つの測定値、または複数の測定値の平均値。
`sampleCount` | `? number` | 平均で表される測定値の数。既定値は 1 です。
`min` | `? number` | このサンプルの最小の測定値。既定値は平均値です。
`max` | `? number` | このサンプルの最大の測定値。既定値は平均値です。

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

例外を記録します(ブラウザーによってキャッチされた例外も記録されます)。

ポータルで、[例外の種類を検索して、その種類、メッセージ、個々のインスタンスのスタック トレースを表示](app-insights-diagnostic-search.md)することができます。

 | | 
---|---|---
`exception` | `Error` | catch 句から発生したエラー。  
`handledAt` | `? string` | 既定値は "unhandled" です。
`properties` | `? {[string]:string}` | ポータルのページとメトリックをフィルター処理するために使用する追加のデータ。既定値は空です。
`measurements` | `? {[string]:number}` | ポータルのメトリックス エクスプローラーに表示される、このページに関連付けられているメトリック。既定値は空です。

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

メソッドへの出入りなど、診断用のイベントを記録します。

ポータルでは、メッセージの内容を検索して、[trackTrace の個々のイベントを表示](app-insights-diagnostic-search.md)できます(`trackEvent` とは異なり、ポータルでメッセージの内容をフィルター処理することはできません)。

 | | 
---|---|---
`message` | `string` | 診断データ。名前よりかなり長くなる可能性があります。

### flush

    flush()

キューに登録されたすべてのテレメトリを即座に送信します。

ウィンドウを閉じるときに使用します。


### config

    config: IConfig

テレメトリ データの送信方法を制御する値。

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Web ページに挿入する[スニペット](app-insights-javascript-api.md)にこれらの値を設定します。次の行を検索し、項目をさらに追加します。

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

SDK が環境から抽出しようとする、デバイス、場所、ユーザーに関する情報。


## class TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## オープン ソース コード

[SDK 用のコード](https://github.com/Microsoft/ApplicationInsights-js)を閲覧または投稿してください。

<!---HONumber=August15_HO6-->