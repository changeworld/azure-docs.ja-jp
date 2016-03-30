<properties
	pageTitle="Windows Phone アプリとストア アプリの分析"
	description="Windows デバイス アプリの使用状況とクラッシュを分析します。"
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/17/2016"
	ms.author="awills"/>

# Windows Phone アプリとストア アプリの分析

デバイスの devOps に関してマイクロソフトでは、2 つのソリューションを提供しています。クライアント側の分析を目的とした [HockeyApp](http://hockeyapp.net/) とサーバー側の分析を目的とした [Application Insights](app-insights-overview.md) です。

[HockeyApp](http://hockeyapp.net/) は、iOS、OS X、Android、Windows デバイスのアプリ、および Xamarin、Cordova、Unity に基づくクロス プラットフォーム アプリ用のモバイル開発運用ソリューションです。これを使用すると、ベータ テスト担当者にビルドを配布し、クラッシュ データを収集して、ユーザーからのフィードバックやメトリックを得ることができます。このソリューションは Visual Studio Team Services と統合されており、ビルドのデプロイメントや作業項目の統合を簡単に行うことができます。

参照先:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Windows 用 HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp のブログ](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/) に参加して最新リリースを入手する。

アプリにサーバー側がある場合は、[Application Insights](app-insights-overview.md) を使用して [ASP.NET](app-insights-asp-net.md) または [J2EE](app-insights-java-get-started.md) 上のアプリの Web サーバー側を監視できます。


[Windows デスクトップ アプリ用の Application Insights](app-insights-windows-desktop.md) を使用することもできます。

> [AZURE.NOTE] 2016 年 6 月 15 日をもって、Application Insights での iOS アプリ、Android アプリ、Windows ストア アプリ、および Windows Phone アプリのデータの表示を停止します。
> 
> [この変更の詳細については、こちらをご覧ください](https://azure.microsoft.com/blog/transitioning-mobile-apps-from-application-insights-to-hockeyapp/)。

<!---HONumber=AcomDC_0323_2016-->