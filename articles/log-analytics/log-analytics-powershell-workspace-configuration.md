---
title: PowerShell を使用した Log Analytics ワークスペースの作成と構成 | Microsoft Docs
description: Log Analytics は、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。 Azure 診断によって生成された場合は、Azure Storage からマシンのデータを収集できます。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 08/15/2016
ms.author: richrund

---
# <a name="manage-log-analytics-using-powershell"></a>PowerShell を使用した Log Analytics の管理
Log Analytics のさまざまな機能は、コマンド ラインまたはスクリプトから [Log Analytics の PowerShell コマンドレット](http://msdn.microsoft.com/library/mt188224.aspx) を使用して実行できます。  PowerShell で実行できる作業の例を次に挙げます。

* ワークスペースの作成
* ソリューションの追加と削除
* 保存されている検索のインポートとエクスポート
* コンピューター グループの作成
* Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
* Linux および Windows コンピューターからのパフォーマンス カウンターの収集
* Linux コンピューターの syslog からのイベントの収集 
* Windows イベント ログからのイベントの収集
* カスタム イベント ログの収集
* Azure 仮想マシンへの Log Analytics エージェントの追加
* Azure 診断を使用して収集されたデータを Log Analytics でインデックスするための構成

この記事の 2 つのコード サンプルで紹介しているのは、PowerShell から実行できる機能の一部です。  その他の機能については、 [Log Analytics の PowerShell コマンドレット リファレンス](http://msdn.microsoft.com/library/mt188224.aspx) を参照してください。

> [!NOTE]
> Log Analytics は以前、オペレーション インサイトと呼ばれていました。そのため、コマンドレットにはその旧称が使用されています。
> 
> 

## <a name="prerequisites"></a>前提条件
Log Analytics ワークスペースで PowerShell を使用するには、次の条件を満たしている必要があります。

* Azure サブスクリプション 
* ご利用の Azure サブスクリプションに関連付けられた Azure Log Analytics ワークスペース

OMS ワークスペースを作成してあっても、まだ Azure サブスクリプションに関連付けていない場合は、以下の方法でリンクを作成できます。

* Azure ポータルで次の操作を行います。
* OMS ポータル 
* Get-AzureRmOperationalInsightsLinkTargets および New-AzureRmOperationalInsightsWorkspace コマンドレットの使用。

## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成と構成
以下のサンプル スクリプトは、次の作業の方法を紹介したものです。

1. ワークスペースの作成
2. 利用可能なソリューションをリストする
3. ソリューションをワークスペースに追加する
4. 保存した検索条件をインポートする
5. 保存した検索条件をエクスポートする
6. コンピューター グループの作成
7. Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
8. Linux コンピューターからの Logical Disk パフォーマンス カウンター (% Used Inodes、Free Megabytes、% Used Space、Disk Transfers/sec、Disk Reads/sec、Disk Writes/sec) の収集
9. Linux コンピューターからの syslog イベントの収集
10. Windows コンピューターのアプリケーション イベント ログからのエラーおよび警告のイベントの収集
11. Windows コンピューターからの Memory Available Mbytes パフォーマンス カウンターの収集
12. カスタム ログの収集 

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

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
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
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

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Azure 診断で収集されたデータのインデックスを作成するように Log Analytics を構成する
エージェントを介さずに Azure リソースを監視するには、リソースで Azure 診断を有効にし、ストレージ アカウントへの書き込みを構成する必要があります。 その後、ストレージ アカウントからログを収集するように Log Analytics を構成します。 上記の構成を行う必要があるリソースには、次のようなものがあります。

* クラシック クラウド サービス (Web ロールと worker ロール)
* Service Fabric クラスター
* ネットワーク セキュリティ グループ
* キー コンテナー 
* アプリケーション ゲートウェイ

PowerShell で特定の Azure サブスクリプションの Log Analytics ワークスペースにアクセスし、別の Azure サブスクリプションからログを収集するように構成することもできます。

以下の例では、次のことを行っています。

1. 既存のストレージ アカウントと Log Analytics でインデックスするデータの収集元とをリストする
2. ストレージ アカウントからデータを読み取るための構成を作成する
3. 新しく作成した構成に変更を加え、インデックスの作成対象となるデータの収集元を追加する
4. 新しく作成した構成を削除する

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
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

## <a name="next-steps"></a>次のステップ
* [Log Analytics の PowerShell コマンドレットを参照](http://msdn.microsoft.com/library/mt188224.aspx) し、Log Analytics を構成するための PowerShell の使い方について詳しく調べる。

<!--HONumber=Oct16_HO2-->


