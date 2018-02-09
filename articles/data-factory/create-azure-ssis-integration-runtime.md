---
title: "Azure Data Factory で Azure-SSIS 統合ランタイムを作成する | Microsoft Docs"
description: "Azure クラウドで SSIS パッケージを実行できるように Azure-SSIS 統合ランタイムを作成する方法を説明します。"
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
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: bb63a3d882d50f509fff220d3eb2c1eb6bf0d70f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で Azure-SSIS 統合ランタイムを作成する
この記事では、Azure Data Factory で Azure-SSIS 統合ランタイムをプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイできます。

チュートリアル「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md)」では、SSIS カタログのストアとして Azure SQL Database を使用して Azure SSIS Integration Runtime (IR) を作成する方法を説明しました。 この記事では、チュートリアルをさらに掘り下げ、次の操作を行う方法を示します。 

- Azure SQL マネージ インスタンス (プライベート プレビュー) を使用して、SSIS カタログ (SSISDB データベース) をホストする。
- Azure-SSIS IR を Azure 仮想ネットワーク (VNet) に参加させる。 VNet への Azure SSIS IR の参加と、Azure Portal での VNet の構成に関する概念的な情報については、[VNet への Azure SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md)に関する記事をご覧ください。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。


## <a name="overview"></a>概要
この記事では、Azure SSIS IR のさまざまなプロビジョニング方法を示します。

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager テンプレート](#azure-resource-manager-template)

Azure-SSIS IR 作成すると、Data Factory は、Azure SQL Database に接続して SSIS カタログ データベース (SSISDB) を準備します。 また、VNet (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを VNet に参加させます。

Azure-SSIS IR のインスタンスをプロビジョニングすると、Azure Feature Pack for SSIS と Access の再頒布可能パッケージもインストールされます。 これらのコンポーネントは、組み込みのコンポーネントでサポートされるデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 現時点では、(Attunity による Oracle コンポーネントおよび Teradata コンポーネント、SAP BI コンポーネントなど、Microsoft が提供するサードパーティ コンポーネントも含め) SSIS 用のサードパーティ コンポーネントをインストールすることはできません。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 サブスクリプションがない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。
- **Azure SQL Database サーバー**または **SQL Server マネージ インスタンス (プライベート プレビュー) (延長プライベート プレビュー)**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 このサーバーは、SSIS カタログ データベース (SSISDB) をホストします。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 Azure SQL Server の価格レベルを書き留めておいてください。 Azure SQL Database でサポートされている価格レベルの一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。

    Azure SQL Database サーバーまたは SQL Server マネージ インスタンス (延長プライベート プレビュー) に、SSIS カタログ (SSIDB データベース) がないことを確認します。 Azure SSIS IR のプロビジョニングでは、既存の SSIS カタログの使用がサポートされていません。
- **クラシックまたは Azure Resource Manager Virtual Network (VNet) (省略可能)**。 次の条件に 1 つでも当てはまる場合は、Azure 仮想ネットワーク (VNet) が必要です。
    - VNet の一部である SQL Server マネージ インスタンス (プライベート プレビュー) 上の SSIS カタログ データベースをホストしている。
    - Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。 SSIS パッケージをクラウドで実行する Azure-SSIS 統合ランタイムは、スクリプトを実行してプロビジョニングします。PowerShell はその実行に使用します。 

> [!NOTE]
> Azure Data Factory V2 および Azure-SSIS Integration Runtime でサポートされているリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 **[データ + 分析]** を展開して、**[Data Factory V2]** と **[SSIS Integration Runtime]** を表示します。

## <a name="azure-portal"></a>Azure ポータル
このセクションでは、Azure ポータル、具体的にはデータ ファクトリ UI を使用して、Azure-SSIS IR を作成します。 

### <a name="create-a-data-factory"></a>Data Factory を作成する。

1. [Azure Portal](https://portal.azure.com/) にログインします。    
2. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**MyAzureSsisDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムをプロビジョニングする

1. 開始ページで、**[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 統合ランタイムの**名前**を指定します。
    2. 統合ランタイムの**場所**を指定します。 サポートされている場所のみが表示されます。
    3. SSIS ランタイムで構成する**ノードのサイズ**を選択します。
    4. クラスター内の**ノードの数**を指定します。
    5. **[次へ]** をクリックします。 
1. **[SQL の設定]** で、次の手順を実行します。 

    ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Azure SQL サーバーを保有する Azure **サブスクリプション**を指定します。 
    2. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で Azure SQL サーバーを選択します。
    3. **管理者**のユーザー名を入力します。
    4. 管理者の**パスワード**を入力します。  
    5. SSISDB データベースの**サービス レベル**を選択します。 既定値は Basic です。
    6. **[次へ]** をクリックします。 
1.  **[詳細設定]** ページで、**[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** の値を選択します。   

    ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. この手順は**省略可能**です。 統合ランタイムを参加させる VNet (クラシックまたは Azure Resource Manager) がある場合は、**[Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings]\(Azure-SSIS 統合ランタイムが参加する VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** オプションを選択し、次の手順を実行します。 

    ![VNet の詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. **[サブスクリプション]** で、VNet を保有する**サブスクリプション**を指定します。 
    2. [種類] で、VNet の**種類** (クラシック仮想ネットワークまたは Azure Resource Manager 仮想ネットワーク) を指定します。 
    3. **[VNet 名]** で、**VNet**の名前を選択します。 
    4. **[サブネット名]** で、VNet の**サブネット**の名前を選択します。
1. **[完了]** をクリックして、Azure-SSIS 統合ランタイムの作成を開始します。 

    > [!IMPORTANT]
    > - このプロセスは、完了するまで約 20 分かかります
    > - Data Factory サービスは、Azure SQL Database に接続して SSIS カタログ データベース (SSISDB) を準備します。 また、VNet (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを VNet に参加させます。
7. **[接続]** ウィンドウで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** をクリックして、状態を更新します。 

    ![作成の状態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. **[アクション]** 列のリンクを使用して、統合ランタイムを停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。 

    ![Azure SSIS IR - アクション](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>ポータルでの Azure SSIS 統合ランタイム

1. Azure Data Factory UI で、**[編集]** タブに切り替えます。**[接続]** をクリックし、**[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 
    ![既存の IR を表示する](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. **[新規]** をクリックして新しい Azure-SSIS IR を作成します。 

    ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Azure-SSIS 統合ランタイムを作成するには、図に示すように **[新規]** をクリックします。 
3. [Integration Runtime Setup]\(統合ランタイムの設定\) ウィンドウで、**[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、**[次へ]** をクリックします。

    ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。

## <a name="azure-powershell"></a>Azure PowerShell
このセクションでは、Azure PowerShell を使用して、Azure-SSIS IR を作成します。

### <a name="create-variables"></a>変数の作成
このチュートリアルのスクリプトで使用する変数を定義します。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>ログインとサブスクリプションの選択
ログインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>データベースへの接続の検証
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

### <a name="configure-virtual-network"></a>仮想ネットワークを構成する
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
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>リソース グループの作成
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data Factory を作成する。
Data Factory を作成するには、次のコマンドを実行します。

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>統合ランタイムの作成
Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。使用するデータベースの種類 (Azure SQL Database または Azure SQL マネージ インスタンス (プライベート プレビュー)) に該当するセクションのスクリプトを使用してください。 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Azure SQL Database を使用して SSISDB データベース (SSIS カタログ) をホストする 

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

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Azure SQL マネージ インスタンス (プライベート プレビュー) を使用して SSISDB データベースをホストする

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

### <a name="start-integration-runtime"></a>統合ランタイムの起動
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


### <a name="full-script"></a>完全なスクリプト
Azure-SSIS IR を作成して VNet に参加させるための完全なスクリプトを次に示します。 このスクリプトでは、Azure SQL マネージ インスタンス (MI) を使用して SSIS カタログをホストしていることを前提としています。 

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
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
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

Login-AzureRmAccount
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
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
このセクションでは、Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを作成します。 サンプル チュートリアルを次に示します。 

1. 次の Resource Manager テンプレートを使用して、JSON ファイルを作成します。 山かっこ内の値 (プレース ホルダー) を独自の値に置き換えます。 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Resource Manager テンプレートをデプロイするには、次の例に示すように、New-AzureRmResourceGroupDeployment コマンドを実行します。 この例で、ADFTutorialResourceGroup はリソース グループの名前です。 ADFTutorialARM.json はデータ ファクトリおよび Azure-SSIS IR の JSON 定義を含むファイルです。 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    このコマンドは、データ ファクトリを作成し、その中に Azure-SSIS IR を作成しますが、IR を起動しません。 
3. Azure-SSIS IR を起動するには、Start-AzureRmDataFactoryV2IntegrationRuntime コマンドを実行します。 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストする Azure SQL サーバーに接続します。 Azure SQL Server の名前は、&lt;servername&gt;.database.windows.net の形式になります (Azure SQL Database の場合)。 手順については、[パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [Azure-SSIS IR を VNet に参加させる](join-azure-ssis-integration-runtime-virtual-network.md):  この記事では、Azure 仮想ネットワーク (VNet) への Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が VNet に参加できるように Azure Portal を使って VNet を構成する手順も説明されています。 