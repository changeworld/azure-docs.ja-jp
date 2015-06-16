<properties 
	pageTitle="Windows デバイスの Application Insights のトラブルシューティング" 
	description="トラブルシューティング ガイド、質問と回答" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Windows デバイス用 Application Insights のトラブルシューティングおよび Q&A

[Windows で Visual Studio Application Insights][windows] を使用するうえでの疑問または問題はありませんか? ここでは、いくつかのヒントを紹介します。



## データが表示されない 

*Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません。*

* 少し待ってから、[最新の情報に更新] をクリックします。現時点では、自動での更新は行われません。
* ApplicationInsights.config ファイルにインストルメンテーション キーが定義されていることと、それが Application Insights ポータルのキーと同じであることを確認します。キーを表示するには、概要ブレードで [Essentials] をクリックします。
* アプリが[外向きのネットワーク アクセスを要求すること](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)を確認します。
* エミュレーターまたはテスト デバイスと Application Insights ポータルの間にファイアウォールはありますか。 場合によっては、dc.services.visualstudio.com と f5.services.visualstudio.com にトラフィックを送るために TCP ポート 80 と 443 を開く必要があります。
* Microsoft Azure のスタート ボードで、サービス状態マップを確認してください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。

#### データが表示されていたのに停止しました。

* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)を確認してください。


## ユニバーサル アプリに Application Insights を追加する方法は?

ソリューションの各デバイス プロジェクトに NuGet パッケージを手動で追加します。「[概要 - ユニバーサル アプリ][universal]」を参照してください。

## テレメトリの無効化

*テレメトリの収集を無効にする方法を教えてください。*

コード内で以下のように指定します。

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## ターゲット リソースの変更

*自分のプロジェクトがデータを送信する Application Insights のリソースを変更するにはどうすればいいですか?*

新しい Application Insights の概要ブレードで、[Essentials] を開き、インストルメンテーション キーをコピーします。

`<InstrumentationKey>` ノードの ApplicationInsights.config ファイルにキーを貼り付けます。

あるいは、実行時にターゲットを変更する場合、次を使用します。

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## クライアント サーバー アプリケーションの監視方法は?

これを行うには 2 つの方法があります。

* クライアントとサーバーに (異なるインストルメンテーション キーで) 2 つの Application Insights リソースを作成します。ただし、同じ Azure リソース グループで作成します。これで互いの切り替えが簡単になります。
* 1 つの Application Insights リソースを使用し、クライアント プロジェクトとサーバー プロジェクトの両方にそのインストルメンテーション キーを配置します。次に、2 つのリソースのメトリックとイベントを相互に関連付けることができます。

クライアントとサーバーのイベントを相互関連付けに役立てるために、要求別の操作 ID を生成します。それをクライアントとサーバーの間で送信し、両方の端で利用統計情報に追加します。

    telemetry.Context.OperationId = opid;


## Azure のスタート画面

*[Azure ポータル](http://portal.azure.com)を表示しています。このマップから、自分のアプリについて何か情報が得られるのでしょうか?*

いいえ、そのマップは世界中の Azure サーバーの正常性を表しています。

*Azure のスタート画面 (ホーム画面) から、アプリに関するデータを見つける方法を教えてください。*

[Application Insights 用にアプリを設定してある][windows]と仮定します。[参照] をクリックし、[Application Insights] を選択して、アプリ用に作成したリソースを選択します。次からその場所にすばやくアクセスできるように、スタート画面にリソースをピン留めすることができます。

## データの保持 

*ポータルでのデータ保持期間はどのくらいですか? セキュリティで保護されていますか?*

[データの保持とプライバシー][data]に関するページを参照してください。

## 次のステップ

*Java サーバー アプリ用に Application Insights を設定しました。他には何ができるか教えてください。*

* [Web ページの可用性の監視][availability]
* [Web ページの使用状況の監視][usage]
* [デバイス アプリの使用状況を追跡し、問題を診断する][platforms]
* [アプリの使用状況を追跡するためのコードを記述する][track]
* [診断ログのキャプチャ][javalogs]


## 問い合わせ

* [スタック オーバーフロー](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58--> 