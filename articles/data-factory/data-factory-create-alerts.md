<properties 
	pageTitle="Azure Data Factory のイベントに対するアラートの作成" 
	description="データ ファクトリの操作に対して Azure によって生成されたイベントについてのアラートの作成方法を説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Azure イベントに対するアラートの作成
Azure イベントは、Azure のリソースで何が起きているのかを把握するための便利な情報を提供します。Azure では、Azure のリソース (データ ファクトリなど) の作成、更新、または削除時に、ユーザー イベントがログに記録されます。Azure Data Factory の使用時には、次の場合にイベントが生成されます。
 
1.	Azure Data Factory が作成、更新、または削除された場合。
2.	データ処理 (実行と呼びます) が開始または完了した場合。
3.	オンデマンド HDInsight クラスターが作成および削除された場合。

これらのユーザー イベントに対してアラートを作成し、サブスクリプションの管理者と共同管理者に電子メール通知を送信するよう構成できます。さらに、条件が満たされた場合に電子メール通知を受け取る必要があるユーザーの追加の電子メール アドレスを指定できます。

## アラートの定義の指定
アラートの定義を指定するには、アラートの対象となる操作を記述する JSON ファイルを作成します。以下の例では、アラートによって **RunFinished** 操作に関する電子メール通知が送信されます。具体的には、データ ファクトリで実行が完了し、その実行が失敗していた場合 (Status = FailedExecution) に電子メール通知が送信されます。

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

特定のエラーについてアラートを受信しないようにする場合は、上記の JSON 定義から **subStatus** を削除できます。

操作と状態 (および副状態) の一覧については、[使用できる操作と状態](#AvailableOperationsStatuses)のセクションを参照してください。

## アラートのデプロイ
アラートをデプロイするには、次の例に示すように Azure PowerShell コマンドレットの **New-AzureResourceGroupDeployment** を使用します。

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

StorageAccountName は、デプロイしたアラートの JSON ファイルを格納するストレージ アカウントを指定しています。

リソース グループのデプロイメントが正常に終了すると、次のメッセージが表示されます。
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## Azure リソース グループのデプロイメント一覧の取得
デプロイした Azure リソース グループのデプロイメント一覧を取得するには、次の例に示すように、**Get-AzureResourceGroupDeployment** コマンドレットを使用します。
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>使用できる操作の名前と状態値

| 操作の名前 | 状態 | 副状態 |
| -------------- | ------ | ---------- |
| RunStarted | 開始済み | Starting |
| RunFinished | 失敗/成功 |	<p>FailedResourceAllocation </p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p>Canceled</p><p>FailedValidation</p><p>Abandoned</p> | 
| SliceOnTime | In Progress | Ontime |
| SliceDelayed | In Progress | Late |
| OnDemandClusterCreateStarted | 開始済み | |
| OnDemandClusterCreateSuccessful | Succeeded | | 
| OnDemandClusterDeleted | Succeeded | |


## ユーザー イベントのトラブルシューティング
生成されたイベントを表示するには、次のコマンドを実行します。

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=July15_HO4-->