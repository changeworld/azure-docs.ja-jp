<properties 
   pageTitle="Application Gateway のアクセス ログとパフォーマンス ログの監視 | Microsoft Azure"
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
   ms.date="07/14/2016"
   ms.author="amitsriva" />

#Application Gateway の診断ログ

Azure の各種ログを使用して、Application Gateway の管理とトラブルシューティングを行うことができます。これらのログの一部にはポータルからアクセスできます。また、どのログも、Azure Blob Storage から抽出して、[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Excel、PowerBI などのさまざまなツールで表示することができます。各種ログの詳細については、以下の一覧を参照してください。

- **監査ログ:** [Azure 監査ログ](../azure-portal/insights-debugging-with-events.md) (以前の操作ログ) を使用すると、Azure サブスクリプションに送信されているすべての操作とその操作の状態を表示できます。監査ログは既定で有効になっており、Azure プレビュー ポータルで表示できます。
- **アクセス ログ:** このログを使用すると、Application Gateway のアクセス パターンを確認し、重要な情報 (呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイト数など) を分析できます。アクセス ログは 300 秒ごとに収集されます。このログには、Application Gateway のインスタンスごとに 1 つのレコードが含まれます。Application Gateway のインスタンスは、"instanceId" プロパティで識別できます。
- **パフォーマンス ログ:** このログを使用すると、Application Gateway のインスタンスの実行状況を確認できます。このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。パフォーマンス ログは 60 秒ごとに収集されます。

>[AZURE.WARNING] ログは、リソース マネージャーのデプロイ モデルでデプロイされたリソースについてのみ使用できます。クラシック デプロイメント モデルのリソースには使用できません。2 つのモデルについて理解を深めるには、「[リソース マネージャー デプロイと従来のデプロイを理解する](../resource-manager-deployment-model.md)」を参照してください。

##ログの有効化
監査ログは、リソース マネージャーのすべてのリソースで常に自動的に有効になります。アクセス ログとパフォーマンス ログで使用可能なデータの収集を開始するには、これらのログを有効にする必要があります。ログを有効にするには、次の手順に従います。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。これは、/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> のような形式です。サブスクリプション内の任意のストレージ アカウントを使用できます。この情報は、プレビュー ポータルで確認できます。![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics1.png)
 
2. ログを有効にする Application Gateway のリソース ID をメモしておきます。これは、/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> のような形式です。この情報は、プレビュー ポータルで確認できます。![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics2.png)

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] 監査ログでは別のストレージ アカウントは必要ありません。アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。


## 監査ログ
監査ログ (以前の "操作ログ") は、既定では Azure によって生成されます。ログは、Azure のイベント ログ ストアに 90 日間保存されます。これらのログの詳細については、「[イベント ログと監査ログの表示](../azure-portal/insights-debugging-with-events.md)」を参照してください。

## アクセス ログ
このログは、前述のように、Application Gateway ごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次に示すように、Application Gateway の各アクセスは JSON 形式でログに記録されます。

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resoource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
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
このログは、前述のように、Application Gateway ごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次のデータがログに記録されます。

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

## 監査ログの表示と分析
次のいずれかの方法を使用して、監査ログのデータを表示および分析できます。

- **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure プレビュー ポータルを使用して、監査ログから情報を取得します。それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../resource-group-audit.md)」を参照してください。
- **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。[Power BI 用 Azure 監査ログ コンテンツ パック](https://powerbi.microsoft.com/ja-JP/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## アクセスおよびパフォーマンス ログの表示と分析 
Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) は、Blob Storage アカウントからカウンターとイベントのログ ファイルを収集でき、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

>[AZURE.TIP] Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、Github から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## 次のステップ

- [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) で、カウンター ログとイベント ログを視覚化します。
- [Power BI を使用した Azure 監査ログの視覚化](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログ記事
- [Power BI などにおける Azure 監査ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ記事

<!---HONumber=AcomDC_0720_2016-->