---
title: "Azure Application Insights による Web アプリケーションの利用状況分析 | Microsoft Docs"
description: "Application Insights による利用状況分析の概要"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Application Insights による Web アプリケーションの利用状況の分析
ユーザーによるアプリケーションの利用状況を把握することで、ユーザーにとって最も重要なシナリオに重点を置いて開発作業を行うことができ、ユーザーがより実現しやすい、または実現しにくいと考えている目標について情報を得ることもできます。

[Azure Application Insights](app-insights-overview.md) では、2 つのレベルで利用状況を追跡できます。

* **ユーザー、セッション、およびページ ビュー データ** - 既定で提供されています。  
* **カスタム テレメトリ** - アプリのユーザー エクスペリエンスを通してユーザーを追跡する[コードを記述](app-insights-api-custom-events-metrics.md)します。 


## <a name="get-started"></a>作業開始

Application Insights をアプリのサーバー コードと Web ページの両方にインストールすることにより、最適な操作環境が得られます。 アプリのクライアントおよびサーバー コンポーネントから Azure Portal に分析用のテレメトリが送信されます。

1. **サーバー コード:** [ASP.NET](app-insights-asp-net.md)、[Azure](app-insights-azure.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md)、または[その他](app-insights-platforms.md)のアプリ向けの適切なモジュールをインストールします。

    * "*サーバー コードをインストールしたくない場合は、[Azure Application Insights リソースの作成](app-insights-create-new-resource.md)のみを行ってください。*"

2. **Web ページ コード:** [Azure Portal](https://portal.azure.com) でアプリの Application Insights リソースを開いてから、**[はじめに]、[クライアント側アプリケーションの監視と診断]** の順に開きます。 

    ![マスター Web ページの先頭にスクリプトをコピーします。](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **テレメトリの取得:** プロジェクトをデバッグ モードで数分間実行し、Application Insights の [概要] ブレードで結果を確認します。

    アプリを発行し、アプリのパフォーマンスを監視してユーザーがアプリを使って何をしているか確認します。

## <a name="usage-analysis-out-of-the-box"></a>既定の利用状況分析
[使用状況] ブレードを開きます。

![ユーザー、セッション、ページ ビューの各グラフ](./media/app-insights-web-track-usage/14-usage.png)

グラフ上の空白部分にマウス ポインターを移動すると、特定のポイントにおける数が表示されます。 それ以外の場合は、特定の期間にわたって集計された値 (平均、合計、期間における一意のユーザーの数など) が表示されます。

各グラフに示されているデータの意味は、以下のとおりです。

* **ユーザー:** グラフの時間範囲内で変化する別個のアクティブ ユーザーの数。 Web アプリケーションでは、ユーザーは Cookie を使用してカウントされます。 複数のブラウザーを使用しているユーザー、Cookie を消去しているユーザー、またはプライバシー機能を使用しているユーザーは、複数回カウントされます。
* **認証されたユーザー**: コード内に [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) の呼び出しを挿入した場合にカウントされます。
* **セッション:** アクティブなセッションの数。 Web セッションは、非アクティブな状態が 30 分続いた後でカウントされます。 
* **ページ ビュー**: trackPageView() の呼び出し数をカウントします。通常、各 Web ページで 1 回呼び出されます。

グラフをクリックすると、詳細が表示されます。 グラフの時間範囲を変更できます。

### <a name="where-do-my-users-live"></a>ユーザーの居住地を調べる
[ユーザー] グラフの空白部分をクリックすると、別のブレードが開いて詳細が表示されます。

![[使用状況] ブレードで [ユーザー] グラフをクリックします。](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>使用されているブラウザーまたはオペレーティング システムを調べる

ユーザー グラフで **[編集]** をクリックし、[ブラウザー]、[オペレーティング システム]、[市区町村] などのプロパティ別にデータをグループ化 (セグメント化) します。 

![1 つのメトリックを表示するグラフを選択します。[グループ] をオンにし、プロパティを選択します](./media/app-insights-web-track-usage/03-browsers.png)

カウントされた数をすべて表示するには、グラフの種類を **[グリッド]** に切り替えます。 また、追加のメトリックを選択して表示することもできます。

![複数列のグリッド グラフ](./media/app-insights-web-track-usage/multi-column-grid.png)

グラフィカルな種類のグラフでは、プロパティ別のグループ化か複数のメトリックの選択のいずれかの操作が可能で、両方を同時に行うことはできません。 次のグラフでは、ユーザーと[認証されたユーザー](app-insights-api-custom-events-metrics.md#authenticated-users)という 2 つのメトリックを比較しています。 

![グラフを選択します。メトリックを検索し、オンまたはオフにします。](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>ページ ビュー
[使用状況] ブレードでページ ビュー グラフをクリックすると、その詳細バージョンと、最も人気のあるページの内訳が表示されます。

![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-web-track-usage/05-games.png)

上の例はゲーム関連の Web サイトの情報を示したグラフです。 このグラフから、すぐに以下のような情報を読み取ることができます。

* 利用状況はこの 1 週間改善されていません。 おそらく、検索エンジンの最適化について検討する必要があります。
* テニスが最も人気のあるゲーム ページです。 このページを重点的に改善しましょう。
* 平均すると、ユーザーは 1 週間に約 3 回テニス ページにアクセスしています。 (セッション数がユーザー数の約 3 倍になっています)。
* ほとんどのユーザーは、米国の就業日かつ就業時間内にこのサイトにアクセスしています。 Web ページ上に "すばやく隠す" ボタンを用意する必要があるかもしれません。
* グラフ上の[注釈](app-insights-annotations.md)は、Web サイトの新しいバージョンがデプロイされた日時を示しています。 最近行われたデプロイのうち、利用状況に顕著な影響を与えたものはありません。

## <a name="custom-tracking"></a>カスタムの追跡
それぞれのゲームを別個の Web ページで実装する代わりに、ほとんどの機能が Web ページの JavaScript としてコーディングされた同じ単一ページ アプリにこれらのゲームをすべてリファクタリングするとします。 これにより、ユーザーは、ゲームをすばやく切り替えたり、1 ページ上に複数のゲームを表示したりできます。

ただし、Application Insights では、各ゲームが開かれた回数をログに記録する際、ゲームが個別の Web ページにある場合とまったく同じ方法で行います。 これを実現するのは簡単です。新しい "ページ" が開かれたことを記録する JavaScript コードにテレメトリ モジュールの呼び出しを挿入するだけです。

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
この呼び出しにより、ページ ビューを記録するテレメトリがシミュレートされます。  ただし、ページ ビューとメッセージを一緒にしたくない場合もあります。 その場合は、カスタム イベントを使用します。 カスタム イベントは、Web ページや Web サーバーから送信できます。


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

[Web またはサーバー コードに挿入したカスタム テレメトリ](app-insights-api-custom-events-metrics.md#trackevent)は、アプリケーションの利用状況を把握するためにさまざまな方法で使用できます。

TrackEvent() によって送信されたイベントを表示するには、メトリックス エクスプローラーを開き、新しいグラフを追加して、それを編集します。 メトリックは [カスタム メトリック] の下に表示されます。 

![カスタム イベントを示すグラフ。](./media/app-insights-web-track-usage/06-eventsSegment.png)

イベント内の[プロパティ値](app-insights-api-custom-events-metrics.md#properties) (ディメンションとも呼ばれます) を設定すると、そのプロパティ値によってグループ化とフィルター処理を行うことができます。

複数のグラフを作成し、他のメトリックやイベントにおける変化の関連性を探ります。 たとえば、プレイするゲームが多くなると、破棄されたゲームも多くなると予想されます。 しかし、破棄されたゲーム数の増加率には偏りがあります。そこで、高い負荷が原因となってユーザーが許容できない問題が発生しているかどうかを確認してみましょう。

## <a name="drill-into-specific-events"></a>特定のイベントのドリルダウン
一般的なセッションの状況についてより深く理解するためには、特定の種類のイベントが含まれている特定のユーザー セッションに注目します。

イベントのグリッドで目的のイベントをクリックし、最近の特定の発生箇所を選択します。

![概要グラフの下の一覧で、イベントをクリックします](./media/app-insights-web-track-usage/08-searchEvents.png)

ここで、この特定の NoGame イベントが発生したセッションのすべてのテレメトリを見てみましょう。

![[セッションのすべてのテレメトリ] をクリックします](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

例外はありません。ここから、ユーザーのプレイを妨げるような障害が発生していないことがわかります。

このセッションのページ ビューを除くすべての種類のテレメトリをフィルターで除外できます。

![](./media/app-insights-web-track-usage/10-filter.png)

ここから、このユーザーは最新のスコアを確認するためにのみログオンしたことがわかります。 スコアの確認を簡単にするユーザー ストーリーの作成を検討することが必要である可能性があります  (加えて、この特定のストーリーが発生したことを報告するカスタム イベントを実装する必要があります)。

## <a name="filter-search-and-segment-your-data-with-properties"></a>プロパティを使用してデータをフィルター処理、検索、および分割する
イベントには任意のタグと数値をアタッチできます。

"*Web ページの JavaScript*"

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

"*Web ページの JavaScript*"

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

診断検索で、イベントのそれぞれの発生箇所をクリックしてプロパティを表示します。

![イベントの一覧で、イベントを開き、[...] をクリックして その他のプロパティを表示します](./media/app-insights-web-track-usage/11-details.png)

[検索] ボックスを使用して、特定のプロパティ値を持つイベントを表示します。

![[検索] ボックスに値を入力します](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>テレメトリに対するクエリを編集および作成する

任意のグラフの Azure Analytics アイコンをクリックし、そのグラフに相当する編集可能なクエリを開きます。
下へスクロールし、複数のクエリが生成されているかどうかを確認します。 いずれかのクエリにカーソルを置き、**[移動]** をクリックします。 

または、[概要] ブレードにあるアイコンから Analytics を開き、独自のクエリを記述するか、[Analytics Home (Analytics ホーム)] タブでいくつかのサンプル クエリを試してみることもできます。


![生成済みのクエリが表示されている Analytics ウィンドウ](./media/app-insights-web-track-usage/open-analytics.png)

[こちら](app-insights-analytics.md)から、Azure Analytics クエリ言語の詳細を確認してください。

利用状況の分析には、特に以下のテーブルが役立ちます。

* `customEvents` - [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 呼び出しの結果。
* `pageViews` - クライアント ブラウザーで開かれたページの数、または [trackPageView()](app-insights-api-custom-events-metrics.md#page-views) の呼び出し回数。


## <a name="a--b-testing"></a>A | B テスト
ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。 各バージョンの成功の度合いを測定してから、統合したバージョンに移行します。

この手法では、アプリの各バージョンから送信されるすべてのテレメトリに異なるタグをアタッチします。 これは、アクティブな TelemetryContext のプロパティを定義することで実行できます。 このような既定のプロパティは、カスタム メッセージだけでなく、標準のテレメトリも同様に、アプリケーションから送信されるすべてのテレメトリ メッセージに追加されます。

Application Insights ポータルでは、タグに基づいてデータをフィルター選択およびグループ化 (分割) して、異なるバージョンを比較できます。


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


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
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

アプリ初期化子 (Global.asax.cs など) 内:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>ビルド、評価、学習
分析は、単に問題の解決に役立つだけのものではなく、開発サイクルの重要な部分を構成します。 いくつかのヒントを次に示します。

* アプリケーションの主なメトリックを決定する。 できるだけ多くのユーザーにプレイしてもらいたいのか、それとも、少数のユーザーに十分満足してもらえればいいのか。 アクセス数と売り上げのどちらを最重要視するのか、を検討します。
* 各ストーリーの評価を計画する。 新しいユーザー ストーリーや機能の概要を作成する場合、または既存のユーザー ストーリーや機能の更新を計画する場合は、変更の成功を評価する方法について必ず検討してください。 コーディングの開始前に、"成功した場合に、メトリックにどのような影響があるか。 新しいイベントを追跡する必要があるか」 を考えます。
  当然ながら、機能が実装されたら、分析に目を向け、その結果に基づいて行動するようにしてください。
* 他のメトリックを重要なメトリックに関連付ける。 たとえば、"お気に入り" 機能を追加した場合は、ユーザーがお気に入りを追加する頻度を確認することになるでしょう。 しかし、ユーザーがお気に入りに再びアクセスする頻度を知る方が、おそらくより興味深い結果が得られるでしょう。 さらに、最も重要なのは、お気に入りを使用する顧客が最終的により多くの製品を購入してくれるかどうかです。
* カナリア テストを実行する。 新しい機能を一部のユーザーのみに表示できるようにする機能スイッチをセットアップします。 Application Insights を使用して、その新しい機能が予想どおりに使用されているかどうかを確認してください。 調整後に、対象ユーザーの範囲を広げてリリースします。
* ユーザーと会話をする。 分析は、それだけでは不十分ですが、良好な顧客関係を保つための補完的役割を果たします。

## <a name="learn-more"></a>詳細情報
* [Application Insights を使用した検出、トリアージ、診断](app-insights-detect-triage-diagnose.md)
* [さまざまなプラットフォームで Application Insights を使ってみる](app-insights-detect-triage-diagnose.md)
* [API の使用 - 概要](app-insights-api-custom-events-metrics.md)
* [JavaScript API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



