<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# アプリケーションの正常性と利用状況の監視

*Application Insights はプレビュー段階です。*

アプリケーションが正常に動作していてユーザーを満足させていることを確認します。Application Insights は、パフォーマンスに関するあらゆる問題を通知して、その原因の発見と診断を支援します。Application Insights では、ユーザーのアクティビティをトレースできるため、ユーザーのニーズを満たすようにアプリケーションを調整することが可能になります。

Application Insights では、Windows Phone アプリケーション、Windows ストア アプリケーション、および Microsoft Azure Web サイトに加えて内部環境または仮想マシンでホストされている Web アプリケーションを監視できます。

[Visual Studio Update 3][Visual Studio Update 3] と [Microsoft Azure][Microsoft Azure] のアカウントが必要です (無料のお試し期間が用意されています)。

1.  [Application Insights の追加](#add)
-   [プロジェクトの実行][プロジェクトの実行]
-   [監視データの表示][監視データの表示]
-   [アプリケーションのデプロイ][アプリケーションのデプロイ]
-   [次のステップ][次のステップ]

*また、再デプロイしたり Visual Studio を使用したりすることなく既存の Web サービスを監視する場合は、[エージェントをサーバーにインストール][エージェントをサーバーにインストール]します。*

## <a name="add"></a>1. Application Insights の追加

### 新しいプロジェクトの場合

Visual Studio 2013 で新しいプロジェクトを作成するときに、Application Insights が選択されていることを確認します。

![Create an ASP.NET project][Create an ASP.NET project]

初めてこの操作を行う場合は、Microsoft Azure プレビューのログイン情報を入力するかまたはサインアップするよう求められます (このアカウントは Visual Studio Online アカウントとは異なります)。

> Application Insights のオプションが見つからない場合は、Visual Studio 2013 Update 3 を使用していること、および Web、Windows ストア、または Windows Phone のいずれかのプロジェクトを作成していることを確認してください。

### ... 既存のプロジェクトの場合

Application Insights を新しいプロジェクトに追加するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。

![Choose Add Application Insights][Choose Add Application Insights]

## <a name="run"></a>2. プロジェクトの実行

F5 キーを押してアプリケーションを実行し、いろいろなページを開いてみてください。

Visual Studio に、受け取ったイベントの数が表示されます。

![][]

## <a name="monitor"></a>3. 監視データの表示

Application Insights をプロジェクトから開きます。

![Right-click your project and open the Azure portal][Right-click your project and open the Azure portal]

[アプリケーションの健全性] タイルと [利用状況分析] タイルのデータを確認します。次に例を示します。

![Click through to more data][Click through to more data]

任意のタイルをクリックして詳細を表示します。[タイルとレポートの詳細][タイルとレポートの詳細]

> [WACOM.NOTE] このプレビュー バージョンでは、多くのタイルで詳細情報が一部しか表示されません。

## <a name="deploy"></a>4. アプリケーションのデプロイ

アプリケーションをデプロイして、データが蓄積されるのを待ちます。

## <a name="next"></a>次のステップ

[タイルとレポートの詳細][タイルとレポートの詳細]

[Web テスト][Web テスト]

[診断ログのキャプチャと検索][診断ログのキャプチャと検索]

[トラブルシューティング][トラブルシューティング]

## 詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][アプリケーションの正常性と利用状況の監視]
-   [ライブ Web サーバーの監視を今すぐ始める][エージェントをサーバーにインストール]
-   [メトリックの探索][タイルとレポートの詳細]
-   [Application Insights での診断ログの検索][診断ログのキャプチャと検索]
-   [Web アプリケーションの可用性と応答性の監視][Web テスト]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティング]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [プロジェクトの実行]: #run
  [監視データの表示]: #monitor
  [アプリケーションのデプロイ]: #deploy
  [次のステップ]: #next
  [エージェントをサーバーにインストール]: ../app-insights-monitor-performance-live-website-now/
  [Create an ASP.NET project]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Choose Add Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Right-click your project and open the Azure portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Click through to more data]: ./media/appinsights/appinsights-05-usageTiles.png
  [タイルとレポートの詳細]: ../app-insights-explore-metrics/
  [Web テスト]: ../app-insights-monitor-web-app-availability/
  [診断ログのキャプチャと検索]: ../app-insights-search-diagnostic-logs/
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Application Insights - 概要]: ../app-insights-get-started/
  [アプリケーションの正常性と利用状況の監視]: ../app-insights-monitor-application-health-usage/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
