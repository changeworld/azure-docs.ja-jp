<properties
   pageTitle="Azure Cloud Services 向けの Application Insights"
   description="Application Insights で Web と worker ロールを効果的に監視する"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Azure Cloud Services 向けの Application Insights


*Application Insights はプレビュー段階です。*

[Microsoft Azure Cloud サービス アプリ](http://azure.microsoft.com/services/cloud-services/)の可用性、パフォーマンス、障害、使用状況を [Visual Studio Application Insights][start] で監視できます。アプリのパフォーマンスと効果に関するフィードバックが得られたら、各開発ライフサイクルにおける設計の方向性について、情報に基づいて選択できます。

![例](./media/app-insights-cloudservices/sample.png)

[Microsoft Azure](http://azure.com) のサブスクリプションが必要になります。Windows、XBox Live、またはその他の Microsoft クラウド サービスに与えられる Microsoft アカウントでサインインします。


#### Application Insights を使用してインストルメント化されたサンプル アプリケーション

Application Insights がクラウド サービスに追加され、2 つの worker ロールが Azure でホストされている[サンプル アプリケーション](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)をご覧ください。

以降では、同じようにして独自のクラウド サービス プロジェクトを調整する方法について説明します。

## 役割ごとに Application Insights リソースを作成する

Application Insights リソースでは、利用統計情報データが分析され、表示されます。

1.  [Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。アプリケーションの種類として ASP.NET アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-cloudservices/01-new.png)

2.  インストルメンテーション キーをコピーします。これはじきに SDK を構成するために必要になります。

    ![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-cloudservices/02-props.png)


通常、それぞれの Web と worker ロールからのデータに対して個別のリソースを作成するのが最良の方法になります。

代替として、すべてのロールからのデータを 1 つだけのリソースに送信できます。ただし、各ロールからの結果をフィルター処理またはグループ化できるように[既定のプロパティ][apidefaults]を設定します。

## <a name="sdk"></a>各プロジェクトに SDK をインストールする


1. Visual Studio で、クラウド アプリ プロジェクトの NuGet パッケージを編集します。

    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-cloudservices/03-nuget.png)


2. [Web 向けの Application Insights](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージを追加します。SDK のこのバージョンには、ロールの情報など、サーバー コンテキストを追加するモジュールが含まれています。worker ロールの場合、Windows Services の Application Insights を使用します。

    !["Application Insights" の検索](./media/app-insights-cloudservices/04-ai-nuget.png)


3. データを Application Insights リソースに送信するように SDK を構成します。

    インストるメンテーション キーを構成設定として `ServiceConfiguration.Cloud.cscfg` ファイル内に設定します ([サンプル コード](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg))。
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    適切なスタートアップ関数の中に、構成設定のインストるメンテーション キーを設定します。

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    構成設定の同じ名前 `APPINSIGHTS_INSTRUMENTATIONKEY` が Azure 診断レポートによって使用されることに注意してください。


    この手順をアプリケーションの各ロールで実行します。次の例を参照してください。
 
 * [Web ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [worker ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [Web ページ向け](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. ApplicationInsights.config ファイルが常に出力ディレクトリにコピーされるように設定します。 

    (.config ファイルの中に、インストルメンテーション キーの配置を求めるメッセージがあります。ただし、クラウド アプリケーションでは、それは .cscfg ファイルから設定することをお勧めします。これにより、ポータルでロールが正確に識別されます)。


#### アプリを実行して発行する

アプリを実行し、Azure にサインインします。作成した Application Insights リソースを開くと、個別のデータ ポイントが[検索](app-insights-diagnostic-search.md)に表示され、集計データが[メトリックス エクスプローラー](app-insights-metrics-explorer.md)に表示されます。

さらにテレメトリを追加し (後のセクションを参照)、アプリを発行して、ライブの診断と使用状況のフィードバックを取得します。


#### データが表示されない場合

* [[検索]][diagnostic] タイルを開き、個々のイベントを表示します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照します。



## テレメトリの追加

以下のセクションでは、アプリケーションの異なる部分から他のテレメトリを取得する方法を示します。


## ワーカー ロールからの要求を追跡する

Web ロールでは、HTTP 要求に関するデータが要求モジュールによって自動的に収集されます。既定の収集動作をオーバーライドする方法の例については、[サンプルの MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) をご覧ください。

HTTP 要求の場合と同じ方法で追跡することで、worker ロールの呼び出しのパフォーマンスを記録できます。Application Insights では、「要求」型の利用統計情報は、サーバー側で名前を付け、時間を指定し、個別に成功と失敗を判定できる作業単位を測定できます。SDK は HTTP 要求を自動的に記録します。一方で、独自のコードを挿入し、worker ロールへの要求を追跡できます。

要求をレポートするためにインストルメント化された 2 つのサンプル worker ロール [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) および [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB) をご覧ください。

## Azure 診断

[Azure 診断](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)のデータには、ロール管理イベント、パフォーマンス カウンター、およびアプリケーション ログが含まれます。これらのデータを Application Insights に送信し、他のテレメトリと共に表示して、問題の診断を容易にできます。

ロールが予期せずに失敗した場合、または起動に失敗した場合は、Azure 診断が特に役立ちます。

1. ロール (プロジェクトではない!) を右クリックしてプロパティを開き、**[診断を有効にする]**、**[診断を Application Insights に送信する]** を選択します。

    !["Application Insights" の検索](./media/app-insights-cloudservices/21-wad.png)

    **または、アプリが既に発行されて動いている場合は**、サーバー エクスプローラーまたは Cloud エクスプローラーを開き、アプリを右クリックして同じオプションを選択します。

3.  他のテレメトリと同じ Application Insights リソースを選択します。

    必要な場合は、異なるサービス構成 (クラウド、ローカル) で異なるリソースを設定し、開発データとライブ データを分離しておくことができます。

3. 必要に応じて、Application Insights に転送する[一部の Azure 診断を除外](app-insights-azure-diagnostics.md)します。既定ではすべてです。

### Azure 診断イベントを表示する

診断データは次の場所で見つかります。

* パフォーマンス カウンターは、カスタム メトリックとして表示されます。 
* Windows イベント ログは、トレースとカスタム イベントとして表示されます。
* アプリケーション ログ、ETW ログ、診断インフラストラクチャ ログは、トレースとして表示されます。

パフォーマンス カウンターおよびイベント数を表示するには、[メトリックス エクスプローラー](app-insights-metrics-explorer.md)を開き、新しいグラフを追加します。


![](./media/app-insights-cloudservices/23-wad.png)

[検索](app-insights-diagnostic-search.md)を使用して、Azure 診断によって送信されるさまざまなトレース ログを検索します。たとえば、ロールでハンドルされない例外が発生して、ロールがクラッシュし、リサイクルされた場合、Windows イベント ログのアプリケーション チャネルにその情報が表示されます。検索機能を使用して、Windows イベント ログのエラーを確認し、例外のスタック トレース全体を取得することで、問題の根本原因を発見できます。


![](./media/app-insights-cloudservices/25-wad.png)

## アプリの診断

Azure 診断には、アプリが System.Diagnostics.Trace を使用して生成するログ エントリが自動的に含まれます。

ただし、Log4N または NLog フレームワークを既に使用している場合は、[これらのログ トレースをキャプチャする][netlogs]こともできます。

アプリケーションのパフォーマンスと使用状況の詳細を把握するには、クライアントとサーバーのどちらか一方または両方で[カスタム イベントとメトリックを追跡][api]します。

## 依存関係

Application Insights SDK では、REST API や SQL サーバーなどの外部依存関係に対してアプリが実行した呼び出しをレポートできます。このレポートを使用して、特定の依存関係が応答速度の低下や障害の原因となっているかどうかを確認できます。

依存関係を追跡するには、[Application Insights エージェント](app-insights-monitor-performance-live-website-now.md) ("Status Monitor" とも呼ばれます) を使用して、Web/worker ロールを設定する必要があります。

Web/worker ロールで Application Insights エージェントを使用するには:

* Web/worker ロール プロジェクトに [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) フォルダーを追加し、その中に 2 つのファイルを追加します。常に出力ディレクトリにコピーされるように、ビルド プロパティを設定してください。これらのファイルによってエージェントがインストールされます。
* [こちら](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18)に示すように、CSDEF ファイルに開始タスクを追加します。
* 注: [こちら](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44)に示すように、*worker ロール*には 3 つの環境変数が必要です。これは web ロールでは必要ありません。

Application Insights ポータルに表示される内容の例を次に示します。

* 要求と依存関係が自動的に関連付けられる多様な診断:

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Web ロールのパフォーマンスと依存関係情報:

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* worker ロールの要求と依存関係の情報が表示されたスクリーンショット:

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

## 例外

各種 Web アプリケーションから未処理の例外を収集する方法の詳細については、[Application Insights での例外の監視](app-insights-asp-net-exceptions.md)に関するページをご覧ください。

サンプルの web ロールには MVC5 コントローラーおよび Web API 2 コントローラーが含まれています。この 2 つからの未処理の例外は、以下を使用してキャプチャされます。

* MVC5 コントローラー向けの [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) セットアップ ([こちらを参照](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12))
* Web API 2 コントローラー向けの [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) セットアップ ([こちらを参照](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25))

woker ロールの場合、例外を追跡する方法は 2 つあります。

* TrackException(ex)
* Application Insights トレース リスナー NuGet パッケージを追加した場合は、System.Diagnostics.Trace を使用して例外を記録できます。[コード例はこちらです。](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## パフォーマンス カウンター

既定では、次のカウンターが収集されます。

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

また、web ロールの場合は次も収集されます。

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

[こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)に示すように、カスタム カウンターや他の Windows パフォーマンス カウンターを追加で指定できます。

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## worker ロールのテレメトリの関連付け

豊富な診断のエクスペリエンスでは、要求が失敗した場合や待機時間が長い場合の原因を確認できます。Web ロールを使用すると、SDK によって関係するテレメトリの間に関連付けが自動的に設定されます。worker ロールの場合は、カスタムのテレメトリ初期化子を使用して、すべてのテレメトリに共通の Operation.Id context 属性を設定することで、これを実現できます。これにより、待機時間/失敗の問題の原因が依存関係とコードのどちらにあるかを一目で把握することができます。

その方法は次のとおりです。

* [こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)に示すように、CallContext に関連付け ID を設定します。このケースでは、要求 ID を関連付け ID として使用しています。
* カスタムの TelemetryInitializer 実装を追加します。これにより、上で設定した correlationId に Operation.Id が設定されます。こちらをご覧ください ([ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13))。
* カスタムのテレメトリ初期化子を追加します。これは、ApplicationInsights.config ファイル内か、[こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)に示すようにコード内で実行できます。

これで完了です。 ポータル エクスペリエンスは既に、関連付けられたすべてのテレメトリを一目で確認できるように設定されています。

![](./media/app-insights-cloudservices/bHxuUhd.png)



## クライアント テレメトリ

[Web ページに JavaScript SDK を追加][client]して、ページ ビュー数、ページの読み込み時間、スクリプトの例外などのブラウザー ベースのテレメトリを取得し、ページ スクリプトにカスタムのテレメトリを記述することができます。

## 可用性テスト

[Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。



## 例

[この例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)では、1 つの Web ロールと 2 つの worker ロールが含まれたサービスを監視します。

## 関連トピック

* [Application Insights に Azure 診断を送信するための構成](app-insights-azure-diagnostics.md)
* [PowerShell を使用した Application Insights への Azure 診断の送信])(app-insights-powershell-azure-diagnostics.md)



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_1125_2015-->