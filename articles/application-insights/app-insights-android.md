<properties
    pageTitle="Android アプリの分析 | Microsoft Azure"
    description="Android アプリの使用状況とパフォーマンスを分析します。"
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="08/26/2016"
    ms.author="awills"/>

# Android アプリの分析

デバイスの devOps に関してマイクロソフトでは、2 つのソリューションを提供しています。クライアント側の分析を目的とした [HockeyApp](http://hockeyapp.net/) とサーバー側の分析を目的とした [Application Insights](app-insights-overview.md) です。

[HockeyApp](http://hockeyapp.net/) は、iOS、OS X、Android、Windows デバイスのアプリ、および Xamarin、Cordova、Unity に基づくクロス プラットフォーム アプリ用のモバイル開発運用ソリューションです。これを使用すると、ベータ テスト担当者にビルドを配布し、クラッシュ データを収集して、ユーザーからのフィードバックを得ることができます。このソリューションは Visual Studio Team Services と統合されており、ビルドのデプロイメントや作業項目の統合を簡単に行うことができます。

参照先:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp for Android を使ってみる](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)。
* [HockeyApp のブログ](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/) に参加して最新リリースを入手する。

アプリにサーバー側がある場合は、[Application Insights](app-insights-overview.md) を使用して [ASP.NET](app-insights-asp-net.md) または [J2EE](app-insights-java-get-started.md) 上のアプリの Web サーバー側を監視できます。


## HockeyApp データに対する分析、エクスポート、API アクセス 

Application Insights で [HockeyApp ブリッジを設定](app-insights-hockeyapp-bridge-app.md)します。これを使用すると、次のことができます。

* テレメトリに対して強力な [Analytics](app-insights-analytics.md) クエリ言語を使用する。
* Azure BLOB ストレージに[テレメトリをエクスポート](app-insights-export-telemetry.md)する。

## 次のステップ

* [HockeyApp for Android を使用](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)します。

<!---HONumber=AcomDC_0831_2016-->