<properties 
	pageTitle="Windows デバイスの Application Insights のトラブルシューティング" 
	description="Windows デバイスの Application Insights に関するトラブルシューティング ガイドと質疑応答です。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Windows デバイス用 Application Insights のトラブルシューティングおよび Q&A

[Windows で Visual Studio Application Insights][windows] を使用するうえでの疑問または問題はありませんか? ここでは、いくつかのヒントを紹介します。



## データが表示されない 

*Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません。*

* 少し待ってから、[最新の情報に更新] をクリックします。Currently, refresh isn't automatic.
* Check that you have an instrumentation key defined in the ApplicationInsights.config file, and that it is the same as the key in the Application Insights portal.キーを表示するには、概要ブレードで [Essentials] をクリックします。
* アプリが[外向きのネットワーク アクセスを要求すること](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)を確認します。
* エミュレーターまたはテスト デバイスと Application Insights ポータルの間にファイアウォールはありますか。 You might have to open TCP ports 80 and 443 for outgoing traffic to dc.services.visualstudio.com and f5.services.visualstudio.com.
* Microsoft Azure のスタート ボードで、サービス状態マップをご確認ください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。


#### データが表示されていたのに停止しました。

* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)をご確認ください。
* データ ポイントの月間クォータに達していませんか? Open Settings/Quota and Pricing to find out.上限に達している場合は、プランをアップグレードするか、追加容量分を購入することができます。「[料金プラン](http://azure.microsoft.com/pricing/details/application-insights/)」をご覧ください。


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

* Create two Application Insights resources (with different instrumentation keys), for the client and the server.But create them in the same Azure resource group.That makes it easy to switch between one and the other.
* Use one Application Insights resource and put its instrumentation key into both client and server projects.Then you can correlate metrics and events from the two sources.

To help correlate events in the client and server, generate an operation id for each request.それをクライアントとサーバーの間で送信し、両方の端で利用統計情報に追加します。

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
[track]: app-insights-api-custom-events-metrics.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->