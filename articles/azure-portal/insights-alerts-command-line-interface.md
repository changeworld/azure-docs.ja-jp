<properties
	pageTitle="クロスプラットフォーム コマンド ライン インターフェイス (CLI) での Azure サービス アラートの作成 | Microsoft Azure"
	description="コマンド ライン インターフェイスを使用して Azure アラートを作成します。このアラートにより、指定した条件が満たされたときに通知やオートメーションをトリガーできます。"
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="robb"/>

# クロスプラットフォーム コマンド ライン インターフェイス (CLI) での Azure サービス アラートの作成 

> [AZURE.SELECTOR]
- [ポータル](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## Overview

この記事では、コマンド ライン インターフェイス (CLI) を使用して Azure アラートを設定する方法について説明します。

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

- **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。
- **アクティビティ ログ イベント** - アラートは、"*すべて*" のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

- サービスの管理者/共同管理者に電子メール通知を送信する
- 指定した追加の電子メール アドレスに電子メールを送信する。
- Webhook を呼び出す
- Azure Runbook の実行を開始する (現時点では Azure ポータルからのみ)

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

- [Azure ポータル](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
- [Azure Insights REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


コマンドの末尾に -help を追加して入力すると、そのコマンドのヘルプをいつでも確認することができます。次に例を示します。

	azure insights alerts -help
	azure insights alerts actions email create -help


## CLI を使用してアラート ルールを作成する

1. 前提条件を実行し、Azure にログインします。[Azure Insights CLI サンプル](insights-cli-samples.md)に関するページをご覧ください。簡単に言うと、CLI をインストールし、次のコマンドを実行します。このコマンドによりログインし、使用しているサブスクリプションを表示して、Azure Insights のコマンドを実行する準備をします。


	```console
	azure login
	azure account show
	azure config mode arm 

	```

2.  リソース グループの既存のルールを一覧表示するには、**azure insights alerts rule list** *[オプション] &lt;resourceGroup&gt;* という形式を使用します

	```console
	azure insights alerts rule list myresourcegroupname

	```
3. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。
	- アラートを設定するリソースの**リソース ID**
	- そのリソースに使用できる**メトリック定義**
	
    リソース ID は、Azure ポータルを使用して取得できます。リソースが既に作成されている場合は、ポータルでそのリソースを選択し、次のブレードで "*設定*" セクションの "*プロパティ*" を選択します。次のブレードに "*リソース ID*" フィールドがあります。また、[Azure リソース エクスプローラー](https://resources.azure.com/)を使用することもできます。

    Web アプリのリソース ID の例を次に示します
  
	```
	/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
	```

    この例のリソースで使用できるメトリック、およびそのメトリックの単位の一覧を取得するには、次の CLI コマンドを使用します。

	```console
	azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M 
  	```

	_PT1M_ は、利用可能な測定の粒度 (1 分間隔) です。別の粒度を使用すると、さまざまなメトリック オプションが表示されます。

 
4. メトリック ベースのアラート ルールを作成するには、次の形式のコマンドを使用します。
 
	**azure insights alerts rule metric set** *[オプション] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*
	
	次の例では、Web サイト リソースにアラートを設定します。アラートは、5 分間を通してすべてのトラフィックを受け取り続けたときにトリガーされ、その後 5 分間トラフィックを受け取らなくなった時点で再度トリガーされます。

    ```console
	azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
   
	```

5. アラートが発生したときに webhook を作成するか、電子メールを送信するには、最初に電子メールと webhook、またはそのいずれかを作成します。その後すぐに、ルールを作成します。CLI を使用して、webhook または電子メールを、作成済みのルールと関連付けることはできません。
 
	```console
	azure insights alerts actions email create --customEmails myemail@contoso.com

	azure insights alerts actions webhook create https://www.contoso.com

	azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
	```


6. アクティビティ ログで特定の状況に発生するアラートを作成するには、次の形式を使用します。
 
	**insights alerts rule log set** *[オプション] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;operationName&gt;*

	たとえば、次のように入力します。

	```console
	azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
	```

    operationName は、アクティビティ ログ エントリのイベントの種類に対応します。*Microsoft.Compute/virtualMachines/delete*、*microsoft.insights/diagnosticSettings/write* などが例として挙げられます。

    PowerShell コマンド [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) を使用すると、考えられる operationNames の一覧を取得できます。また、Azure ポータルを使用して、アクティビティ ログを照会し、アラートを作成する過去の操作を検索することもできます。操作は、フレンドリ名のグラフィック ログに表示されます。JSON でエントリを検索し、OperationName 値を取得します。

7. アラートが正しく作成されていることを確かめるには、個別のルールを確認します。

	```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
	```

8. ルールを削除するには、次の形式のコマンドを使用します。

	**insights alerts rule delete** [オプション] &lt;resourceGroup&gt; &lt;ruleName&gt;

	この記事で作成したルールは、次のコマンドで削除します。

	```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
	```



## 次のステップ

* 収集および監視できる情報の種類など、[Azure での監視の概要](monitoring-overview.md)情報を入手します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](..\automation\automation-starting-a-runbook.md) の詳細情報を確認します。
* [診断ログ収集の概要](monitoring-overview-of-diagnostic-logs.md)情報を入手して、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md)情報を入手して、サービスの可用性と応答性を確認します。

<!---HONumber=AcomDC_0928_2016-->