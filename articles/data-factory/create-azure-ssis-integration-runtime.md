---
title: "Azure Data Factory での自己ホスト型統合ランタイムの作成 | Microsoft Docs"
description: "SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 8cc4d44bff6284f2c52423f04e463e324a932abd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2017
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で Azure-SSIS 統合ランタイムを作成する
この記事では、Azure Data Factory で Azure-SSIS 統合ランタイムをプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイできます。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

チュートリアル「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md)」では、SSIS カタログのストアとして Azure SQL Database を使用して Azure SSIS 統合ランタイム (IR) を作成する方法を説明しています。 この記事では、チュートリアルをさらに掘り下げ、次の操作を行う方法を示します。 

- Azure SQL マネージ インスタンス (プライベート プレビュー) を使用して、SSIS カタログ (SSISDB データベース) をホストする。
- Azure-SSIS IR を Azure 仮想ネットワーク (VNet) に参加させる。 

VNet への Azure SSIS IR の参加と、Azure Portal での VNet の構成に関する概念的な情報については、[VNet への Azure SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md)に関する記事をご覧ください。 

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 サブスクリプションがない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。
- **Azure SQL Database サーバー**または **SQL Server マネージ インスタンス (プライベート プレビュー) (延長プライベート プレビュー)**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 このサーバーは、SSIS カタログ データベース (SSISDB) をホストします。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 Azure SQL Server の価格レベルを書き留めておいてください。 Azure SQL Database でサポートされている価格レベルの一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。
- **クラシック Virtual Network (VNet) (省略可能)**。 次の条件に 1 つでも当てはまる場合は、Azure 仮想ネットワーク (VNet) が必要です。
    - VNet の一部である SQL Server マネージ インスタンス (プライベート プレビュー) 上の SSIS カタログ データベースをホストしている。
    - Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。 SSIS パッケージをクラウドで実行する Azure-SSIS 統合ランタイムは、スクリプトを実行してプロビジョニングします。PowerShell はその実行に使用します。 

> [!NOTE]
> Azure Data Factory V2 および Azure-SSIS Integration Runtime でサポートされているリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 **[データ + 分析]** を展開して、**[Data Factory V2]** と **[SSIS Integration Runtime]** を表示します。


## <a name="create-variables"></a>変数の作成
このチュートリアルのスクリプトで使用する変数を定義します。

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

```

## <a name="validate-the-connection-to-database"></a>データベースへの接続の検証
Azure SQL Database サーバー (server.database.windows.net) または Azure SQL マネージ インスタンス (プライベート プレビュー) サーバー エンドポイントを検証するには、次のスクリプトを追加します。 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

## <a name="log-in-and-select-subscription"></a>ログインとサブスクリプションの選択
ログインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する
Azure-SSIS 統合ランタイムが参加するための仮想ネットワークのアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Data Factory を作成する。
Data Factory を作成するには、次のコマンドを実行します。

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>統合ランタイムの作成
Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。 

**Azure SQL Database** を使用して SSISDB データベース (SSIS カタログ) をホストする場合。 


```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

オンプレミスのデータ アクセスが不要な場合、つまりオンプレミスのデータ ソース/移動先が SSIS パッケージ内にある場合は、VNetId とサブネットの値を渡す必要はありません。 CatalogPricingTier パラメーターの値を渡す必要があります。 Azure SQL Database でサポートされている価格レベルの一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。

**Azure SQL マネージ インスタンス (プライベート プレビュー)** を使用して、SSISDB データベースをホストする場合。

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

VNet に参加させる Azure SQL マネージ インスタンス (プライベート プレビュー) に、VnetId とサブネットのパラメーターの値を渡す必要があります。 CatalogPricingTier パラメーターは、Azure SQL のマネージ インスタンスには適用されません。 

## <a name="start-integration-runtime"></a>統合ランタイムの起動
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
このコマンドは、完了までに **20 ～ 30 分**かかります。 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストする Azure SQL サーバーに接続します。 Azure SQL Server の名前は、&lt;servername&gt;.database.windows.net の形式になります (Azure SQL Database の場合)。 手順については、[パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [Azure-SSIS IR を VNet に参加させる](join-azure-ssis-integration-runtime-virtual-network.md):  この記事では、Azure 仮想ネットワーク (VNet) への Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が VNet に参加できるように Azure Portal を使って VNet を構成する手順も説明されています。 