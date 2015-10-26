<properties 
	pageTitle="Application Insights による Web アプリケーションの利用状況の分析" 
	description="Application Insights による利用状況分析の概要" 
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
	ms.date="08/24/2015" 
	ms.author="awills"/>
 
# Application Insights による Web アプリケーションの利用状況の分析

ユーザーによるアプリケーションの使用状況を把握することで、ユーザーにとって最も重要なシナリオに重点を置いて開発作業を行うことができ、実現するのが簡単またはより困難であるとユーザーが捉えている目標を理解することができます。

Visual Studio Application Insights は、2 つのレベルの使用状況の追跡を提供します。

* **ユーザー、セッション、およびページ ビュー データ** - 既定で提供されています。  
* **カスタム テレメトリ** - アプリのユーザー エクスペリエンスを使用して[コードを記述][api]してユーザーを追跡します。 

## 設定

Web アプリケーションからの使用状況データは、クライアント ブラウザーから取得します。

#### Application Insights リソースの設定 

Application Insights リソースは、アプリのテレメトリ データが分析され、表示される Microsoft Azure 内の場所です。[ASP.NET][greenbrown] か [J2EE][java] のサーバー側からのデータを表示するためにApplication Insights リソース を既にセットアップしています。そうでない場合は、その作業を実行できます。

It's usually best to display the usage data from the web client in the same resource as the data from the server.That way, you can easily correlate diagnostics and metrics from the two ends.そのため、リソースが既にある場合は、次の手順に進みます。

#### Web ページにコードを挿入する

[Azure ポータル][portal]のリソースで、クイック スタートを開き、コード スニペットを取得して、Web ページを監視します。

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

すべてのページに含まれるように、コードを (.NET の) \_Layout.cshtml などのマスター ページまたはインクルード ファイルに配置します。

The code snippet includes the instrumentation key (iKey) that identifies your resource.To send data to a different resource - for example during testing - you only have to replace the iKey.

Web ページを発行するか、デバッグ モードで使用するには、テレメトリ データを作成します。


## Web アプリケーションの人気度を調べる

[Azure ポータル][portal]にログインし、アプリケーション リソースを確認し、[使用状況] をクリックします。

![](./media/app-insights-web-track-usage/14-usage.png)

* **ユーザー:** グラフの時間範囲内で変化する別個のアクティブ ユーザーの数。 
* **セッション:** アクティブなセッションの数
* **ページ ビュー**: trackPageView() の呼び出し数をカウントします。通常、各 Web ページで 1 回呼び出されます。

グラフをクリックすると、詳細が表示されます。グラフの時間範囲を変更できます。

### ユーザーの居住地を調べる

[使用状況] ブレードで [ユーザー] グラフをクリックして詳細を表示します。

![[使用状況] ブレードで [ユーザー] グラフをクリックします。](./media/app-insights-web-track-usage/02-sessions.png)
 
### 使用されているブラウザーまたはオペレーティング システムを調べる

プロパティ (ブラウザー、オペレーティング システム、市区町村など) に基づいてデータをグループ化 (分割) できます。

![1 つのメトリックを表示するグラフを選択します。[グループ] をオンにし、プロパティを選択します](./media/app-insights-web-track-usage/03-browsers.png)


## セッション

セッションは、 Application Insights の基本的な概念であり、要求、ページ ビュー、例外、ユーザーがコード化したカスタム イベントなどのすべてのテレメトリ イベントを特定のユーザーセッションに関連付けます。

デバイスの特性や地理的位置、オペレーティング システムなど、各セッションについての豊富なコンテキスト情報が収集されます。

クライアントとサーバーの両方をインストルメント化する場合 ([ASP.NET][greenbrown] か [J2EE][java])、SDK は両方の側でイベントが関連付けられるように 、クライアントとサーバー間のセッション ID を反映します。

[問題を診断する][diagnostic]場合は、すべての要求とイベント、ログに記録された例外や追跡を含む、問題が発生したセッションに関連するすべてのテレメトリを見つけることができます。

セッションは、デバイス、オペレーティング システム、場所などのコンテキストの人気度について詳細な情報を提供します。By showing the count of sessions grouped by device, for example, you get a more accurate count of how often that device is used with your app, than by counting page views.これはデバイス固有の問題の優先順位を決定する便利なインプットになります。


#### セッションとは何ですか。

セッションは、ユーザーとアプリケーション間の単一のエンカウンターを表します。In its simplest form, session starts with a user launching the app and finishes when user leaves the app.For web apps, by default, the session terminates after 30 minutes of inactivity, or after 24 hours of activity.

これらの既定値は、コード スニペットを編集して変更できます。

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs` : 時間は、ミリ秒単位で、アイドル状態の有効期限を決定します。既定値: 30 分。
* `sessionExpirationMs` : セッションの最大の長さをミリ秒単位で決定します。ユーザーはこの時間以降もアクティブなままである場合は、別のセッションがカウントされます。既定値: 24 時間。

**セッションの期間** は、セッションの最初と最後のテレメトリ項目の時間範囲を記録する[メトリック][metrics]です。(タイムアウト期間は含まれません)。

特定の間隔の**セッション数**は、この間隔でのアクティビティとの一意のセッション数として定義されます。過去 1 週間の日次セッション数などの長い時間範囲を確認する場合は、通常、セッションの合計数と同じです。

ただし、1 時間ごとなどの短い時間範囲を確認する場合、複数の時間にわたる長いセッションは、セッションがアクティブな状態だった時間ごとにカウントされます。

## ユーザーとユーザー数


各ユーザーのセッションは、一意のユーザー ID に関連付けられます。

By default, the user is identified by placing a cookie.複数のブラウザーやデバイスを使用しているユーザーは複数回カウントされます。ただし、次の場合は[認証されたユーザー](#authenticated-users)に関するページを参照してください:


特定の間隔の**ユーザー数**メトリックは、この間隔の間に記録されたアクティビティとの一意のユーザー数として定義されます。その結果、時間範囲を1 時間未満に設定した場合、長いセッションを持つユーザーは複数回カウントされることがあります。

**新しいユーザー**は、最初のセッションに、この間隔の間に発生したアプリを持つユーザーをカウントします。Cookie によってユーザーをカウントする既定のメソッドを使用した場合、Cookie をクリアしているユーザー、または、新しいデバイスやブラウザーを使用して初めてアプリにアクセスするユーザーも含まれます。![[使用状況] ブレードで [ユーザー] グラフをクリックして新しいユーザーを確認します。](./media/app-insights-web-track-usage/031-dual.png)

### 認証されたユーザー

ユーザーがサインインできる Web アプリの場合、Application Insights に一意のユーザー識別子を提供することで、より正確な数値を取得できます。It doesn't have to be their name, or the same id that you use in your app.アプリでユーザーが識別されたら、次のコードを使用します。


*クライアント側の JavaScript*

      appInsights.setAuthenticatedUserContext(userId);

アカウントにアプリのグループ ユーザーがある場合、アカウントの識別子も渡すことができます。

      appInsights.setAuthenticatedUserContext(userId, accountId);

ユーザー ID とアカウント ID には、スペースまたは文字 (`,;=|`) を含めることはできません。


[メトリックス エクスプローラー](app-insights-metrics-explorer.md)で、**認証されたユーザー**と**アカウント**のグラフを作成できます。

## 合成トラフィック

合成トラフィックには、可用性とロード テスト、検索エンジンのクローラーやその他のエージェントからの要求が含まれています。

Application Insights tries strives to automatically determine and classify synthetic traffic and mark it appropriately.ほとんどの場合、合成トラフィックは JavaScript SDK を呼び出しません。そのため、このアクティビティはユーザーとセッションのカウントから除外されます。

ただし、Application Insights [Web テスト][availability]では、ユーザー ID は、POP の場所に基づいて、セッション ID はテストの実行 ID に基づいて、自動的に設定されます。既定のレポートでは、合成のトラフィックは既定により、フィルター処理され、これらのユーザーとセッションは除外されます。ただし、合成トラフィックが含まれる場合は、全体的なユーザーとセッション数はわずかに増加する可能性があります。
 
## ページ利用状況

ページ ビュー グラフをクリックすると、その拡大バージョンと、最も人気のあるページの内訳が表示されます。


![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-web-track-usage/05-games.png)
 
上の例はゲーム Web サイトの情報を示しています。From it we can instantly see:

* Usage hasn't improved in the past week.Maybe we should think about search engine optimization?
* Many fewer people see the games pages than the Home page.Why doesn't our Home page attract people to play games?
* 'Crossword' is the most popular game.このゲームに優先的に新しいアイデアと改良を加える必要があります。

## カスタムの追跡

それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。This allows the user to switch quickly between one game and another, or even have several games on one page.

But you'd still like Application Insights to log the number of times each game is opened, in exactly the same way as when they were on separate web pages.これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

	appInsights.trackPageView(game.Name);

## カスタム イベント

カスタム イベントを使用できます。カスタム イベントは、デバイスのアプリ、Web ページ、または Web サーバーから送信できます。

*JavaScript*

    appInsights.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


最もよく使用されるカスタム イベントは、概要ブレードに示されます。

![概要ブレードで、下へスクロールし、[カスタム イベント] をクリックします。](./media/app-insights-web-track-usage/04-events.png)

テーブルの先頭をクリックすると、イベントの合計数が表示されます。イベント名などのさまざまな属性によって、グラフを分割することができます。

![1 つのメトリックのみを示すグラフを選択します。[グループ] をオンにします。プロパティを選択します。使用できないプロパティもあります。](./media/app-insights-web-track-usage/06-eventsSegment.png)

タイムラインには、変化を他のメトリックやイベントに関連付けることができるという便利な機能があります。For example, at times when more games are played, you'd expect to see a rise in abandoned games as well.しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## 特定のイベントのドリルダウン

一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

In this example, we coded a custom event "NoGame" that is called if the user logs out without actually starting a game.Why would a user do that? Maybe if we drill into some specific occurrences, we'll get a clue.

アプリから受信したカスタム イベントは、概要ブレードに名前別に表示されます。


![概要ブレードで、いずれかのカスタム イベントの種類をクリックします。](./media/app-insights-web-track-usage/07-clickEvent.png)
 
目的のイベントをクリックし、最近の特定の発生箇所を選択します。


![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-web-track-usage/08-searchEvents.png)
 
ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。


![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。
 
このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。


![](./media/app-insights-web-track-usage/10-filter.png)
 
ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。Maybe we should consider developing a user story that makes it easier to do that.(加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

## プロパティを使用してデータをフィルター処理、検索、および分割する
イベントには任意のタグと数値をアタッチできます。
 

*クライアント側の JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*サーバー側の C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*サーバー側の VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

次のようにページ ビューを同じ方法でプロパティに結び付けます。

*クライアント側の JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

診断検索で、イベントのそれぞれの発生箇所をクリックしてプロパティを表示します。


![イベントの一覧で、イベントを開き、[...] をクリックしてその他のプロパティを表示します](./media/app-insights-web-track-usage/11-details.png)
 
[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。


![[検索] ボックスに値を入力します](./media/app-insights-web-track-usage/12-searchEvents.png)


## A | B テスト

ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。Measure the success of each, and then move to a unified version.

For this technique, you attach distinct tags to all the telemetry that is sent by each version of your app.You can do that by defining properties in the active TelemetryContext.These default properties are added to every telemetry message that the application sends - not just your custom messages, but the standard telemetry as well.

Application Insights ポータルでは、タグに基づいてデータをフィルター選択およびグループ化 (分割) して、異なるバージョンを比較できます。

*サーバー側の C#*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*サーバー側の VB*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

個々のテレメトリは、既定値を無視することができます。

You can set up a universal initializer so that all new TelemetryClients automatically use your context.

```C#

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

アプリ初期化子 (Global.asax.cs など) 内:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## ビルド、評価、学習

分析は、単に問題の解決に役立つだけのものではなく、開発サイクルの重要な部分を構成します。Here are some tips:

* Determine the key metric of your application.Do you want as many users as possible, or would you prefer a small set of very happy users? Do you want to maximize visits or sales?
* Plan to measure each story.When you sketch a new user story or feature, or plan to update an existing one, always think about how you will measure the success of the change.Before coding starts, ask "What effect will this have on our metrics, if it works? Should we track any new events?" And of course, when the feature is live, make sure you look at the analytics and act on the results. 
* Relate other metrics to the key metric.For example, if you add a "favorites" feature, you'd like to know how often users add favorites.But it's perhaps more interesting to know how often they come back to their favorites.And, most importantly, do customers who use favorites ultimately buy more of your product?
* Canary testing.Set up a feature switch that allows you to make a new feature visible only to some users.Use Application Insights to see whether the new feature is being used in the way you envisaged.Make adjustments, then release it to a wider audience.
* Talk to your users! 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。


## 参照

* [API の使用 - 概要][api]
* [JavaScript API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## ビデオ

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO3-->