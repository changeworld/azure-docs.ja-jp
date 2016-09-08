<properties
    pageTitle="iOS アプリの分析 | Microsoft Azure"
    description="iOS アプリの使用状況とパフォーマンスを分析します。"
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/26/2016"
    ms.author="awills"/>

# iOS アプリの分析

デバイスの devOps に関してマイクロソフトでは、2 つのソリューションを提供しています。クライアント デバイス用の [HockeyApp](http://hockeyapp.net/) とサーバー側およびクライアント Web ページ用の [Application Insights](app-insights-overview.md) です。

[HockeyApp](http://hockeyapp.net/) は、iOS、OS X、Android、Windows デバイスのアプリ、および Xamarin、Cordova、Unity に基づくクロス プラットフォーム アプリを構築するためのモバイル開発運用ソリューションです。これを使用すると、ベータ テスト担当者にビルドを配布し、クラッシュ データを収集して、ユーザーからのフィードバックを得ることができます。このソリューションは Visual Studio Team Services と統合されており、ビルドのデプロイメントや作業項目の統合を簡単に行うことができます。


参照先:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [iOS 用 HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [HockeyApp のブログ](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/) に参加して最新リリースを入手する。

アプリにサーバー側がある場合は、[Application Insights](app-insights-overview.md) を使用して [ASP.NET](app-insights-asp-net.md) または [J2EE](app-insights-java-get-started.md) 上のアプリの Web サーバー側を監視できます。


## HockeyApp データに対する分析、エクスポート、API アクセス 

Application Insights で [HockeyApp ブリッジを設定](app-insights-hockeyapp-bridge-app.md)します。これを使用すると、次のことができます。

* テレメトリに対して強力な [Analytics](app-insights-analytics.md) クエリ言語を使用する。
* Azure BLOB ストレージに[テレメトリをエクスポート](app-insights-export-telemetry.md)する。

## 次のステップ

* [HockeyApp for iOS を使用](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)します。

<!---HONumber=AcomDC_0831_2016-->