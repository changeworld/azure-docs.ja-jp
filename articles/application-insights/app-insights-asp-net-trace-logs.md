<properties 
	pageTitle="Application Insights の .NET トレース ログを調べる" 
	description="Trace、NLog、または Log4Net で生成されたログを検索します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Application Insights の .NET トレース ログを調べる  

ASP.NET アプリケーションで NLog、log4Net、System.Diagnostics.Trace を診断追跡に使用している場合、ログを [Visual Studio Application Insights][start] に送信し、そこで調査したり、検索したりできます。ログはアプリケーションから送信される他の利用統計情報と結合されます。それにより、互いのユーザー要求にサービスを提供することに関連付けられているトレースを特定し、それらを他のイベントや例外レポートに相互に関連付けることができます。

> [AZURE.NOTE]ログ キャプチャ モジュールは必要ですか。 ログ キャプチャ モジュールは、サード パーティ製のロガーの場合は便利なアダプターですが、NLog、log4Net、または System.Diagnostics.Trace をまだ使用していない場合は、[Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) を直接呼び出すことを検討してください。

[プロジェクトに Application Insights をまだ設定していない場合][start]、今すぐ設定します。プロジェクトには、ファイル `ApplicationInsights.config` と、NuGet パッケージ `Microsoft.ApplicationInsights.Web` が必要です。


##  ログ記録フレームワークにアダプターをインストールする

ログ記録フレームワーク (log4Net、NLog、System.Diagnostics.Trace) を使用している場合、それらのログを他の利用統計情報と共に Application Insights に送信するアダプターをインストールできます。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。 
2. ソリューション エクスプローラーでプロジェクトを右クリックし、[**NuGet パッケージの管理**] を選択します。
3. Search for "Application Insights"

    ![適切なパッケージのプレリリース バージョンを入手する](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. 次のいずれかの適切なパッケージを選択します。
  + Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace コールをキャプチャするため)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet パッケージは、必要なアセンブリをインストールし、web.config または app.config も変更します。

#### 診断ログの呼び出しを挿入する

System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net、または NLog を使用する場合

    logger.Warn("Slow response - database01");


## トレース API を直接利用する

Application Insights トレース API を直接呼び出すことができます。ログ記録のアダプターはこの API を使用します。

次に例を示します。

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。たとえば、その中に POST データをエンコードできます。


## ログを調査する

[the Application Insights ポータル][portal]のアプリの [概要] ブレードで、[[検索][diagnostic]] を選択します。

![Application Insights で、[検索] を選択する](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![診断検索](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

たとえば、次の操作ができます。

* ログ トレースまたは特定のプロパティを持つ項目をフィルター処理する
* 特定の項目の詳細に調べる
* 同じユーザー要求に関連する (つまり、OperationId が同じ) 他の利用統計情報を探す 
* このページの構成をお気に入りとして保存する

> [AZURE.NOTE]**サンプリング。** アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。[サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## 次のステップ

[ASP.NET の例外とエラーを診断する][exceptions]

[診断検索の詳細][diagnostic]。



## トラブルシューティング

### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました

Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、[**Application Insights の更新**] を選択します。Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。これで問題は修正されます。

### 診断検索にトレースが表示されますが、他のイベントがありません

すべてのイベントと要求がパイプラインを通過するまで時間がかかることがあります。

### <a name="limits"></a>保持されるデータの量はどのくらいですか

各アプリケーションで、1 秒あたり 500 イベントまでです。イベントは 7 日間保持されます。

### 予期されるログ エントリの一部が表示されません

アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。[サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## <a name="add"></a>次のステップ

* [可用性と応答性のテストを設定する][availability]
* [トラブルシューティング][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1203_2015-->