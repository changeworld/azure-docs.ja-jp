<properties 
	pageTitle="Application Insights のアラートの設定" 
	description="クラッシュ、例外、メトリックの変化に関して電子メールを受け取ります。" 
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
	ms.date="09/14/2015" 
	ms.author="awills"/>
 
# Application Insights のアラートの設定

[Visual Studio Application Insights][start] は、アプリケーションのパフォーマンスまたは使用状況のメトリックの変化についてアラートを発行できます。

Application Insights は、[さまざまなプラットフォーム][platforms]で実行中のアプリケーションを監視して、パフォーマンスの問題の診断と使用パターンの把握を支援します。

次の 2 つの種類のアラートがあります。
 
* **Web テスト** は、サイトがインターネットで使用できなくなったとき、または応答速度が低下したときに通知します。[詳細情報][availability]
* **メトリック アラート**は、メトリックが、一定の期間に、エラー数、メモリ、ページ ビューなどのしきい値を超えた場合に通知します。 

[Web テストに関する別のページ][availability]があるため、ここではメトリック アラートのみが表示されます。

## メトリック アラート

アプリケーションに対して Application Insights を設定していない場合は、[最初に設定を行います][start]。

メトリックがしきい値を超えたときに電子メールを受け取るには、メトリックス エクスプローラーまたは [概要] ブレードのアラート ルール タイルのいずれかから設定を始めます。

![[アラート ルール] ブレードで、[アラートの追加] をクリックします。測定するリソースとしてアプリケーションを設定し、アラートの名前を指定し、メトリックを選択します。](./media/app-insights-alerts/01-set-metric.png)

その他のプロパティの前に、リソースを設定します。パフォーマンスまたは使用状況のメトリックにアラートを設定する場合は、**「(コンポーネント)」のリソースを選択**します。

しきい値を入力するように求められたら、単位に注意してください。

アラートに付ける名前は、リソース グループ内で (アプリケーション内だけでなく) 一意である必要があります。

*[アラートの追加] ボタンが表示されない場合は、*組織アカウントを使用していることを確認してください。 所有者または投稿者が、アプリケーションのリソースにアクセスする場合に、アラートを設定できます。[設定] -> [ユーザー] を表示します。[アクセス制御の詳細情報][roles]。

## アラートの表示

アラートの状態が非アクティブとアクティブとの間で変化すると、メールが送信されます。

[アラート ルール] ブレードに、各アラートの現在の状態が表示されます。

状態の変更履歴は、操作イベント ログにも表示されます。

![下部付近の [概要] ブレードで、[過去 1 週間のイベント] をクリックします。](./media/app-insights-alerts/09-alerts.png)

*これらの「イベント」は、利用統計情報イベントまたはカスタム イベントに関係しますか。*

* いいえ。これらの操作イベントは、このアプリケーション リソースに対する操作のみの記録です。 

## 可用性のアラート

世界中の場所から任意の Web サイトをテストする Web テストを設定できます。[詳細情報][availability]。

## どのようなアラートを設定するのが適切でしょうか。

これは、アプリケーションごとに異なります。第一に、多数のメトリックを設定しないことをお勧めします。通常の動作を把握するために、アプリケーションの実行中に、しばらくの間、メトリックのグラフを観察してください。これは、パフォーマンスを向上させる方法を見つけるために役立ちます。次に、メトリックが標準ゾーンを超えた場合に通知するアラートを設定します。

次のような一般的なアラートがあります。

* [Web テスト][availability]は、アプリケーションが公共のインターネットに表示される Web サイトまたは Web サービスの場合に重要です。このテストは、アプリケーションの問題ではなく、通信事業者の問題である場合でも、サイトがダウンするか、応答速度が低下した場合に通知します。ただし、これは代理テストであるため、ユーザーの実際のエクスペリエンスは測定されません。
* [ブラウザー メトリック][client]、特にブラウザー ページの読み込み時間は、Web アプリケーションに適しています。ページに多数のスクリプトがある場合は、ブラウザーの例外に注意する必要が生じます。これらのメトリックとアラートを取得するには、[Web ページの監視][client]を設定する必要があります。
* Web アプリケーションのサーバー側のサーバー応答時間と失敗した要求。アラートの設定に加えて、これらのメトリックを監視して、高い要求レートでメトリックが過度に変動するかどうかを確認します。これは、アプリケーションでのリソースの不足を示している可能性があります。

## PowerShell を使用したアラートの設定

ほとんどの場合、手動によるアラート設定で十分に目的を果たせます。ただし、メトリックのアラートを自動的に作成する必要がある場合は、PowerShell を使用することができます。

#### 1 回限りのセットアップ

以前に Azure サブスクリプションで PowerShell を使用したことがない場合

1. スクリプトを実行するコンピューターに Azure PowerShell モジュールをインストールします。 
 * [Microsoft Web Platform Installer (v5 以上)](http://www.microsoft.com/web/downloads/platform.aspx) をインストールする。
 * このインストーラーを使用して Microsoft Azure PowerShell をインストールする。
2. Azure PowerShell を起動して、[サブスクリプションに接続](powershell-install-configure.md)します。

    ```
    Add-AzureAccount
    ```

#### アラートの取得

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### アラートの追加


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### 例 1

「HTTP 要求に対するサーバーの応答時間」の 5 分間の平均が 1 秒を超えた場合、電子メールで通知してください。私の Application Insights リソースは IceCreamWebApp と呼ばれており、リソース グループ Fabrikam 内にあります。私は Azure サブスクリプションの所有者です。

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### 例 2

私は、[TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) を使用して "salesPerHour" という名前のメトリックをレポートするアプリケーションを持っています。 24 時間にわたる "salesPerHour" の平均が 100 を下回る場合は、私の同僚に電子メールを送信してください。

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

別の追跡呼び出しの[測定パラメーター](app-insights-api-custom-events-metrics.md#properties) (TrackEvent や trackPageView など) を使用してレポートされるメトリックにも同じルールを使用することができます。

#### メトリックの名前

メトリックの名前 | 画面の名前 | 説明
---|---|---
`basicExceptionBrowser.count`|ブラウザーの例外|ブラウザーでスローされた、キャッチされない例外の数。
`basicExceptionServer.count`|サーバーの例外|アプリによってスローされた未処理の例外の数
`clientPerformance.clientProcess.value`|クライアントの処理時間|ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。非同期要求がまだ処理されている可能性があります。
`clientPerformance.networkConnection.value`|ページ読み込みのネットワーク接続時間| ブラウザーがネットワークに接続するために要する時間です。キャッシュされている場合は、0 にすることができます。
`clientPerformance.receiveRequest.value`|受信応答時間| ブラウザーが要求を送信してから応答を受信し始めるまでの時間。
`clientPerformance.sendRequest.value`|要求送信時間| ブラウザーが要求を送信するのに要する時間。
`clientPerformance.total.value`|ブラウザーのページ読み込み時間|ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。
`performanceCounter.available_bytes.value`|使用可能なメモリ|プロセスまたはシステムの用途で、すぐに利用できる物理メモリ。
`performanceCounter.io_data_bytes_per_sec.value`|IO 処理速度|ファイル、ネットワーク、およびデバイスに対する読み書きで 1 秒あたりに処理される合計バイト数。
`performanceCounter.number_of_exceps_thrown_per_sec`|例外レート|1 秒あたりにスローされる例外。
`performanceCounter.percentage_processor_time.value`|プロセス CPU|アプリケーション プロセスの実行命令に対してプロセッサが使用するすべてのプロセス スレッドの経過時間の割合。
`performanceCounter.percentage_processor_total.value`|プロセッサ時間|プロセッサが非アイドル スレッドで費やす時間の割合。
`performanceCounter.process_private_bytes.value`|プロセスのプライベート バイト|監視対象のアプリケーション プロセスに対して専用に割り当てられるメモリ。
`performanceCounter.request_execution_time.value`|ASP.NET 要求の実行時間|最新の要求の実行時間。
`performanceCounter.requests_in_application_queue.value`|実行キュー内の ASP.NET 要求|アプリケーション要求キューの長さ。
`performanceCounter.requests_per_sec`|ASP.NET 要求レート|ASP.NET からの 1 秒あたりのアプリケーションへのすべての要求のレート。
`remoteDependencyFailed.durationMetric.count`|依存関係の障害|サーバー アプリケーションによる外部リソースの呼び出しが失敗した回数。
`request.duration`|サーバーの応答時間|HTTP 要求を受信してから、応答の送信を終了するまでの時間。
`request.rate`|要求レート|1 秒あたりのアプリケーションに出されるすべての要求のレート。
`requestFailed.count`|失敗した要求|400 またはこれより大きな応答コードを生じさせた HTTP 要求の数 
`view.count`|ページ ビュー|Web ページに対するクライアント ユーザーの要求数。代理トラフィックはフィルターで除外されます。
{カスタム メトリック名}|{メトリック名}|[TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) または[追跡呼び出しの測定パラメーター](app-insights-api-custom-events-metrics.md#properties)でレポートされるメトリック値。

   


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO3-->