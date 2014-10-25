<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

<!-- Required end of Sprint 69 - AUX48 -->

# ライブ Web サーバーの監視を今すぐ始める

*Application Insights はプレビュー段階です。*

Web アプリケーションの動作がおかしくなっていませんか。Web アプリケーションを再構築したり再デプロイしたりすることなく、例外やパフォーマンスの問題をすぐに分析できます。Application Insights エージェントをサーバーにインストールすると、パフォーマンスのボトルネックを見つけたり、任意の例外のスタック トレースを取得したりできます。

#### この方法で Application Insights を設定するのはどのような場合が適していますか?

このアプローチは、主に、ライブ IIS Web サイトのパフォーマンスの問題を迅速に診断するのに適しています。

エージェントをサーバーにインストールするだけで、Application Insights にパフォーマンス データが表示されます。

-   この方法は、IIS サーバー上でホストされる ASP.NET アプリケーションに適用できます。

-   データを表示するには、[Microsoft Azure アカウント][Microsoft Azure アカウント]が必要です。

-   Web アプリケーションが実行されているサーバーに対する管理アクセス権が必要です。

-   Web アプリケーションのコードは*不要*です。アプリケーションの再構築や再デプロイも必要ありません。

-   この方法では、Web アプリケーションがそのままインストルメント化されます。トレースまたはログ コードを挿入したりはしません (ただし、後で必要に応じてそうすることもできます)。

ログまたは診断トレースを挿入する場合は、ここから続けずに、[Application Insights をプロジェクトに追加し][既存の]、再デプロイしてください。

## Web サーバーへの Application Insights エージェントのインストール

1.  Web サーバーで、管理者の資格情報を使用してログインします。

2.  バージョン 5.0 以降の [Web プラットフォーム インストーラー][Web プラットフォーム インストーラー]があることを確認します。
3.  Web プラットフォーム インストーラーを使用して、Application Insights エージェントをインストールします。

    ![][]

4.  インストール ウィザードで、Microsoft Azure にサインインします。

    ![][1]

5.  監視するインストール済みの Web アプリケーションまたは Web サイトを選択し、Application Insights ポータルで結果を表示するときに使用する名前を構成します。最後に、[追加] ボタンをクリックします。

    ![][2]

    通常は、新しいリソースを作成する方法を選んでください。

    既存のリソースを使用するケースとしては、たとえば、サイト用の [Web テスト][Web テスト]を既に設定している場合があります。

6.  監視対象の Web サイトに ApplicationInsights.config が挿入されます。

    ![][3]

    これ以外にも、web.config にいくつかの変更が加えられます。

### 後で (再) 構成する

ウィザードを完了した後、いつでも必要に応じてエージェントを再構成できます。この方法は、エージェントをインストールした際の初期設定に問題があった場合にも使用できます。

![Click the Application Insights icon on the task bar][Click the Application Insights icon on the task bar]

## データの表示

Azure のアカウントを開いた後、Application Insights を参照して、作成したリソースを開きます。

![][4]

[アプリケーションの健全性] にデータが表示されます。

![][5]

#### データが表示されない場合

-   サイトを使用してデータを生成します。
-   データが到着するまでしばらく待ちます。
-   サーバーのファイアウォールでポート 443 を介した dc.services.visualstudio.com への発信トラフィックが許可されていることを確認します。

## <a name="next"></a>次のステップ

[データの表示][データの表示]

[診断ログの検索][診断ログの検索]

[Web テスト][Web テスト]

[利用状況の監視の設定][利用状況の監視の設定]

[トラブルシューティング][トラブルシューティング]

## 詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][利用状況の監視の設定]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サーバーの監視を今すぐ始める]
-   [メトリックの探索][データの表示]
-   [Application Insights での診断ログの検索][診断ログの検索]
-   [Web アプリケーションの可用性と応答性の監視][Web テスト]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティング]

<!--Link references-->

  [Microsoft Azure アカウント]: http://azure.com
  [Web プラットフォーム インストーラー]: http://www.microsoft.com/web/downloads/platform.aspx
  []: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [Web テスト]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Click the Application Insights icon on the task bar]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [データの表示]: ../app-insights-explore-metrics/
  [診断ログの検索]: ../app-insights-search-diagnostic-logs/
  [利用状況の監視の設定]: ../app-insights-monitor-application-health-usage/
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Application Insights - 概要]: ../app-insights-get-started/
  [ライブ Web サーバーの監視を今すぐ始める]: ../app-insights-monitor-performance-live-website-now/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
