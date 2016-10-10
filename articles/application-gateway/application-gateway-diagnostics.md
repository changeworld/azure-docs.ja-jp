<properties 
   pageTitle="Application Gateway のアクセス ログおよびパフォーマンス ログとメトリックの監視 | Microsoft Azure"
   description="Application Gateway のアクセス ログとパフォーマンス ログを有効にし、管理する方法について説明します。"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# Application Gateway の診断ログとメトリック

Azure は、ログとメトリックを使用してリソースを監視する機能を提供しています。

[**ログ**](#enable-logging-with-powershell) - リソースのパフォーマンス、アクセス、その他を記録したログは、監視のために保存し使用することができます。

[**メトリック**](#metrics) - アプリケーション ゲートウェイが持つメトリックは現在 1 つです。このメトリックは、アプリケーション ゲートウェイの 1 秒あたりのスループットをバイト単位で測定したものです。

Azure の各種ログを使用して、アプリケーション ゲートウェイの管理とトラブルシューティングを行うことができます。これらのログの一部はポータルからアクセスできます。また、すべてのログは、Azure BLOB ストレージから抽出して、[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Excel、PowerBI などのさまざまなツールで表示することができます。各種ログの詳細については、以下の一覧を参照してください。

- **監査ログ:** [Azure 監査ログ](../azure-portal/insights-debugging-with-events.md) (以前の "操作ログ") を使用すると、Azure サブスクリプションに送信されているすべての操作とその操作の状態を表示できます。監査ログは既定で有効になっており、Azure プレビュー ポータルで表示できます。
- **アクセス ログ:** このログを使用すると、アプリケーション ゲートウェイのアクセス パターンを確認し、重要な情報 (呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイト数など) を分析できます。アクセス ログは 300 秒ごとに収集されます。このログには、アプリケーション ゲートウェイのインスタンスごとに 1 つのレコードが含まれます。アプリケーション ゲートウェイのインスタンスは、"instanceId" プロパティで識別できます。
- **パフォーマンス ログ:** このログを使用すると、アプリケーション ゲートウェイのインスタンスの実行状況を確認できます。このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。パフォーマンス ログは 60 秒ごとに収集されます。
- **ファイアウォール ログ:** このログを使用すると、Web アプリケーション ファイアウォールが構成されたアプリケーション ゲートウェイの、検出モードまたは防止モードでログに記録された要求を表示することができます。

>[AZURE.WARNING] ログは、リソース マネージャーのデプロイ モデルでデプロイされたリソースについてのみ使用できます。クラシック デプロイメント モデルのリソースには使用できません。2 つのモデルについて理解を深めるには、「[リソース マネージャー デプロイと従来のデプロイを理解する](../resource-manager-deployment-model.md)」を参照してください。

## PowerShell を使用したログの有効化

監査ログは、リソース マネージャーのすべてのリソースで自動的に有効になります。アクセス ログとパフォーマンス ログで使用可能なデータの収集を開始するには、これらのログを有効にする必要があります。ログ記録を有効にするには、次の手順に従って操作します。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。これは、/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> のような形式です。サブスクリプション内の任意のストレージ アカウントを使用できます。この情報は、プレビュー ポータルで確認できます。

	![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics1.png)

2. ログを有効にするアプリケーション ゲートウェイのリソース ID をメモしておきます。これは、/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> のような形式です。この情報は、プレビュー ポータルで確認できます。

	![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics2.png)

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] 監査ログでは別のストレージ アカウントは必要ありません。アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

## Azure ポータルを使用したログの有効化

### 手順 1

Azure ポータルで、リソースに移動します。**[診断ログ]** をクリックします。初めて診断を構成する場合、ブレードは次の図のようになります。

アプリケーション ゲートウェイでは、次の 3 つのログを利用できます。

- アクセス ログ
- パフォーマンス ログ
- ファイアウォール ログ

**[診断を有効にする]** をクリックして、データの収集を開始します。

![[診断設定] ブレード][1]

### 手順 2.

**[診断設定]** ブレードで、診断ログの収集方法を設定します。この例では、Log Analytics を使用してログを保存します。**[Log Analytics]** の下にある **[構成]** をクリックして、ワークスペースを構成します。Event Hubs とストレージ アカウントを使用して診断ログを保存することもできます。

![[診断] ブレード][2]

### 手順 3.

既存の OMS ワークスペースを選択するか、新しい OMS ワークスペースを作成します。この例では、既存のものを使用します。

![OMS ワークスペース][3]

### 手順 4.

完了したら、設定を確認し、**[保存]** をクリックして設定を保存します。

![選択内容の確認][4]

## 監査ログ

監査ログ (以前の "操作ログ") は、既定では Azure によって生成されます。ログは、Azure のイベント ログ ストアに 90 日間保存されます。これらのログの詳細については、「[イベント ログと監査ログの表示](../azure-portal/insights-debugging-with-events.md)」を参照してください。

## アクセス ログ

このログは、前の手順で示したように、Application Gateway ごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次の例に示すように、Application Gateway の各アクセスは JSON 形式でログに記録されます。

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}

## パフォーマンス ログ

このログは、前の手順で示したように、Application Gateway ごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次のデータがログに記録されます。

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}


## ファイアウォール ログ

このログは、前の手順で示したように、アプリケーション ゲートウェイごとにログを有効にした場合にのみ生成されます。このログを使用するには、アプリケーション ゲートウェイで Web アプリケーション ファイアウォールを構成する必要もあります。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次のデータがログに記録されます。

	{
		"resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
		"operationName": "ApplicationGatewayFirewall",
		"time": "2016-09-20T00:40:04.9138513Z",
		"category": "ApplicationGatewayFirewallLog",
		"properties":     {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIp":"108.41.16.164",
			"clientPort":1815,
			"requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
			"ruleId":"OWASP_973336",
			"message":"XSS Filter - Category 1: Script Tag Vector",
			"action":"Logged",
			"site":"Global",
			"message":"XSS Filter - Category 1: Script Tag Vector",
			"details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
	}

## 監査ログの表示と分析

次のいずれかの方法を使用して、監査ログのデータを表示および分析できます。

- **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure プレビュー ポータルを使用して、監査ログから情報を取得します。それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../resource-group-audit.md)」を参照してください。
- **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。[Power BI 用 Azure 監査ログ コンテンツ パック](https://powerbi.microsoft.com/ja-JP/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## アクセス ログ、パフォーマンス ログ、ファイアウォール ログの表示と分析

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) は、BLOB ストレージ アカウントからカウンターとイベントのログ ファイルを収集でき、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

>[AZURE.TIP] Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、Github から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## メトリック

メトリックは特定の Azure リソース用の機能で、ポータルでパフォーマンス カウンターを表示できます。Application Gateway では、この記事の執筆時に使用できるメトリックは 1 つです。そのメトリックとはスループットのことで、ポータルで確認することができます。アプリケーション ゲートウェイに移動して **[メトリック]** をクリックします。**[Available metrics (使用可能なメトリック)]** セクションでスループットを選択すると、値が表示されます。次の図は、さまざまな時間範囲のデータの表示に使用できるフィルターの例を示しています。

現在サポートされているメトリックの一覧を表示するには、「[Supported metrics with Azure Monitor (Azure Monitor でサポートされているメトリック)](../azure-portal/monitoring-supported-metrics.md)」を参照してください。

![メトリック ビュー][5]

## アラート ルール

アラート ルールは、リソースのメトリックに基づいて開始することができます。つまり、アプリケーション ゲートウェイのスループットが指定した期間にしきい値を上回るか下回る場合、またはしきい値に等しい場合、アラートで webhook を呼び出したり、管理者に電子メールを送信したりできます。

次の例では、スループットのしきい値を超えた後に管理者に電子メールを送信するアラート ルールの作成手順を説明します。

### 手順 1

**[Add metric alert (メトリック アラートの追加)]** をクリックして開始します。このブレードには、メトリック ブレードからもアクセスできます。

![[アラート ルール] ブレード][6]

### 手順 2.

**[ルールの追加]** ブレードで、名前、条件、通知セクションを入力し、完了したら **[OK]** をクリックします。

**[条件]** セレクターに入力できるのは、**[より大きい]**、**[以上]**、**[未満]**、または **[以下]** の 4 つの値です。

**[期間]** セレクターでは、5 分から 6 時間までの期間を選択できます。

**[メールの所有者、投稿者、閲覧者]** を選択すると、そのリソースのアクセス権を持つユーザーに基づいて電子メールを動的に送信できます。それ以外の場合は、ユーザーのコンマ区切りの一覧を **[追加の管理者メール]** テキストボックスに指定できます。

![[ルールの追加] ブレード][7]

しきい値を超えた場合、次の図のような電子メールが送信されます。

![しきい値違反の電子メール][8]

メトリック アラートが作成されると、アラートの一覧が表示されるため、すべてのアラート ルールの概要を確認できます。

![アラート ルールの表示][9]

アラート通知の詳細については、[アラート通知の受信](../azure-portal/insights-receive-alert-notifications.md)に関するページを参照してください。

webhook と、webhook とアラートを使用する方法の詳細については、「[Azure メトリック アラートでの webhook の構成](../azure-portal/insights-webhooks-alerts.md)」を参照してください。

## 次のステップ

- [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) で、カウンター ログとイベント ログを視覚化します。
- [Power BI を使用した Azure 監査ログの視覚化](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログ記事
- [Power BI などにおける Azure 監査ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ記事

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png

<!---HONumber=AcomDC_0928_2016-->