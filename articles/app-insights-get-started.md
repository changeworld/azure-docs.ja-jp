<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights - 概要

*Application Insights はプレビュー段階です。*

Application Insights を使うと、ライブ アプリケーションの可用性、パフォーマンス、利用状況を監視できます (アプリケーションは Microsoft Azure アプリケーションである必要はありません)。構成は非常に簡単です。そして、結果をすぐに確認できます。

-   **可用性** - Web アプリケーションが利用可能であり応答性を維持していることを確認します。地球上のいろいろな場所から数分おきに URL がテストされ、問題がある場合は通知されます。
-   **パフォーマンス** - Web サービスのあらゆるパフォーマンスの問題または例外が診断されます。応答時間が要求数と共にどのように変化するか、CPU またはその他のリソースに過剰な負荷がかかっていないかどうかを確認できます。また、例外からスタック トレースを取得したり、ログ トレースを容易に検索したりできます。
-   **利用状況** - お客様のアプリケーションを使用しているユーザーを把握することで、開発作業を最も有用な部分にフォーカスできます。現在、Web アプリケーション、Windows ストア アプリケーション、Windows Phone アプリケーションを監視できます。

## はじめに

利用を開始するには、次の 2 つの方法があります。

-   [Application Insights を Visual Studio のプロジェクトに追加する][Application Insights を Visual Studio のプロジェクトに追加する]

    Application Insights をプロジェクトに追加すると、利用状況、パフォーマンス、可用性を追跡し、診断ログを分析できます。データはデバッグ モードですぐに表示されます。プロジェクトをデプロイすることで、ライブ データを取得できます。

    プロジェクトを更新または作成している場合は、このオプションを使用してください。

    [Application Insights をプロジェクトに追加して作業を開始する][Application Insights を Visual Studio のプロジェクトに追加する]

-   [ライブ Web サービスの問題を今すぐ診断する][ライブ Web サービスの問題を今すぐ診断する]

    Insights エージェントを IIS サーバーにインストールすると、すぐにパフォーマンス データを確認できます。要求数、応答時間、リソースの負荷を監視し、例外トレースを取得できます。

    このオプションは、Web サーバーの状態を今すぐ確認する必要がある場合に使用します。この方法では、コードの再デプロイは行われません。ただし、サーバーおよび Microsoft Azure アカウントへの管理アクセス権が必要になります。

    可用性の監視はいつでも追加できます。

    後で他のオプションを使って Application Insights をプロジェクトに追加して、診断ログを分析し、利用状況を追跡できます。

    [Application Insights を Web サーバーにインストールして作業を開始する][ライブ Web サービスの問題を今すぐ診断する]

> [WACOM.NOTE] Visual Studio Online には [Application Insights の以前のバージョン][Application Insights の以前のバージョン]が登録されています。Application Insights は、Microsoft Azure の一部として新しく作り直されています。ここで扱っているのは、新しいバージョンです。

![Example application monitor in Application Insights][Example application monitor in Application Insights]

## 詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][Application Insights を Visual Studio のプロジェクトに追加する]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サービスの問題を今すぐ診断する]
-   [メトリックの探索][メトリックの探索]
-   [Application Insights での診断ログの検索][Application Insights での診断ログの検索]
-   [Web アプリケーションの可用性と応答性の監視][Web アプリケーションの可用性と応答性の監視]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights]

<!--Link references-->

  [Application Insights を Visual Studio のプロジェクトに追加する]: ../app-insights-monitor-application-health-usage/
  [ライブ Web サービスの問題を今すぐ診断する]: ../app-insights-monitor-performance-live-website-now/
  [Application Insights の以前のバージョン]: http://msdn.microsoft.com/en-us/library/dn481095.aspx
  [Example application monitor in Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights - 概要]: ../app-insights-get-started/
  [メトリックの探索]: ../app-insights-explore-metrics/
  [Application Insights での診断ログの検索]: ../app-insights-search-diagnostic-logs/
  [Web アプリケーションの可用性と応答性の監視]: ../app-insights-monitor-web-app-availability/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-web-track-usage-custom-events-metrics/
  [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights]: ../app-insights-troubleshoot-faq/
