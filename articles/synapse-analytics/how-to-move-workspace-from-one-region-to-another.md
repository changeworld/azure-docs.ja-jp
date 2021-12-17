---
title: 一方のリージョンから他方に Azure Synapse Analytics ワークスペースを移動する
description: この記事では、一方のリージョンから他方に Azure Synapse Analytics ワークスペースを移動する方法について説明します。
author: phanir
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 08/16/2021
ms.author: phanir
ms.reviewer: jrasnick
ms.openlocfilehash: 6ad246eefb86c31291d2a9745c6f77e276701744
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214087"
---
# <a name="move-an-azure-synapse-analytics-workspace-from-one-region-to-another"></a>一方のリージョンから他方に Azure Synapse Analytics ワークスペースを移動する

この記事は、一方の Azure リージョンから他方に Azure Synapse Analytics ワークスペースを移動する方法について説明するステップ バイ ステップ ガイドです。

> [!NOTE]
> この記事の手順では、ワークスペースを実際に移動しません。 この手順では、ソース リージョンからの Azure Synapse Analytics 専用 SQL プールのバックアップと成果物を使用して、新しいリージョンに新しいワークスペースを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- ソース リージョンの Azure Synapse ワークスペースを Azure DevOps または GitHub と統合します。 詳細については、「[Synapse Studio でのソース管理](cicd/source-control.md)」を参照してください。
- スクリプトが実行されるサーバーに [Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-6.3.0&preserve-view=true) および [Azure CLI](/cli/azure/install-azure-cli) モジュールをインストールしておきます。
- すべての依存サービス (Azure Machine Learning、Azure Storage、Azure Private Link ハブなど) がターゲット リージョンに再作成されているか、ターゲット リージョンに移動されている (サービスでリージョンの移動をサポートしている場合) ことを確認します。 
- Azure Storage を別のリージョンに移動します。 詳細については、「[Azure ストレージ アカウントを別のリージョンに移動する](../storage/common/storage-account-move.md)」をご覧ください。
- 専用 SQL プール名と Apache Spark プール名が、ソース リージョンとターゲット リージョンのワークスペースで同じであることを確認します。

## <a name="scenarios-for-a-region-move"></a>リージョンの移動に関するシナリオ

- **新しいコンプライアンス要件**: 新しいコンプライアンス要件の一部として、組織ではデータとサービスを同じリージョンに配置する必要があります。
- **新しい Azure リージョンが利用可能**: このシナリオでは、新しい Azure リージョンが利用可能であり、新たに利用可能になった Azure リージョンにワークスペースやその他の Azure リソースを移動するためのプロジェクトまたはビジネス要件があります。
- **間違ったリージョンの選択**: Azure リソースの作成時に間違ったリージョンが選択されました。

## <a name="steps-to-move-an-azure-synapse-workspace-to-another-region"></a>Azure Synapse ワークスペースを別のリージョンに移動する手順

あるリージョンから別のリージョンへの Azure Synapse ワークスペースの移動は、複数ステップのプロセスです。 手順の概要は次のとおりです。

1. ターゲット リージョンで、新しい Azure Synapse ワークスペースと、ソース リージョンのワークスペースで使用されているのと同じ構成の Spark プールを作成します。
1. 復元ポイントまたは geo バックアップを使用して、専用 SQL プールをターゲット リージョンに復元します。
1. 新しい論理 SQL Server 上に必要なすべてのログインを再作成します。
1. サーバーレス SQL プールおよび Spark プールのデータベースとオブジェクトを作成します。
1. Azure Synapse のロールベースのアクセス制御 (RBAC) の Synapse 成果物発行元ロールに Azure DevOps のサービス プリンシパルを追加します (Azure DevOps リリース パイプラインを使用して成果物をデプロイする場合)。
1. Azure DevOps リリース パイプラインからターゲット リージョンの Azure Synapse ワークスペースに、コード成果物 (SQL スクリプト、ノートブック)、リンクされたサービス、パイプライン、データセット、Spark ジョブ定義トリガー、資格情報をデプロイします。
1. Azure Active Directory (Azure AD) のユーザーまたはグループを Azure Synapse RBAC ロールに追加します。 Azure Storage と Azure Key Vault 上のシステム割り当てマネージド ID (SA-MI) にストレージ BLOB 共同作成者のアクセス権を付与します (マネージド ID を使用して認証を行う場合)。
1. 接続されている既定のストレージ、またはサーバーレス SQL プールを使用してデータをクエリするストレージ アカウントで、必要な Azure AD ユーザーにストレージ BLOB 閲覧者またはストレージ BLOB 共同作成者ロールを付与します。
1. セルフホステッド統合ランタイム (SHIR) を再作成します。
1. ターゲットの Azure Synapse ワークスペースに、必要なすべてのライブラリと jar を手動でアップロードします。
1. すべてのマネージド プライベート エンドポイントを作成します (ワークスペースがマネージド仮想ネットワーク内にデプロイされている場合)。
1. ターゲット リージョンで新しいワークスペースをテストし、ソース リージョンのワークスペースを指している DNS エントリをすべて更新します。
1. ソース ワークスペースにプライベート エンドポイント接続が作成されている場合は、ターゲット リージョン ワークスペースにも作成します。
1. ソース リージョン内のワークスペースは、十分にテストし、すべての接続をターゲット リージョン ワークスペースにルーティングした後で削除できます。
## <a name="prepare"></a>準備
## <a name="step-1-create-an-azure-synapse-workspace-in-a-target-region"></a>手順 1: ターゲット リージョンに Azure Synapse ワークスペースを作成する

ここでは、Azure PowerShell、Azure CLI、Azure portal を使用して、Azure Synapse ワークスペースを作成します。 リソース グループを Azure Data Lake Storage Gen2 アカウントと共に作成します。このアカウントは、PowerShell スクリプトや CLI スクリプトの一部として、ワークスペースの既定のストレージに使用されます。 デプロイ プロセスを自動化する場合は、DevOps リリース パイプラインからこれらの PowerShell または CLI スクリプトを呼び出します。

### <a name="azure-portal"></a>Azure portal
Azure portal からワークスペースを作成するには、「[クイック スタート: Synapse ワークスペースを作成する](quickstart-create-workspace.md)」の手順に従います。

### <a name="azure-powershell"></a>Azure PowerShell 
次のスクリプトでは、New-AzResourceGroup および New-AzSynapseWorkspace コマンドレットを使用して、リソース グループと Azure Synapse ワークスペースを作成します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

```powershell
$storageAccountName= "<YourDefaultStorageAccountName>"
$resourceGroupName="<YourResourceGroupName>"
$regionName="<YourTargetRegionName>"
$containerName="<YourFileSystemName>" # This is the file system name
$workspaceName="<YourTargetRegionWorkspaceName>"

$sourcRegionWSName="<Your source region workspace name>"
$sourceRegionRGName="<YourSourceRegionResourceGroupName>"
$sqlUserName="<SQLUserName>"
$sqlPassword="<SQLStrongPassword>"

$sqlPoolName ="<YourTargetSQLPoolName>" #Both Source and target workspace SQL pool name will be same
$sparkPoolName ="<YourTargetWorkspaceSparkPoolName>"
$sparkVersion="2.4"

New-AzResourceGroup -Name $resourceGroupName -Location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを作成する

```powershell
#If the Storage account is already created, then you can skip this step.
New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location  $regionName `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $true 
```


#### <a name="create-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースを作成する

```powershell
$password = ConvertTo-SecureString $sqlPassword -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ($sqlUserName, $password)

New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds 
```

マネージド仮想ネットワークを使用してワークスペースを作成する場合は、"ManagedVirtualNetwork" パラメーターをスクリプトに追加します。 使用可能なオプションの詳細については、[マネージド仮想ネットワーク構成](/powershell/module/az.synapse/new-azsynapsemanagedvirtualnetworkconfig?view=azps-6.3.0&preserve-view=true)に関するページを参照してください。


```powershell
#Creating a managed virtual network configuration
$config = New-AzSynapseManagedVirtualNetworkConfig -PreventDataExfiltration -AllowedAadTenantIdsForLinking ContosoTenantId 

#Creating an Azure Synapse workspace
New-AzSynapseWorkspace -ResourceGroupName $resourceGroupName `
                        -Name $workspaceName -Location $regionName `
                        -DefaultDataLakeStorageAccountName $storageAccountName `
                        -DefaultDataLakeStorageFilesystem $containerName `
                        -SqlAdministratorLoginCredential $creds `
                              -ManagedVirtualNetwork $config
```


### <a name="azure-cli"></a>Azure CLI

次の Azure CLI スクリプトでは、リソース グループ、Data Lake Storage Gen2 アカウント、ファイル システムを作成します。 その後で、Azure Synapse ワークスペースを作成します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

```azurecli
az group create --name $resourceGroupName --location $regionName
```

#### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを作成する

```azurecli
# Checking if name is not used only then creates it.

$StorageAccountNameAvailable=(az storage account check-name --name $storageAccountName --subscription $subscriptionId | ConvertFrom-Json).nameAvailable

if($StorageAccountNameAvailable)
{
Write-Host "Storage account Name is available to be used...creating storage account"

#Creating an Data Lake Storage Gen2 account
$storgeAccountProvisionStatus=az storage account create `
  --name $storageAccountName `
  --resource-group $resourceGroupName `
  --location $regionName `
  --sku Standard_GRS `
  --kind StorageV2 `
  --enable-hierarchical-namespace $true

($storgeAccountProvisionStatus| ConvertFrom-Json).provisioningState
}
else
{
    Write-Host "Storage account Name is NOT available to be used...use another name --    exiting the script..."
    EXIT
}

#Creating a container in a Data Lake Storage Gen2 account

$key=(az storage account keys list -g $resourceGroupName -n $storageAccountName|ConvertFrom-Json)[0].value

$fileShareStatus=(az storage share create --account-name $storageAccountName --name $containerName --account-key $key)

if(($fileShareStatus|ConvertFrom-Json).created -eq "True")
{
      Write-Host f"Successfully created the fileshare - '$containerName'"
}
```


#### <a name="create-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースを作成する

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $containerName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName
```
マネージド仮想ネットワークを有効にするには、前のスクリプトに `--enable-managed-virtual-network` パラメーターを含めます。 その他のオプションについては、[ワークスペースのマネージド仮想ネットワーク](/cli/azure/synapse/workspace?view=azure-cli-latest&preserve-view=true)に関するページを参照してください。

```azurecli
az synapse workspace create `
  --name $workspaceName `
  --resource-group $resourceGroupName `
  --storage-account $storageAccountName `
  --file-system $FileShareName `
  --sql-admin-login-user $sqlUserName `
  --sql-admin-login-password $sqlPassword `
  --location $regionName `
  --enable-managed-virtual-network true `
  --allowed-tenant-ids "Contoso"
```

## <a name="step-2-create-an-azure-synapse-workspace-firewall-rule"></a>手順 2: Azure Synapse ワークスペースのファイアウォール規則を作成する 
ワークスペースが作成されたら、ワークスペースのファイアウォール規則を追加します。 IP を特定の範囲に制限します。 ファイアウォールは、Azure portal、PowerShell、または CLI を使用して追加できます。

### <a name="azure-portal"></a>Azure portal
次のスクリーンショットに示すように、ファイアウォール オプションを選択し、IP アドレスの範囲を追加します。

:::image type="icon" source="media/how-to-move-workspace-from-one-region-to-another/firewall.png" border="false":::


### <a name="azure-powershell"></a>Azure PowerShell 
ファイアウォール規則を追加するには、開始および終了 IP アドレスを指定して次の PowerShell コマンドを実行します。 ご自身の要件に従って、IP アドレスの範囲を更新します。


```powershell
$WorkspaceWeb = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Web
$WorkspaceDev = (Get-AzSynapseWorkspace -Name $workspaceName -ResourceGroupName $resourceGroup).ConnectivityEndpoints.Dev

# Adding firewall rules
$FirewallParams = @{
  WorkspaceName = $workspaceName
  Name = 'Allow Client IP'
  ResourceGroupName = $resourceGroup
  StartIpAddress = "0.0.0.0"
  EndIpAddress = "255.255.255.255"
}
New-AzSynapseFirewallRule @FirewallParams
```

次のスクリプトを実行して、ワークスペースのマネージド ID の SQL コントロール設定を更新します。

```powershell 
Set-AzSynapseManagedIdentitySqlControlSetting -WorkspaceName $workspaceName -Enabled $true 
```

### <a name="azure-cli"></a>Azure CLI


```azurecli
az synapse workspace firewall-rule create --name allowAll --workspace-name $workspaceName  `
--resource-group $resourceGroupName --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
 
次のスクリプトを実行して、ワークスペースのマネージド ID の SQL コントロール設定を更新します。

```azurecli
az synapse workspace managed-identity grant-sql-access `
--workspace-name $workspaceName --resource-group $resourceGroupName
```


## <a name="step-3-create-an-apache-spark-pool"></a>手順 3: Apache Spark プールを作成する

ソース リージョンのワークスペースで使用されているのと同じ構成の Spark プールを作成します。

### <a name="azure-portal"></a>Azure portal

Azure portal から Spark プールを作成する場合は、「[クイック スタート: Azure portal を使用して新しいサーバーレス Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)」を参照してください。


「[クイック スタート: Synapse Studio を使用してサーバーレス Apache Spark プールを作成する](quickstart-create-apache-spark-pool-studio.md)」の手順に従って、Synapse Studio から Spark プールを作成することもできます。

### <a name="azure-powershell"></a>Azure PowerShell

次のスクリプトでは、2 つのワーカーおよび 1 つのドライバー ノードを含む Spark プールを作成します。 ソース リージョン ワークスペースの Spark プールと一致するように値を更新します。


```powershell
#Creating a Spark pool with 3 nodes (2 worker + 1 driver) and a small cluster size with 4 cores and 32 GB RAM. 
New-AzSynapseSparkPool `
    -WorkspaceName  $workspaceName `
    -Name $sparkPoolName `
    -NodeCount 3 `
    -SparkVersion $sparkVersion `
    -NodeSize Small
```
 
### <a name="azure-cli"></a>Azure CLI

```azurecli
az synapse spark pool create --name $sparkPoolName --workspace-name $workspaceName --resource-group $resourceGroupName `
--spark-version $sparkVersion --node-count 3 --node-size small
```
## <a name="move"></a>詳細ビュー
## <a name="step-4-restore-a-dedicated-sql-pool"></a>手順 4: 専用 SQL プールを復元する 

### <a name="restore-from-geo-redundant-backups"></a>geo 冗長バックアップからの復元

Azure portal と PowerShell を使用して geo バックアップから専用 SQL プールを復元する場合は、「[Azure Synapse Analytics で専用 SQL プールを geo リストアする](sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup.md)」を参照してください。


### <a name="restore-by-using-restore-points-from-the-source-region-workspace-dedicated-sql-pool"></a>ソース リージョン ワークスペースの専用 SQL プールからの復元ポイントを使用した復元

ソース リージョン ワークスペースの専用 SQL プールの復元ポイントを使用して、専用 SQL プールをターゲット リージョン ワークスペースに復元します。 復元ポイントからの復元には、Azure portal、Synapse Studio、または PowerShell を使用できます。 ソース リージョンにアクセスできない場合は、このオプションを使用して復元することはできません。

#### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio では、"*復元ポイント*" を使用して、サブスクリプション内の任意のワークスペースから専用 SQL プールを復元できます。 専用 SQL プールを作成するときに、次のスクリーンショットに示すように、 **[追加の設定]** で **[復元ポイント]** を選択し、ワークスペースを選択します。 ユーザー定義の復元ポイントを作成した場合は、それを使用して SQL プールを復元します。 それ以外の場合は、最新の自動復元ポイントを選択できます。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/restore-sql-pool.png" alt-text="SQL プールの復元":::


#### <a name="azure-powershell"></a>Azure PowerShell

次の PowerShell スクリプトを実行してワークスペースを復元します。 このスクリプトでは、ソース ワークスペースの専用 SQL プールからの最新の復元ポイントを使用して、ターゲット ワークスペースに SQL プールを復元します。 スクリプトを実行する前に、パフォーマンス レベルを DW100c から必要な値に更新します。 

> [!IMPORTANT]
> 専用 SQL プール名は、両方のワークスペースで同じにする必要があります。


```powershell
#Getting the restore points
$restorePoint=Get-AzSynapseSqlPoolRestorePoint -WorkspaceName $sourceRegionWSName -Name $sqlPoolName|Sort-Object  -Property RestorePointCreationDate -Descending `
                                                                                         | SELECT RestorePointCreationDate -ExpandProperty  RestorePointCreationDate -First 1
```
 
 

```powershell
<#
Transform Synapse SQL pool resource ID to SQL database ID because currently the command only accepts the SQL database ID. 
For example: /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Sql/servers/<WorkspaceName>/databases/<DatabaseName>
#>
$pool = Get-AzSynapseSqlPool -ResourceGroupName $sourceRegionRGName -WorkspaceName $sourcRegionWSName -Name $sqlPoolName
$databaseId = $pool.Id `
    -replace "Microsoft.Synapse", "Microsoft.Sql" `
    -replace "workspaces", "servers" `
    -replace "sqlPools", "databases" 
 

$restoredPool = Restore-AzSynapseSqlPool -FromRestorePoint `
                                         -RestorePoint $restorePoint `
                                         -TargetSqlPoolName $sqlPoolName `
                                         -ResourceGroupName $resourceGroupName `
                                         -WorkspaceName $workspaceName `
                                         -ResourceId $databaseId `
                                         -PerformanceLevel DW100c -AsJob


#Tracks the status of the restore 

Get-Job | Where-Object Command -In ("Restore-AzSynapseSqlPool") | `
Select-Object Id,Command,JobStateInfo,PSBeginTime,PSEndTime,PSJobTypeName,Error |Format-Table
```
専用 SQL プールが復元されたら、Azure Synapse のすべての SQL ログインを作成します。 すべてのログインを作成する場合は、[ログインの作成](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true)に関するページの手順に従います。

## <a name="step-5-create-a-serverless-sql-pool-spark-pool-database-and-objects"></a>手順 5: サーバーレス SQL プール、Spark プールのデータベース、オブジェクトを作成する

サーバーレス SQL プールのデータベースおよび Spark プールをバックアップして復元することはできません。 考えられる対処法として、以下を実行できます。

1. ノートブックと SQL スクリプトを作成します。これには、必要なすべての Spark プール、サーバーレス SQL プールのデータベース、テーブル、ロール、およびユーザーとすべてのロールの割り当てを再作成するコードが含まれています。 これらの成果物を Azure DevOps または GitHub にチェックインします。
1. Storage アカウントの名前が変更された場合は、コード成果物が正しいストレージ アカウント名を指していることを確認します。
1. パイプラインを作成します。これにより、特定のシーケンスでこれらのコード成果物が呼び出されます。 これらのパイプラインをターゲット リージョンのワークスペースで実行すると、Spark SQL データベース、サーバーレス SQL プールのデータベース、外部データ ソース、ビュー、ロール、およびユーザーとアクセス許可がターゲット リージョンのワークスペースに作成されます。
1. ソース リージョンのワークスペースを Azure DevOps と統合すると、これらのコード成果物がリポジトリに含められます。 その後、手順 6 で説明しているように、DevOps リリース パイプラインを使用して、これらのコード成果物をターゲット リージョンのワークスペースにデプロイできます。
1. ターゲット リージョンのワークスペースで、これらのパイプラインを手動でトリガーします。

## <a name="step-6-deploy-artifacts-and-pipelines-by-using-cicd"></a>手順 6: CI/CD を使用して成果物とパイプラインをデプロイする 

 Azure Synapse ワークスペースを Azure DevOps または GitHub と統合する方法および成果物をターゲット リージョンのワークスペースにデプロイする方法については、「[Azure Synapse ワークスペースの継続的インテグレーションとデリバリー (CI/CD)](cicd/continuous-integration-delivery.md)」の手順に従ってください。 

ワークスペースが Azure DevOps と統合されると、workspace_publish という名前の分岐が見つかります。 この分岐には、ノートブック、SQL スクリプト、データセット、リンク サービス、パイプライン、トリガー、Spark ジョブ定義などの成果物の定義を含むワークスペース テンプレートが含まれています。

Azure DevOps リポジトリからの次のスクリーンショットには、成果物とその他のコンポーネントのワークスペース テンプレート ファイルが示されています。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/devops-repo-workspace-publish.png" alt-text="workspace-publish を示すスクリーンショット。":::

次のスクリーンショットに示すように、ワークスペース テンプレートを使用して、Azure DevOps リリース パイプラインで成果物とパイプラインをワークスペースにデプロイできます。

:::image type="content" source="media/how-to-move-workspace-from-one-region-to-another/release-pipeline.png" alt-text="synapse-release-pipeline を示すスクリーンショット。":::

ワークスペースが GitHub または Azure DevOps と統合されない場合は、カスタムの PowerShell または Azure CLI スクリプトを手動で再作成または記述して、すべての成果物、パイプライン、リンク サービス、資格情報、トリガー、Spark 定義をターゲット リージョンのワークスペースにデプロイする必要があります。


> [!NOTE]
> このプロセスでは、ソース リージョンのワークスペースで Spark およびサーバーレス SQL プール、オブジェクト、ロールに加えられたすべての変更を含めるために、パイプラインとコード成果物を更新し続ける必要があります。

## <a name="step-7-create-a-shared-integration-runtime"></a>手順 7: 共有された統合ランタイムを作成する

SHIR を作成するには、「[セルフホステッド統合ランタイムを作成して構成する](../data-factory/create-self-hosted-integration-runtime.md)」の手順に従います。

## <a name="step-8-assign-an-azure-role-to-managed-identity"></a>手順 8: マネージド ID に Azure ロールを割り当てる

 アタッチされた既定の Data Lake Storage Gen2 アカウントで、新しいワークスペースのマネージド ID に `Storage Blob Contributor` アクセス権を割り当てます。 また、認証に SA-MI が使用される他のストレージ アカウントでもアクセスを割り当てます。 必要なすべてのストレージ アカウントについて、Azure AD のユーザーとグループに `Storage Blob Contributor` または `Storage Blob Reader` アクセス権を割り当てます。

### <a name="azure-portal"></a>Azure portal
「[ワークスペースのマネージド ID にアクセス許可を付与する](security/how-to-grant-workspace-managed-identity-permissions.md)」の手順に従って、ワークスペースのマネージド ID にストレージ BLOB データ共同作成者ロールを割り当てます。

### <a name="azure-powershell"></a>Azure PowerShell
ワークスペースのマネージド ID にストレージ BLOB データ共同作成者ロールを割り当てます。

```powershell

$workSpaceIdentityObjectID= (Get-AzSynapseWorkspace -ResourceGroupName $resourceGroupName -Name $workspaceName).Identity.PrincipalId 
$scope = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Storage/storageAccounts/$($storageAccountName)"

# Adding Storage Blob Data Contributor to WS Managed Identity on the storage account. This errors out with the message New-AzRoleAssignment : Exception of type 'Microsoft.Rest.Azure.CloudException' was thrown.
# But it creates the required permissions on the storage account.
$roleAssignedforManagedIdentity=New-AzRoleAssignment -ObjectId $workSpaceIdentityObjectID `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope $scope -ErrorAction SilentlyContinue
```


### <a name="azure-cli"></a>Azure CLI

```azurecli
# Getting Role name
$roleName =az role definition list --query "[?contains(roleName, 'Storage Blob Data Contributor')].{roleName:roleName}" --output tsv

#Getting resource id for storage account
$scope= (az storage account show --name $storageAccountName|ConvertFrom-Json).id

#Getting principal ID for WS Managed Identity
$workSpaceIdentityObjectID=(az synapse workspace show --name $workspaceName --resource-group $resourceGroupName|ConvertFrom-Json).Identity.PrincipalId 
                    
# Adding Storage Blob Data Contributor Azure role to SA-MI
az role assignment create --assignee $workSpaceIdentityObjectID `
--role $roleName `
--scope $scope
```

## <a name="step-9-assign-azure-synapse-rbac-roles"></a>手順 9: Azure Synapse RBAC ロールを割り当てる

個別のロールとアクセス許可を使用して、ターゲット ワークスペースへのアクセスを必要とするすべてのユーザーを追加します。 次の PowerShell および CLI スクリプトでは、ターゲット リージョン ワークスペースで Synapse 管理者ロールに Azure AD ユーザーを追加します。 すべての Azure Synapse RBAC ロール名を確認する場合は、「[Azure Synapse RBAC ロール](security/synapse-workspace-synapse-rbac-roles.md)」を参照してください。

### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio から Azure Synapse RBAC の割り当てを追加または削除するには、「[Synapse Studio で Azure Synapse RBAC ロールの割り当てを管理する方法](security/how-to-manage-synapse-rbac-role-assignments.md)」の手順に従います。


### <a name="azure-powershell"></a>Azure PowerShell

次の PowerShell スクリプトでは、Synapse 管理者ロールの割り当てを Azure AD のユーザーまたはグループに追加します。 次のコマンドで -RoleDefinitionName の代わりに -RoleDefinitionId を使用すると、ユーザーをワークスペースに追加できます。

```powershell
# Add the Synapse RBAC assignment. Use the objectId of the Azure AD user or group you want to assign.
New-AzSynapseRoleAssignment `
   -WorkspaceName $workspaceName  `
   -RoleDefinitionName "Synapse Administrator" `
   -ObjectId 1c02d2a6-ed3d-46ec-b578-6f36da5819c6

# Check if user is added to the access control by running this command.
Get-AzSynapseRoleAssignment -WorkspaceName $workspaceName  
```

ソース リージョンのワークスペース内の ObjectId と RoleId を取得するには、Get-AzSynapseRoleAssignment コマンドを実行します。 ターゲット リージョンのワークスペース内の Azure AD のユーザーまたはグループに同じ Azure Synapse RBAC ロールを割り当てます。

パラメーターとして -ObjectId を使用する代わりに -SignInName を使用することもでき、その場合はユーザーのメール アドレスまたはユーザー プリンシパル名を指定します。 使用可能なオプションの詳細については、[Azure Synapse RBAC の PowerShell コマンドレット](/powershell/module/az.synapse/new-azsynapseroleassignment?view=azps-6.3.0&preserve-view=true)に関するページを参照してください。 

### <a name="azure-cli"></a>Azure CLI

```azurecli
#Get the Object Id of the user and assign the required Azure Synapse RBAC permissions to the Azure AD user. You can provide the email address of the user (username@contoso.com) for the --assignee parameter.
az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Administrator" --assignee adasdasdd42-0000-000-xxx-xxxxxxx

az synapse role assignment create `
--workspace-name $workspaceName `
--role "Synapse Contributor" --assignee "user1@contoso.com"

```

使用可能なオプションの詳細については、[Azure Synapse RBAC の CLI](/cli/azure/synapse/role/assignment?view=azure-cli-latest&preserve-view=true) に関するページを参照してください。 

## <a name="step-10-upload-workspace-packages"></a>手順 10: ワークスペース パッケージをアップロードする

必要なすべてのワークスペース パッケージを新しいワークスペースにアップロードします。 ワークスペース パッケージをアップロードするプロセスを自動化する場合は、[Microsoft Azure Synapse Analytics Artifacts クライアント ライブラリ](https://www.nuget.org/packages/Azure.Analytics.Synapse.Artifacts/1.0.0-preview.10)を参照してください。

## <a name="step-11-permissions"></a>手順 11: アクセス許可
    
ターゲット リージョンの Azure Synapse ワークスペースのアクセス制御を設定するには、「[Azure Synapse ワークスペースのアクセス制御を設定する方法](security/how-to-set-up-access-control.md)」の手順に従います。 


## <a name="step-12-create-managed-private-endpoints"></a>手順 12: マネージド プライベート エンドポイントを作成する

ターゲット リージョンのワークスペースでソース リージョンのワークスペースからマネージド プライベート エンドポイントを再作成する場合は、「[データ ソースへのマネージド プライベート エンドポイントを作成する](security/how-to-create-managed-private-endpoints.md)」を参照してください。 

## <a name="discard"></a>破棄
ターゲット リージョンのワークスペースを破棄する場合は、ターゲット リージョンのワークスペースを削除します。 これを行うには、ポータルのダッシュボードでリソース グループを選択し、ワークスペースを選択して、リソース グループ ページの上部にある [削除] を選択します。

## <a name="clean-up"></a>クリーンアップ
変更をコミットし、ワークスペースの移動を完了するには、ターゲット リージョンのワークスペースをテストした後、ソース リージョンのワークスペースを削除します。 これを行うには、ポータルのダッシュボードで、ソース リージョンのワークスペースがあるリソース グループを選択し、ワークスペースを選択して、リソース グループ ページの上部にある [削除] を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse マネージド仮想ネットワーク](security/synapse-workspace-managed-vnet.md)の詳細を確認します。
- [Azure Synapse マネージド プライベート エンドポイント](security/synapse-workspace-managed-private-endpoints.md)の詳細を確認します。
- [制限されたネットワークからのワークスペース リソースへの接続](security/how-to-connect-to-workspace-from-restricted-network.md)方法の詳細を確認します。
