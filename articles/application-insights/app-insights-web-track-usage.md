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
	ms.date="06/19/2015" 
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

通常、サーバーからのデータと同じリソースで、Web クライアントからの使用状況データを表示することをお勧めします。これにより、両端から診断とメトリックを簡単に関連付けることができます。そのため、リソースが既にある場合は、次の手順に進みます。

#### Web ページにコードを挿入する

[Azure ポータル][portal]のリソースで、クイック スタートを開き、コード スニペットを取得して、Web ページを監視します。

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

すべてのページに含まれるように、コードを (.NET の) \_Layout.cshtml などのマスター ページまたはインクルード ファイルに配置します。

コード スニペットには、リソースを識別するインストルメンテーション キー (iKey) が含まれています。たとえば、テスト中に個別のリソースにデータを送信するには、iKey を置き換える必要があります。

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

セッションは、デバイス、オペレーティング システム、場所などのコンテキストの人気度について詳細な情報を提供します。たとえば、デバイスでグループ化されたセッションの数を示すことにより、デバイスがアプリと使用された頻度について、ページ ビューをカウントするよりも正確にカウントできます。これはデバイス固有の問題の優先順位を決定する便利なインプットになります。


#### セッションとは何ですか。

セッションは、ユーザーとアプリケーション間の単一のエンカウンターを表します。最も単純な形式では、セッションは、ユーザーがアプリケーションを起動すると開始され、ユーザーがアプリを終了すると終了します。Web Apps では、既定では、アイドル状態が 30 分続くか、アクティビティが 24 時間続くと終了します。

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

既定では、ユーザーは、Cookie を配置することによって識別されます。この場合、複数のブラウザーやデバイスを使用しているユーザーは複数回カウントされます。

特定の間隔の**ユーザー数**メトリックは、この間隔の間に記録されたアクティビティとの一意のユーザー数として定義されます。その結果、時間範囲を1 時間未満に設定した場合、長いセッションを持つユーザーは複数回カウントされることがあります。

**新しいユーザー**は、最初のセッションに、この間隔の間に発生したアプリを持つユーザーをカウントします。Cookie によってユーザーをカウントする既定のメソッドを使用した場合、Cookie をクリアしているユーザー、または、新しいデバイスやブラウザーを使用して初めてアプリにアクセスするユーザーも含まれます。![[使用状況] ブレードで [ユーザー] グラフをクリックして新しいユーザーを確認します。](./media/app-insights-web-track-usage/031-dual.png)

## 合成トラフィック

合成トラフィックには、可用性とロード テスト、検索エンジンのクローラーやその他のエージェントからの要求が含まれています。

Application Insight は合成トラフィックを自動的に特定して分類し、適切にマークにします。ほとんどの場合、合成トラフィックは JavaScript SDK を呼び出しません。そのため、このアクティビティはユーザーとセッションのカウントから除外されます。

ただし、Application Insights [Web テスト][availability]では、ユーザー ID は、POP の場所に基づいて、セッション ID はテストの実行 ID に基づいて、自動的に設定されます。既定のレポートでは、合成のトラフィックは既定により、フィルター処理され、これらのユーザーとセッションは除外されます。ただし、合成トラフィックが含まれる場合は、全体的なユーザーとセッション数はわずかに増加する可能性があります。
 
## ページ利用状況

ページ ビュー グラフをクリックすると、その拡大バージョンと、最も人気のあるページの内訳が表示されます。


![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-web-track-usage/05-games.png)
 
上の例はゲーム Web サイトの情報を示しています。このグラフから、次のことがわかります。

* 過去 1 週間の間に利用数が伸びていない。おそらく検索エンジンの最適化について考える必要があります。
* ホーム ページを見ているユーザーよりもゲームのページを見ているユーザーの方が少ない。ホーム ページ上でより積極的にゲームに誘導する必要があります。
* 最も人気のあるゲームは "クロスワード" である。このゲームに優先的に新しいアイデアと改良を加える必要があります。

## カスタムの追跡

それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。これにより、ユーザーは、1 つのゲームから別のゲームにすばやく移動したり、1 ページに複数のゲームを表示したりできます。

この場合も、Application Insights を使用して、別個の Web ページでゲームを実装していた場合とまったく同じように、それぞれのゲームが開かれた回数を記録したいとします。これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

	telemetryClient.trackPageView(game.Name);

## カスタム イベント

カスタム イベントを使用できます。カスタム イベントは、デバイスのアプリ、Web ページ、または Web サーバーから送信できます。

*JavaScript*

    telemetryClient.trackEvent("GameEnd");

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

タイムラインには、変化を他のメトリックやイベントに関連付けることができるという便利な機能があります。たとえば、より多くのゲームがプレイされているときは、破棄されたゲームの数も増えることが予想されます。しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## 特定のイベントのドリルダウン

一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

この例では、ユーザーが実際にゲームを開始せずにログアウトすると呼び出されるカスタム イベント "NoGame" を作成しています。ユーザーはなぜそのようなことをするのでしょうか。 いくつかの特定の発生箇所をドリルダウンすると、そのヒントが得られるでしょう。

アプリから受信したカスタム イベントは、概要ブレードに名前別に表示されます。


![概要ブレードで、いずれかのカスタム イベントの種類をクリックします。](./media/app-insights-web-track-usage/07-clickEvent.png)
 
目的のイベントをクリックし、最近の特定の発生箇所を選択します。


![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-web-track-usage/08-searchEvents.png)
 
ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。


![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。
 
このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。


![](./media/app-insights-web-track-usage/10-filter.png)
 
ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。おそらく、これを簡単にするようなユーザー ストーリーの開発を検討する必要があります (加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

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

ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。それぞれの効果を評価した後、統合バージョンに移行します。

この方法では、アプリの各バージョンから送信されるすべてのテレメトリに識別可能なタグをアタッチします。そのためには、アクティブな TelemetryContext でプロパティを定義します。これらの既定のプロパティは、アプリケーションによって送信されるすべてのテレメトリ メッセージに追加されます。つまり、カスタムのメッセージだけでなく、標準のテレメトリにも追加されます。

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

* アプリケーションの重要なメトリックを見極める。対象ユーザーはできる限り多数のユーザーですか。それとも少数のたいへん満足しているユーザーですか。 アクセス数または売上を最大にしたいと思いますか。
* 各ストーリーの評価を計画する。新しいユーザー ストーリーまたは機能の概略を考える場合、または既存のユーザー ストーリーまたは機能の更新を計画する場合は、変更の成功をどのように評価するかを常に考える必要があります。コーディングを始める前に、"これが動作したときにメトリックはどのような影響を受けるか。 すべての新しいイベントを追跡する必要があるか。" を常に考える必要があります。 もちろん、機能が稼動状態になったら、分析に注目し、その結果に基づいて対応します。 
* 他のメトリックを重要なメトリックに関連付ける。たとえば、"お気に入り" 機能を追加した場合は、ユーザーがお気に入りを追加する頻度を確認することになるでしょう。しかし、ユーザーがお気に入りに再びアクセスする頻度を知る方が、おそらくより興味深い結果が得られるでしょう。そして最も重要なことは、お気に入りを利用するユーザーに最終的により多くの製品を購入してもらえるかどうか、ということになります。
* カナリア テスト。新機能が一部のユーザーにのみ表示されるようにする機能スイッチを設定します。Application Insights を使用して、新機能が意図したように使用されるかどうかを確認します。調整を行った後、より多くのユーザーを対象にリリースします。
* ユーザーと話をする。 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。


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

 

<!---HONumber=August15_HO6-->