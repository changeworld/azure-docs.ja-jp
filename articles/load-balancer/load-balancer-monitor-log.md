<properties 
   pageTitle="Load Balancer に関する操作、イベント、カウンターの監視 | Microsoft Azure"
   description="Azure Load Balancer でアラート イベントとプローブの正常性状態のログを有効にする方法について説明します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="joaoma" />

# Azure Load Balancer のログ分析 (プレビュー)
Azure の各種ログを使用して、ロード バランサーの管理やトラブルシューティングを行うことができます。これらのログの一部はポータルからアクセスできます。また、すべてのログは、Azure BLOB ストレージから抽出して Excel や PowerBI などのさまざまなツールで表示することができます。各種ログの詳細については、以下の一覧を参照してください。


- **監査ログ:** [Azure 監査ログ](insights-debugging-with-events.md) (以前の操作ログ) を使用すると、Azure サブスクリプションに送信されているすべての操作とその操作の状態を表示できます。監査ログは既定で有効になっており、Azure ポータルで表示できます。
- **アラート イベント ログ:** ロード バランサーで発生したアラートを確認できます。ロード バランサーの状態は 5 分ごとに収集されます。このログは、ロード バランサーのアラート イベントが発生した場合にのみ書き込まれます。  
- **正常性プローブ ログ:** プローブの正常性チェックの状態を確認し、ロード バランサーのバックエンドでオンラインになっているインスタンスの数や、ロード バランサーからネットワーク トラフィックを受信している仮想マシンの割合を調べることができます。このログは、プローブ状態イベントの変更時に書き込まれます。

>[AZURE.WARNING] ログは、リソース マネージャーのデプロイメント モデルでデプロイされたリソースについてのみ使用できます。クラシック デプロイメント モデルのリソースには使用できません。2 つのモデルについて理解を深めるには、「[リソース マネージャー デプロイメントと従来のデプロイメントを理解する](resource-manager-deployment-model.md)」を参照してください。<BR> ログ分析は現在、インターネットに接続するロード バランサーに対してのみ機能します。この制限は一時的なものであり、いつでも変更される可能性があります。今後の変更を確認するには、このページに再度アクセスしてください。

## ログの有効化
監査ログは、リソース マネージャーのすべてのリソースで常に自動的に有効になります。イベント ログと正常性プローブ ログでデータの収集を開始するには、ログを有効にする必要があります。ログを有効にするには、次の手順に従います。

[Azure ポータル](http://portal.azure.com)にサインインします。ロード バランサーをまだ作成していない場合は、先に進む前に [ロード バランサーを作成](load-balancer-internet-arm-ps.md)します。

ポータルで、**[参照]**、**[ロード バランサー]** の順にクリックします。

![portal - load-balancer](./media/load-balancer-monitor-log/load-balancer-browse.png)

既存のロード バランサーを選択し、**[すべての設定]** をクリックします。

![portal - load-balancer-settings](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

**[設定]** ブレードで **[診断]** をクリックし、**[診断]** ウィンドウで **[状態]** の横の **[オン]** をクリックします。**[設定]** ブレードで **[ストレージ アカウント]** をクリックし、既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。

**[ストレージ アカウント]** の下にあるドロップダウン リストで、アラート イベント、プローブの正常性状態、またはその両方から記録するログを選択し、**[保存]** をクリックします。

![Preview portal - Diagnostics logs](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] 監査ログでは別のストレージ アカウントは必要ありません。イベントと正常性プローブのログ記録にストレージを使用すると、サービス料金が発生します。

## 監査ログ
監査ログ (以前の "操作ログ") は、既定では Azure によって生成されます。ログは、Azure のイベント ログ ストアに 90 日間保存されます。これらのログの詳細については、「[イベント ログと監査ログの表示](insights-debugging-with-events.md)」を参照してください。

## アラート イベント ログ
このログは、既に詳しく説明したように、ロード バランサーごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。次に示すように、JSON 形式で情報が記録されます。

	
	{
    "time": "2016-01-26T10:37:46.6024215Z",
	"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

JSON 形式の出力で *eventname* プロパティを見ると、ロード バランサーでアラートが生成された理由がわかります。この例では、アラートが生成された理由は、ソース IP NAT (SNAT) の制限により TCP ポートが枯渇したことです。

## 正常性プローブ ログ
このログは、既に詳しく説明したように、ロード バランサーごとにログを有効にした場合にのみ生成されます。データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。'insights-logs-loadbalancerprobehealthstatus' という名前のコンテナーが作成され、次のデータがログに記録されます。

		{
	    "records":

	    {
	   		"time": "2016-01-26T10:37:46.6024215Z",
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "time": "2016-01-26T10:37:46.6024215Z",
			"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

JSON 形式の出力でプロパティ フィールドを見れば、プローブの正常性状態の基本的な情報がわかります。*DipDownCount* プロパティは、プローブの応答の失敗によりネットワーク トラフィックを受信していないバックエンド上のインスタンスの合計数を示します。

## 監査ログの表示と分析
次のいずれかの方法を使用して、監査ログのデータを表示および分析できます。

- **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure プレビュー ポータルを使用して、監査ログから情報を取得します。それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](resource-group-audit.md)」を参照してください。
- **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。[Power BI 用 Azure 監査ログ コンテンツ パック](https://support.powerbi.com/knowledgebase/articles/742695)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## 正常性プローブ ログとイベント ログの表示と分析 
イベント ログと正常性プローブ ログの場合は、自身のストレージ アカウントに接続して JSON ログ エントリを取得する必要があります。JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

>[AZURE.TIP] Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、Github から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## その他のリソース

- [Power BI を使用した Azure 監査ログの視覚化](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログ記事
- [Power BI などにおける Azure 監査ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ記事

<!---HONumber=AcomDC_0204_2016-->