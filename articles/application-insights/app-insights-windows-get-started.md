<properties
	pageTitle="Windows Phone アプリとストア アプリの分析 | Microsoft Azure"
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
	ms.date="08/26/2016"
	ms.author="awills"/>

# Windows Phone アプリとストア アプリの分析

デバイスの devOps に関してマイクロソフトでは、2 つのソリューションを提供しています。クライアント側の分析を目的とした [HockeyApp](http://hockeyapp.net/) とサーバー側の分析を目的とした [Application Insights](app-insights-overview.md) です。

[HockeyApp](http://hockeyapp.net/) は、iOS、OS X、Android、Windows デバイスのアプリ、および Xamarin、Cordova、Unity に基づくクロス プラットフォーム アプリ用のモバイル開発運用ソリューションです。これを使用すると、ベータ テスト担当者にビルドを配布し、クラッシュ データを収集して、ユーザーからのフィードバックやメトリックを得ることができます。このソリューションは Visual Studio Team Services と統合されており、ビルドのデプロイメントや作業項目の統合を簡単に行うことができます。

## HockeyApp の概要

参照先:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Windows 用 HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp のブログ](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/) に参加して最新リリースを入手する。

アプリにサーバー側がある場合は、[Application Insights](app-insights-overview.md) を使用して [ASP.NET](app-insights-asp-net.md) または [J2EE](app-insights-java-get-started.md) 上のアプリの Web サーバー側を監視できます。


[Windows デスクトップ アプリ用の Application Insights](app-insights-windows-desktop.md) を使用することもできます。

## HockeyApp データに対する分析、エクスポート、API アクセス 

Application Insights で [HockeyApp ブリッジを設定](app-insights-hockeyapp-bridge-app.md)します。これを使用すると、次のことができます。

* テレメトリに対して強力な [Analytics](app-insights-analytics.md) クエリ言語を使用する。
* Azure BLOB ストレージに[テレメトリをエクスポート](app-insights-export-telemetry.md)する。

## 次のステップ

* [HockeyApp for Windows を使用](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)します。

<!---HONumber=AcomDC_0831_2016-->