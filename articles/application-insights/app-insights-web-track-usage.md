<properties 
	pageTitle="Application Insights による Web アプリケーションの利用状況の分析" 
	description="Application Insights による Web アプリの利用状況分析の概要" 
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

Application Insights のリソースを [Azure ポータル](https://portal.azure.com)で開き、空のブラウザー ページ負荷グラフをクリックして、セットアップの指示に従います。

[詳細情報](app-insights-javascript.md)


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

セッションは、デバイス、オペレーティング システム、場所などのコンテキストの人気度について詳細な情報を提供します。デバイスのグループごとのセッション数を表示することで、例として、自分のアプリがそのデバイスで使用される頻度を、ページ ビューを数える方法より、より正確に知ることができます。  
これはデバイス固有の問題の優先順位を決定する便利なインプットになります。


#### セッションとは何ですか。

セッションは、ユーザーとアプリケーション間の単一のエンカウンターを表します。最もシンプルな形では、ユーザーがアプリを起動してから終了するまでとなります。Web アプリの場合、既定では、セッションはアイドル状態が 30 分続いた場合か、アクティビティが 24 時間続いた場合に終了します。

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

既定では、Cookie を配置してユーザーを特定します。  
複数のブラウザーやデバイスを使用しているユーザーは複数回カウントされます。ただし、次の場合は[認証されたユーザー](#authenticated-users)に関するページを参照してください:


特定の間隔の**ユーザー数**メトリックは、この間隔の間に記録されたアクティビティとの一意のユーザー数として定義されます。その結果、時間範囲を1 時間未満に設定した場合、長いセッションを持つユーザーは複数回カウントされることがあります。

**新しいユーザー**は、最初のセッションに、この間隔の間に発生したアプリを持つユーザーをカウントします。Cookie によってユーザーをカウントする既定のメソッドを使用した場合、Cookie をクリアしているユーザー、または、新しいデバイスやブラウザーを使用して初めてアプリにアクセスするユーザーも含まれます。![[使用状況] ブレードで [ユーザー] グラフをクリックして新しいユーザーを確認します。](./media/app-insights-web-track-usage/031-dual.png)

### 認証されたユーザー

ユーザーがサインインできる Web アプリの場合、Application Insights に一意のユーザー識別子を提供することで、より正確な数値を取得できます。この識別子はユーザーの名前である必要はありません。また、お使いのアプリで使用しているのと同じ ID である必要もありません。アプリでユーザーが識別されたら、次のコードを使用します。


*クライアント側の JavaScript*

      appInsights.setAuthenticatedUserContext(userId);

アカウントにアプリのグループ ユーザーがある場合、アカウントの識別子も渡すことができます。

      appInsights.setAuthenticatedUserContext(userId, accountId);

ユーザー ID とアカウント ID には、スペースまたは文字 (`,;=|`) を含めることはできません。


[メトリックス エクスプローラー](app-insights-metrics-explorer.md)で、**認証されたユーザー**と**アカウント**のグラフを作成できます。

## 合成トラフィック

合成トラフィックには、可用性とロード テスト、検索エンジンのクローラーやその他のエージェントからの要求が含まれています。

Application Insights は、合成トラフィックを自動的に判定、分類したうえで、適切なマーキングを試みます。ほとんどの場合、合成トラフィックは JavaScript SDK を呼び出しません。そのため、このアクティビティはユーザーとセッションのカウントから除外されます。

ただし、Application Insights [Web テスト][availability]では、ユーザー ID は、POP の場所に基づいて、セッション ID はテストの実行 ID に基づいて、自動的に設定されます。既定のレポートでは、合成のトラフィックは既定により、フィルター処理され、これらのユーザーとセッションは除外されます。ただし、合成トラフィックが含まれる場合は、全体的なユーザーとセッション数はわずかに増加する可能性があります。
 
## ページ利用状況

ページ ビュー グラフをクリックすると、その拡大バージョンと、最も人気のあるページの内訳が表示されます。


![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-web-track-usage/05-games.png)
 
上の例はゲーム Web サイトの情報を示しています。この情報から、次のことがすぐにわかります。

* 利用状況はこの一週間改善していません。おそらく、検索エンジンの最適化について検討する必要があります。
* ゲーム ページの閲覧人数は、ホーム ページよりもはるかに少なくなっています。アクセスした人をゲームへと誘導する魅力がホーム ページに欠けているため、その理由を明らかにする必要があります。
* 最も人気のあるゲームは、"Crossword" です。このゲームに優先的に新しいアイデアと改良を加える必要があります。

## カスタムの追跡

それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。これにより、ユーザーはゲームを素早く切り替えたり、1 つのページ上で複数のゲームを確認したりできるようになります。

ただし、各ゲームが開かれた回数を Application Insights を通じてログに記録したい場合は、ゲームを個別の Web ページで実装したときとまったく同じ方法で行います。これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

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

タイムラインには、変化を他のメトリックやイベントに関連付けることができるという便利な機能があります。たとえば、ゲームのプレイ回数が多いときは、破棄されたゲームも多くなると予測されます。しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## 特定のイベントのドリルダウン

一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

この例では、"NoGame" というカスタム イベントをコーディングしました。ユーザーが実際にゲームを始めることなくログアウトした場合に呼び出されるイベントです。なぜユーザーはゲームを始めることなくログアウトするのでしょうか。 おそらく、いくつかの特定のイベントをドリルダウンすると、手掛かりが得られます。

アプリから受信したカスタム イベントは、概要ブレードに名前別に表示されます。


![概要ブレードで、いずれかのカスタム イベントの種類をクリックします。](./media/app-insights-web-track-usage/07-clickEvent.png)
 
目的のイベントをクリックし、最近の特定の発生箇所を選択します。


![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-web-track-usage/08-searchEvents.png)
 
ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。


![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。
 
このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。


![](./media/app-insights-web-track-usage/10-filter.png)
 
ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。スコアの確認を容易にするユーザー ストーリーの開発を検討する必要があるものと思われます。(加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

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

ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。各バージョンの成功を計測した後、統一バージョンに移行します。

この手法では、お使いのアプリの各バージョンによって送信されるすべてのテレメトリに個別のタグをアタッチします。アクティブな TelemetryContext のプロパティを定義することでタグをアタッチできます。これらの既定のプロパティは、アプリケーションが送信するすべてのテレメトリ メッセージに付加されます。カスタム メッセージだけでなく、標準テレメトリも同様です。

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

すべての新しい TelemetryClients が自動的にコンテキストを使用できるように、汎用の初期化子を設定できます。

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

分析は、単に問題の解決に役立つだけのものではなく、開発サイクルの重要な部分を構成します。いくつかのヒントを次に示します。

* アプリケーションの主要なメトリックを決定する。できる限り多くのユーザーにプレイしてもらえるようにするのか、少数のユーザーに心から満足してもらえることを重視するのか。 アクセス数と売上のどちらを最大化したいのか、などを検討します。
* 各ストーリーの計測を計画する。新しいユーザー ストーリーや機能の概要を作成したり、既存の機能の更新を計画したりする場合は、変更の首尾をどのような方法で計測するかを必ず検討してください。コーディングを開始する前に、"うまくいった場合に、メトリックスにどのような影響を与えるか。 別の新しいイベントを追跡する必要はあるか" と考えます。 当然のことながら、機能が実装されたら、分析に注目し、その結果に従って行動してください。 
* 他のメトリックを重要なメトリックに関連付ける。たとえば、"お気に入り" の機能を追加すると、ユーザーがお気に入りを追加する頻度を知りたくなるものです。しかし、ユーザーがお気に入りに再びアクセスする頻度を知る方が、おそらくより興味深い結果が得られるでしょう。そして最も重要なのは、お気に入りを使用した顧客は、はたしてより多くの製品を購入するのか、ということです。
* カナリア テストを行う。新しい機能を一部のユーザーにのみ表示するための機能スイッチをセットアップします。Application Insights を使用して、新しい機能が想定どおりに使用されているかどうかを確認してください。調整を行い、より広範囲の対象ユーザーにリリースします。
* ユーザーと会話しましょう。 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。


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

 

<!---HONumber=AcomDC_1125_2015-->