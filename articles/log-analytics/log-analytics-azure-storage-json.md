---
title: Log Analytics を使用した Azure 診断ログの分析 | Microsoft Docs
description: Log Analytics は、Azure 診断ログを JSON 形式で Blob Storage に出力する Azure サービスからログを読み取ることができます。
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Log Analytics を使用した Azure 診断ログの分析
Log Analytics は、[Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を JSON 形式で Blob Storage に出力する以下の Azure サービスのログを収集することができます。

* Automation (プレビュー)
* Key Vault (プレビュー)
* Application Gateway (プレビュー)
* ネットワーク セキュリティ グループ (プレビュー)

以降のセクションでは、PowerShell を使用して以下のことを行います。

* 各リソースのストレージからログを収集するように Log Analytics を構成する  
* Azure サービスに対して Log Analytics ソリューションを有効にする

Log Analytics でこれらのリソースのデータを収集する前に、Azure 診断を有効にする必要があります。 ログを有効にするには `Set-AzureRmDiagnosticSetting` コマンドレットを使用します。

診断ログを有効にする方法の詳細については、次の記事を参照してください。

* [Key Vault](../key-vault/key-vault-logging.md)
* [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
* [ネットワーク セキュリティ グループ](../virtual-network/virtual-network-nsg-manage-log.md)

このドキュメントでは、次の情報についても詳しく取り上げています。

* データ収集のトラブルシューティング
* データ収集の停止

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Azure 診断ログを Log Analytics で収集するための構成
これらのサービスのログを収集したり、ログを可視化したりするためには、PowerShell スクリプトを使用します。

以下の例では、サポートされている全リソースのログを有効にしています。

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


一部のリソースについては、前述の構成を Azure Portal から「[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)」に書かれている手順で行うことができます。

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Azure 診断ログを Log Analytics で収集するための構成
Log Analytics の構成を支援するための次の 2 つのコマンドレットをエクスポートする PowerShell スクリプト モジュールが用意されています。

1. `Add-AzureDiagnosticsToLogAnalyticsUI` は、ユーザーに入力を求め、単純な構成をセットアップすることができます。
2. `Add-AzureDiagnosticsToLogAnalytics` は、監視するリソースを入力として受け取り、Log Analytics を構成します。  

### <a name="pre-requisites"></a>前提条件
1. Azure PowerShell と、バージョン 1.0.8 以降の Operational Insights コマンドレット。
   * [Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)
   * コマンドレットのバージョンを確認します: `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. 診断ログが、監視する Azure リソース用に構成されていること。 `Set-AzureRmDiagnosticSetting` を使用するか、または [Log Analytics を使用して Azure Storage アカウントからデータを収集する方法](log-analytics-azure-storage.md)のページで診断を有効にする方法を参照してください。
3. [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) ワークスペース  
4. AzureDiagnosticsAndLogAnalytics PowerShell モジュール
   * [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) モジュールを PowerShell ギャラリーからダウンロードします。

### <a name="option-1:-run-the-interactive-configuration-scripts"></a>オプション 1: 対話型の構成スクリプトを実行する
PowerShell を開き、以下を実行します。

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

選択肢の一覧が表示され、選択するように求められます。
次の各項目についての選択を求められます。

* ログの収集対象となるリソースの種類 (Azure Services)
* ログの収集対象となるリソース インスタンス
* データを収集する Log Analytics ワークスペース

このスクリプトを実行すると、新しい診断データがストレージに書き込まれてから約 30 分後に、Log Analytics にレコードが表示されます。 その時間が過ぎてもレコードが表示されない場合は、後のトラブルシューティングのセクションを参照してください。

### <a name="option-2:-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>オプション 2: リソースの一覧を作成して、構成コマンドレットに渡す
Azure 診断が有効になっているリソースの一覧を作成し、リソースを構成コマンドレットに渡すことができます。

`Get-Help Add-AzureDiagnosticsToLogAnalytics` を実行すると、コマンドレットに関する追加情報を表示できます。

OMS の詳細については、[Log Analytics PowerShell コマンドレット](https://msdn.microsoft.com/library/mt188224.aspx)のページを参照してください。

> [!NOTE]
> リソースとワークスペースが異なる Azure サブスクリプションに属している場合は、`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>` を使用して適宜切り替えてください。
> 
> 

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
このスクリプトを実行すると、新しい診断データがストレージに書き込まれてから約 30 分後に、Log Analytics にレコードが表示されます。 その時間が過ぎてもレコードが表示されない場合は、後のトラブルシューティングのセクションを参照してください。  

> [!NOTE]
> この構成は、Azure Portal からは見えません。 構成を確認するには、 `Get-AzureRmOperationalInsightsStorageInsight` コマンドレットを使用します。  
> 
> 

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Log Analytics による Azure 診断ログの収集を停止する
特定のリソースに対する Log Analytics の構成を削除するには、`Remove-AzureRmOperationalInsightsStorageInsight` コマンドレットを使用します。

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Azure 診断ログの構成のトラブルシューティング
*問題*

クラシック ストレージにログを出力するリソースの構成中に次のエラーが表示されます。

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*解決策*

`Add-AzureAccount` を使用して、クラシック デプロイ モデルの API にログインします。

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Azure 診断ログのデータ収集のトラブルシューティング
Log Analytics で Azure リソースのデータが表示されない場合は、次のトラブルシューティング手順を行います。

* データがストレージ アカウントに送信されていることを確認する
* サービスに対して Log Analytics ソリューションが有効になっていることを確認する
* Log Analytics がストレージから読み取りを行うように構成されていることを確認する
* ストレージ アカウント キーを更新する

### <a name="verify-data-is-flowing-to-the-storage-account"></a>データがストレージ アカウントに送信されていることを確認する
データがきちんとストレージ アカウントに出力されているかどうかは、Azure Storage を閲覧できるツール (Visual Studio など) や PowerShell を使って確認できます。

リソースでログの作成先として構成されているストレージ アカウントを検索するには、次の PowerShell を使用します。

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Azure 診断で使用されているストレージ コンテナーは、次の形式の名前を持っています。  

`insights-logs-<Category>`

ストレージ アカウントの内容を表示する方法の詳細については、[ストレージ コマンドレットによる最新のデータの内容の確認](../storage/storage-powershell-guide-full.md)に関するページを参照してください。

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>サービスに対して Log Analytics ソリューションが有効になっていることを確認する
`Add-AzureDiagnosticsToLogAnalyticsUI` を使用すると、適切な Log Analytics ソリューションが自動的に有効になります。

ソリューションが有効になっていることを確認するには、次の PowerShell を実行します。

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

ソリューションが有効になっていない場合は、次の PowerShell を使用して有効にすることができます。

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

各リソースの種類に対して有効にするソリューションの名前を検索するには、次の PowerShell を使用します (この変数は、モジュールをインポートすると使用できるようになります)。

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Log Analytics がストレージから読み取りを行うように構成されていることを確認する
さらに Azure リソースを追加する場合は、そのリソースの診断ログを有効にしたうえで、そのリソースを使用するために必要な構成を Log Analytics に対して行う必要があります。
どのリソースとストレージ アカウントのログを収集するように Log Analytics が構成されているかを確認するには、次の PowerShell を使用します。

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>ストレージ キーを更新する
ストレージ アカウントのキーを変更する場合、Log Analytics の構成も新しいキーで更新する必要があります。
Log Analytics の構成を新しいキーで更新するには、次の PowerShell を使用します。

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

ストレージ インサイト名を検索するには、これまでの例で示されているように、 `Get-AzureRmOperationalInsightsStorageInsight` コマンドレットを使用します。

## <a name="next-steps"></a>次のステップ
* [Blob Storage (IIS の場合) と Table Storage (イベントの場合) を使用](log-analytics-azure-storage-iis-table.md)して、診断情報を Table Storage に出力する Azure サービスのログや、Blob Storage に出力された IIS ログを読み取ります。
* [ソリューションを有効](log-analytics-add-solutions.md) にして、データに対する洞察を得ます。
* [検索クエリを使用](log-analytics-log-searches.md) して、データを分析します。

<!--HONumber=Oct16_HO2-->


