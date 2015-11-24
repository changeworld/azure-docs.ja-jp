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
    ms.date="11/15/2015"
    ms.author="awills"/>

# iOS アプリの分析

Visual Studio Application Insights を使用すると、使用状況、イベント、およびクラッシュに関してモバイル アプリケーションを監視できます。


> [AZURE.NOTE]クラッシュ レポートの取得、分析、配布、およびフィードバックの管理には、[HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) をお勧めします。

## 必要条件

必要なものは次のとおりです。

* [Microsoft Azure](http://azure.com) のサブスクリプションWindows、XBox Live、またはその他の Microsoft クラウド サービスで所有している Microsoft アカウントを使用してサインインします。
* Xcode 6 以降。
* SDK は iOS 6.0 以降がインストールされているデバイスで実行されます。

## Application Insights リソースの作成

[Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。iOS オプションを選択します。

![[新規]、[開発者向けサービス]、[Application Insights] の順にクリックする](./media/app-insights-ios/11-new.png)

表示されるブレードには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。あるいは、[参照] ボタンをクリックして探します。

## セットアップ

[セットアップ ガイド](https://github.com/Microsoft/ApplicationInsights-iOS#setup)に従って、アプリに SDK をインストールしてください。

## Application Insights でデータを表示する

アプリを実行して、テレメトリを生成します。

http://portal.azure.com に戻り、Application Insights リソースを参照します。

**[検索]** をクリックして [[診断検索]][diagnostic] を開きます。ここには、最初のイベントが表示されます。何も表示されない場合は 1 ～ 2 分待機し、**[更新]** をクリックします。

![[診断検索] をクリックする](./media/app-insights-ios/21-search.png)

アプリケーションを使用すると、データが [概要] ブレードに表示されます。

![[概要] ブレード](./media/app-insights-ios/22-oview.png)

詳細情報を表示するグラフをクリックします。たとえば、クラッシュの場合は、次のようになります。

![クラッシュのグラフをクリックする](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][track]

[診断検索][diagnostic]

[メトリックス エクスプローラー][metrics]

[トラブルシューティング][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->