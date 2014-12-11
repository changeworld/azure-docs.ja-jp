<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="Application Insights を使用した Web アプリケーションにおける使用状況イベントおよびメトリックの追跡" description="Insert a few lines of code to find out what users are doing with your website." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# Web アプリケーションにおけるカスタムの利用状況イベントおよびメトリックの追跡

*Application Insights はプレビュー段階です。*

Web アプリケーションにコードを数行挿入し、アプリケーションでユーザーが行っている操作を確認します。イベント、メトリック、およびページ ビューを追跡できます。すべてのユーザーを対象に集計したデータのグラフとテーブルが表示されます。 

> [AZURE.NOTE] 現在、ユーザー エクスペリエンスがすべて実現されているわけではありません。カスタム イベントおよびメトリックを Application Insights に送信し、未加工の利用統計情報を[診断検索][diagnostic]で検索できます。ただし、要約された統計的なグラフは、まだ参照できません。近日、提供する予定です。

<!-- Sample pic -->

* [クライアントとサーバーの追跡](#clientServer)
* [開始する前に](#prep)
* [メトリックを追跡する](#metrics)
* [イベントを追跡する](#events)
* [ページ ビューを追跡する](#pageViews)
* [プロパティを使用してデータをフィルター処理、検索、および分割する](#properties)
* [メトリックとイベントを結合する](#measurements)
* [既定のプロパティ値を設定する](#defaults)
* [複数のコンテキストを定義する](#contexts)
* [利用統計情報のオンとオフを切り替える](#disable)
* [次のステップ](#next)



## <a name="clientServer"></a> クライアントとサーバーの追跡

アプリケーションのクライアント (Web ページ) 側またはサーバー側のどちらか、または両方から利用統計情報を送信できます。

クライアント API とサーバー API は、よく似ています。ユーザーの Web ブラウザーと使用中の Web サーバーの両方から、同じ種類の利用統計情報を送信できます。違いは、送信できるデータの範囲にあります。

* Web クライアントでの追跡が特に役立つのは、JavaScript を多く備えた、機能が豊富でアクティブな Web ページがある場合です。たとえば、ユーザーが特定のボタンをクリックする頻度や検証エラーが発生する回数を監視できます。
* Web サーバーでの追跡は、顧客の購入価格や破棄された注文数などのビジネス メトリックとイベントを監視するのに向いています。

一般的な ASP.NET Web アプリケーションでは、trackPageView() への既定の JavaScript 呼び出しが Web マスター ページに用意されており、サーバー コードにイベントとメトリックを追跡する呼び出しをいくつか追加します。クライアント側のコードの機能が豊富な場合は、クライアント側にもイベントとメトリックを追跡する呼び出しをいくつか追加します。


## <a name="prep"></a>開始する前に

次のことをまだ実行していない場合は、実行します。

* ASP.NET Web アプリケーションからの利用統計情報の取得:
    [アプリケーションの正常性と利用状況の監視][greenbrown]
    Web サーバー コードに次を含めます。
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Web 利用状況分析の設定][usage]JavaScript の初期化コードを、監視コードを記述するすべての Web ページに、またはマスター ページに含める必要があります。 
    このコードが動作している場合は [利用状況分析] の下の [概要] ブレードにデータが表示されます。

開発用コンピューター上でアプリケーションをデバッグ モードで実行すると、結果は Application Insights に数秒以内に表示されます。アプリケーションをデプロイするときに、データは、サーバーとクライアントからパイプラインに移動するのに長い時間がかかります。

<!--
## <a name="metrics"></a> メトリックを追跡する

ページ ビューなどの基本的な利用状況データを取得するために、これ以上操作する必要はありません。ただし、数行のコードを記述することで、ユーザーがアプリケーションで行っている操作について詳細を知ることができます。

たとえば、アプリケーションがゲームである場合、ユーザーが獲得する平均スコアを確認し、新しいバージョンのリリース後にユーザーにとってのゲームの難易度を知ることができます。

メトリック (スコアのような数値) を追跡するには、アプリケーションの適切な場所に次のようなスクリプトを 1 行挿入します。

クライアント側の JavaScript

    appInsights.trackMetric("Alerts", notifications.Count);

サーバー側の C#

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

サーバー側の VB

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

アプリケーションをテストして、trackMetric() 呼び出しを実行するためにこのコードを使用します。


次に Application Insights でアプリケーションに移動しアクセスし、[[メトリック]][metrics] タイルをクリックしてください。メトリックを選択して、最初の結果を表示します。


グラフは、すべてのユーザーが記録した値の最近の平均を示します。 


(注: メトリックを最適化して、問題を診断することはできません。必要な場合は、[診断ログ][diagnostic]を参照してください。) -->


## <a name="events"></a>イベントを追跡する

イベントは、ユーザー全体で平均した発生頻度を示します。たとえば、ユーザーがゲームを完了する頻度を知りたいとします。ゲームを終了するコードに、次のように行を挿入します。

クライアント側の JavaScript

    appInsights.trackEvent("EndOfGame");

サーバー側の C#
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

サーバー側の VB


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

クライアントとサーバーの両方から利用統計情報を送信する場合は、必ずイベントごとに異なる名前を付けてください。


## <a name="pageViews"></a>ページ ビュー (クライアントのみ)

既定では、Web ページの先頭にある初期化スクリプトにより、ページ ビューが記録され、ページの相対 URL の名前がイベントに付けられます。これらの呼び出しは、基本的なページ利用状況の統計を提供します。 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### カスタム ページ データ

必要な場合は、名前を変更する呼び出しを変更できます。または追加の呼び出しを挿入できます。たとえば、単一ページの Web アプリケーションに複数のタブが表示される場合、ユーザーが別のタブに切り替えるときに、ページ ビューを記録できます。次に例を示します。

クライアント側の JavaScript:

    appInsights.trackPageView("tab1");

別々の HTML ページ内で複数のタブを使用している場合、URL を指定することもできます。

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>プロパティを使用してデータをフィルター処理、検索、および分割する

プロパティと測定値を、イベント、ページ ビュー、およびその他の利用統計情報データに結び付けることができます。 

**プロパティ**は、利用状況レポートで利用統計情報をフィルター処理するのに使用できる文字列値です。たとえば、アプリケーションで複数のゲームを提供する場合、各イベントにゲームの名前を結び付けると、人気のあるゲームを確認できます。

**測定値**は、利用状況レポートで統計の取得元になる数値です。


クライアント側の JavaScript

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

サーバー側の C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


サーバー側の VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


次のようにページ ビューを同じ方法でプロパティに結び付けます。

クライアント側の JavaScript

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] プロパティで個人を特定できる情報を記録しないように注意します。


## タイミングを指定したページ ビューとイベント

イベントとページ ビューに、タイミングを合わせたデータを結び付けることができます。trackEvent または trackPageView を呼び出すのではなく、次の呼び出しを使用します。

クライアント側の JavaScript

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

開始の呼び出しと停止の呼び出しで、最初のパラメーターに同じ文字列を使用します。

## <a name="defaults"></a>既定のプロパティ値を設定する (Web クライアント側でない)

TelemetryContext に、既定値を設定できます。既定値は、コンテキストから送られてくるすべてのメトリックとイベントに結び付けられます。 
    

サーバー側の C#

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
サーバー側の VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
個々の利用統計情報は、既定値を無視することができます。

既定のプロパティ値のグループを切り替える場合は、複数のコンテキストを設定します。



## <a name="next"></a>次のステップ


[イベントおよびログを検索する][diagnostic]

[トラブルシューティング][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




