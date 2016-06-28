<properties
    pageTitle="iOS アプリの分析"
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
    ms.date="06/20/2016"
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

> [AZURE.NOTE] 間もなく、HockeyApp SDK から送信されたテレメトリで [Application Insights Analytics](app-insights-analytics.md) と[連続エクスポート](app-insights-export-telemetry.md)を使用できるようになります。
>
> [この変更の詳細については、こちらをご覧ください。](https://azure.microsoft.com/blog/update-on-transitioning-mobile-apps-from-application-insights-to-hockeyapp/)

<!---HONumber=AcomDC_0622_2016-->