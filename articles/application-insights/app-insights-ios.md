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
    ms.date="11/17/2015"
    ms.author="awills"/>

# iOS アプリの分析

デバイスの開発運用のために 2 つのソリューションが提供されています。開発運用ワークフローおよびクラッシュ分析用の [HockeyApp](http://hockeyapp.net/) と、使用状況およびクラッシュ分析用の [Application Insights](app-insights-overview.md) です。

[HockeyApp](http://hockeyapp.net/) は、iOS、OS X、Android、Windows デバイスのアプリ、および Xamarin、Cordova、Unity に基づくクロス プラットフォーム アプリを構築するためのモバイル開発運用ソリューションです。これを使用すると、ベータ テスト担当者にビルドを配布し、クラッシュ データを収集して、ユーザーからのフィードバックを得ることができます。このソリューションは Visual Studio Team Services と統合されており、ビルドのデプロイメントや作業項目の統合を簡単に行うことができます。詳細については [HockeyApp のナレッジ ベース](http://support.hockeyapp.net/kb)を参照し、[HockeyApp のブログ](http://hockeyapp.net/blog/)に常に注目してください。


使用状況の分析には [Application Insights SDK for iOS](https://github.com/Microsoft/ApplicationInsights-iOS) を使用します。アプリにサーバー側がある場合は、同じ Application Insights リソースにクライアントとサーバーのデータを送信できます。


* [Application Insights for iOS を使ってみる](https://github.com/Microsoft/ApplicationInsights-iOS)。
* [HockeyApp for Android を使ってみる](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios)。

<!---HONumber=AcomDC_1203_2015-->