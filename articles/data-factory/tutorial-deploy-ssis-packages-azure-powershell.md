---
title: PowerShell を使用して SSIS パッケージを Azure にデプロイする | Microsoft Docs
description: この記事では、PowerShell を使用して SSIS パッケージを Azure にデプロイし、Azure-SSIS 統合ランタイムを作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 3b127eded5e9f9bc921e5830975513c9e7233a2c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure-with-powershell"></a>PowerShell を使用して SQL Server Integration Services パッケージを Azure にデプロイする
このチュートリアルでは、Azure-SSIS 統合ランタイム (IR) を Azure Data Factory にプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイできます。 このチュートリアルでは、次の手順を実行します。

> [!NOTE]
> この記事では、Azure PowerShell を使用して Azure SSIS IR をプロビジョニングします。 Data Factory ユーザー インターフェイス (UI) を使用して Azure SSIS IR をプロビジョニングするには、[Azure SSIS 統合ランタイムを作成する方法のチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)に関するページを参照してください。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * SSIS パッケージをデプロイします
> * 完全なスクリプトを確認します

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事をご覧ください。
- **Azure SQL Database サーバー**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 このサーバーは、SSIS カタログ データベース (SSISDB) をホストします。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が **[オン]** であることを確認します。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。 
    - Azure SQL Database サーバーに SSIS カタログ (SSIDB データベース) がないことを確認します。 Azure SSIS IR のプロビジョニングでは、既存の SSIS カタログの使用がサポートされていません。 
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。 SSIS パッケージをクラウドで実行する Azure-SSIS 統合ランタイムは、スクリプトを実行してプロビジョニングします。PowerShell はその実行に使用します。 

> [!NOTE]
> - バージョン 2 のデータ ファクトリは、米国東部、米国東部 2、東南アジア、西ヨーロッパの各リージョンで作成できます。 
> - Azure-SSIS IR は、米国東部、米国東部 2、米国中部、米国西部 2、北ヨーロッパ、西ヨーロッパ、英国南部、オーストラリア東部の各リージョンで作成できます。

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE の起動
管理特権を使用して **Windows PowerShell ISE** を起動します。 

## <a name="create-variables"></a>変数の作成
次のスクリプトをコピーして貼り付け、変数の値を指定します。 Azure SQL Database でサポートされている**価格レベル**の一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

# You can create Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, Australia East. 
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>データベースへの接続の検証
Azure SQL Database サーバー (`<server>.database.windows.net`) を検証するには、次のスクリプトを追加します。 

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

スクリプトの一環として Azure SQL データベースを作成するには、次の例を参照してください。 

まだ定義されていない変数の値を設定します。 たとえば、FirewallIPAddress です。 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>ログインとサブスクリプションの選択
ログインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

リソース グループが既に存在する場合は、このコードをスクリプトにコピーしないでください。 

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
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri 
```

## <a name="start-integration-runtime"></a>統合ランタイムの起動
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
このコマンドは、完了までに **20 ～ 30 分**かかります。 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストする Azure SQL サーバーに接続します。 Azure SQL サーバーの名前は、`<servername>.database.windows.net` の形式になります (Azure SQL Database の場合)。 

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>完全なスクリプト
このリリースでは、クラウド内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムのインスタンスをプロビジョニングするには、PowerShell を使用する必要があります。 現時点では、Azure Portal を使ってこのランタイムをプロビジョニングすることはできません。 

このセクション内の PowerShell スクリプトでは、SSIS パッケージを実行するクラウド内の Azure-SSIS 統合ランタイムのインスタンスを構成します。 このスクリプトを正常に実行したら、Azure SQL Database でホストされた SSISDB と共に Microsoft Azure クラウドに SSIS パッケージをデプロイして実行できます。

1. Windows PowerShell Integrated Scripting Environment (ISE) を起動します。
2. ISE のコマンド プロンプトで次のコマンドを実行します。    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. このセクションの PowerShell スクリプトをコピーして ISE に貼り付けます。
4. スクリプトの先頭にある "SSIS in Azure specifications" セクション内のスクリプト パラメーターに適切な値を指定します。 これらのパラメーターについては、次のセクションで説明します。
5. スクリプトを実行します。 スクリプトの最後の方にある `Start-AzureRmDataFactoryV2IntegrationRuntime` コマンドは、実行に **20 ～ 30 分**かかります。

> [!NOTE]
> - このスクリプトでは、Azure SQL データベースに接続して SSIS カタログ データベース (SSISDB) を準備します。 また、VNet (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを VNet に参加させます。
> - Azure-SSIS IR のインスタンスをプロビジョニングすると、Azure Feature Pack for SSIS と Access の再頒布可能パッケージもインストールされます。 これらのコンポーネントは、組み込みのコンポーネントでサポートされるデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントをインストールすることもできます。 詳細については、「[Custom setup for the Azure-SSIS integration runtime (Azure SSIS 統合ランタイムのカスタム セットアップ)](how-to-configure-azure-ssis-ir-custom-setup.md)」を参照してください。


Azure SQL Database でサポートされている**価格レベル**の一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。 

Azure Data Factory V2 および Azure-SSIS Integration Runtime でサポートされているリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 **[データ + 分析]** を展開して、**[Data Factory V2]** と **[SSIS Integration Runtime]** を表示します。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

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
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>VNet への Azure-SSIS IR の参加
Azure SQL マネージ インスタンス (プレビュー) を使用して、仮想ネットワーク (VNet) 内で SQL Server Integration Services (SSIS) カタログをホストしている場合、Azure-SSIS 統合ランタイムも同じ仮想ネットワークに参加させる必要があります。 Azure Data Factory バージョン 2 (プレビュー) では、Azure-SSIS 統合ランタイムを VNet に参加させることができます。 詳細については、[VNet への Azure-SSIS ランタイムの参加](join-azure-ssis-integration-runtime-virtual-network.md)に関する記事をご覧ください。

VNet に参加させる Azure-SSIS ランタイムを作成する完全なスクリプトについては、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事をご覧ください。

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR の監視と管理
Azure-SSIS IR の監視と管理の詳細については、次の記事をご覧ください。 

- [Azure-SSIS 統合ランタイムの監視](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS 統合ランタイムの管理](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * SSIS パッケージをデプロイします
> * 完全なスクリプトを確認します

次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[クラウドのデータをコピーする](tutorial-copy-data-dot-net.md)
