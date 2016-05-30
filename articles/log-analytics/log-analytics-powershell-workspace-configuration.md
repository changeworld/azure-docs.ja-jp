<properties
	pageTitle="PowerShell を使用した Log Analytics ワークスペースの作成と構成 | Microsoft Azure"
	description="Log Analytics は、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。Azure 診断によって生成された場合は、Azure Storage からマシンのデータを収集できます。"
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# PowerShell を使用した Log Analytics の管理

Log Analytics のさまざまな機能は、コマンド ラインまたはスクリプトから [Log Analytics の PowerShell コマンドレット](http://msdn.microsoft.com/library/mt188224.aspx)を使用して実行できます。PowerShell で実行できる作業の例を次に挙げます。

+ ワークスペースの作成
+ ソリューションの追加と削除
+ 保存されている検索のインポートとエクスポート
+ Azure 仮想マシンへの Log Analytics エージェントの追加
+ Azure 診断を使用して収集されたデータを Log Analytics でインデックスするための構成

この記事の 2 つのコード サンプルで紹介しているのは、PowerShell から実行できる機能の一部です。その他の機能については、[Log Analytics の PowerShell コマンドレット リファレンス](http://msdn.microsoft.com/library/mt188224.aspx)を参照してください。

> [AZURE.NOTE] Log Analytics は以前、オペレーション インサイトと呼ばれていました。そのため、コマンドレットにはその旧称が使用されています。

## 前提条件

Log Analytics ワークスペースで PowerShell を使用するには、次の条件を満たしている必要があります。

+ Azure サブスクリプション 
+ ご利用の Azure サブスクリプションに関連付けられた Azure Log Analytics ワークスペース

OMS ワークスペースは作成済みであるが、Azure サブスクリプションへの関連付けが済んでいない場合は、Azure ポータルか OMS ポータルの中で関連付けを作成できます。または Get-AzureRMOperationalInsightsLinkTargets コマンドレットと New-AzureRMOperationalInsightsWorkspace コマンドレットを使用して関連付けを行うこともできます。

## Log Analytics ワークスペースを作成してソリューションと保存した検索条件を追加する

以下のサンプル スクリプトは、次の作業の方法を紹介したものです。

1.	ワークスペースを作成する
2.	利用可能なソリューションをリストする
3.	ソリューションをワークスペースに追加する
4.	保存した検索条件をインポートする
5.	保存した検索条件をエクスポートする

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Azure 診断で収集されたデータのインデックスを作成するように Log Analytics を構成する 

エージェントを介さずに Azure のリソース (Web ロール、worker ロール、Service Fabric クラスター、ネットワーク セキュリティ グループ、Key Vault、Application Gateway など) を監視するためには、まずリソースの Azure 診断を有効にし、その出力先をストレージ アカウントに設定する必要があります。そのうえで、そのストレージ アカウントからログを収集するように Log Analytics を構成します。

PowerShell で特定の Azure サブスクリプションの Log Analytics ワークスペースにアクセスし、別の Azure サブスクリプションからログを収集するように構成することもできます。

以下の例では、次のことを行っています。

1.	既存のストレージ アカウントと Log Analytics でインデックスするデータの収集元とをリストする
2.	ストレージ アカウントからデータを読み取るための新しい構成を作成する
3.	新しく作成した構成に変更を加え、インデックスの作成対象となるデータの収集元を追加する
4.	新しく作成した構成を削除する

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## 次のステップ

- [Log Analytics の PowerShell コマンドレットを参照](http://msdn.microsoft.com/library/mt188224.aspx)し、Log Analytics を構成するための PowerShell の使い方について詳しく調べる。

<!---HONumber=AcomDC_0518_2016-->