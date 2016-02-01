<properties 
	pageTitle="Application Insights での依存関係に関する問題の診断" 
	description="依存関係によって発生したエラーとパフォーマンスの低下の検索" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="awills"/>
 
# Application Insights での依存関係に関する問題の診断


依存関係は、アプリによって呼び出される外部コンポーネントです。一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。Visual Studio Application Insights では、アプリケーションが依存関係を待機する期間や、依存関係の呼び出しが失敗する頻度を簡単に確認できます。

## 使用できる場所

すぐに使用できる依存関係の監視は現在、次のものに対して使用できます。

* IIS サーバーまたは Azure で実行されている ASP.NET Web アプリおよびサービス
* [Java Web アプリ](app-insights-java-agent.md)

デバイス アプリなどのそれ以外の種類では、[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) を使用して独自のモニターを作成できます。

すぐに使用できる依存関係モニターは現在、次の種類の依存関係の呼び出しを報告します。

* ASP.NET
 * SQL データベース
 * HTTP ベースのバインドを使用する ASP.NET Web および WCF サービス
 * ローカルまたはリモートの HTTP 呼び出し
 * Azure DocumentDb、テーブル、Blob Storage、およびキュー
* Java
 * MySQL、SQL Server、PostgreSQL、SQLite などの [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) ドライバーを使用したデータベースの呼び出し
* Web ページ
 * AJAX 呼び出し

この場合も、他の依存関係を監視するための独自の SDK 呼び出しを作成できます。

## 依存関係の監視をセットアップするには

ホスト サーバー用に適切なエージェントをインストールします。

プラットフォーム | インストール
---|---
IIS サーバー | [Status Monitor](app-insights-monitor-performance-live-website-now.md)
Azure Web アプリ | [Application Insights Extension](../azure-portal/insights-perf-analytics.md)
Java Web サーバー | [Java Web アプリ](app-insights-java-agent.md)

IIS サーバー用の Status Monitor では、Application Insights SDK を使用してソース プロジェクトをリビルドする必要はありません。

## <a name="diagnosis"></a>依存関係のパフォーマンスの問題の診断

サーバーでの要求のパフォーマンスを評価するには、次のようにします。

![Application Insights 内のアプリケーションの [概要] ページで、[パフォーマンス] タイルをクリックします](./media/app-insights-dependencies/01-performance.png)

下方向へスクロールして、要求のグリッドを確認します。

![平均およびカウントを持つ要求の一覧](./media/app-insights-dependencies/02-reqs.png)

上部にあるものは、非常に長い時間がかかります。どこに時間がかかるか見つけられるか見てみましょう。

個々の要求イベントを表示するには、その行をクリックします。


![要求回数の一覧](./media/app-insights-dependencies/03-instances.png)

実行時間の長いインスタンスがあればそれをクリックし、さらに検査します。

> [AZURE.NOTE]少し下にスクロールして、インスタンスを選択します。パイプラインでの待機時間は、上部のインスタンスのデータが不完全であることを示す場合があります。

この要求に関連したリモート依存関係呼び出しまで下にスクロールします。

![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-dependencies/04-dependencies.png)

この要求に使われた時間のほとんどが、ローカル サービスへの呼び出しに費やされたように見えます。

さらに情報を得るには、その行をクリックします。


![そのリモートの依存関係をクリックし、問題の原因を特定します](./media/app-insights-dependencies/05-detail.png)

詳細には、問題の診断に必要な情報が含まれています。



## エラー

失敗した要求がある場合は、グラフをクリックします。

![失敗した要求のグラフをクリックします。](./media/app-insights-dependencies/06-fail.png)

要求の種類と要求のインスタンスをクリックし、リモートの依存関係への失敗した呼び出しを見つけます。


![要求の種類をクリックし、インスタンスをクリックして同じインスタンスの異なるビューを取得し、それをクリックして例外の詳細を取得します。](./media/app-insights-dependencies/07-faildetail.png)


## カスタム依存関係の追跡

標準の依存関係追跡モジュールは、外部の依存関係 (データベース、REST API など) を自動的に検出しますが、同じように扱える追加のコンポーネントが必要になる可能性もあります。

標準のモジュールで使用するのと同じ [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) を使用して、依存関係情報を送信するコードを記述できます。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency` を使用して送信します。

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

標準の依存関係追跡モジュールを無効にするには、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) の DependencyTrackingTelemetryModule への参照を削除します。

<!--Link references-->

<!---HONumber=AcomDC_0121_2016-->