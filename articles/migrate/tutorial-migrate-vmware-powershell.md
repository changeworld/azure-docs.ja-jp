---
title: VMware VM を Azure に移行する (エージェントレス) - PowerShell
description: Azure Migrate で PowerShell を使用して VMware VM のエージェントレス移行を実行する方法について説明します。
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.openlocfilehash: ce712736c25c0757f5b654e4442a4f08acdf15e2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029789"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>VMware VM を Azure に移行する (エージェントレス) - PowerShell

この記事では、Azure PowerShell を使用して、検出された VMware VM をエージェントレスの方法で移行する方法について説明します ([[Azure Migrate: Server Migration]](migrate-services-overview.md#azure-migrate-server-migration-tool) 内)。 

以下の方法について説明します。

> [!div class="checklist"]
> * Azure Migrate プロジェクト内の検出された VMware VM を取得します。
> * VM のレプリケートを開始します。
> * VM をレプリケートするためのプロパティを更新します。
> * レプリケーションを監視します。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * 完全な VM 移行を実行します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. [検出のチュートリアルを完了](tutorial-discover-vmware.md)して、移行のために Azure と VMware を準備します。
2. 2 番目のチュートリアルを完了して、Azure に移行する前に [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)しておくことをお勧めします。
3. Azure PowerShell `Az` モジュールがあります。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)を参照してください。

## <a name="install-azure-migrate-powershell-module"></a>Azure Migrate PowerShell モジュールをインストールする

Azure Migrate PowerShell モジュールはプレビューで利用できます。 次のコマンドを使用して、PowerShell モジュールをインストールする必要があります。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにサインインする

`Connect-AzAccount` コマンドレットを使用してお使いの Azure サブスクリプションにサインインします。

```azurepowershell
Connect-AzAccount
```

Azure サブスクリプションを選択します。 `Get-AzSubscription` コマンドレットを使って、アクセスできる Azure サブスクリプションの一覧を取得します。 `Set-AzContext` コマンドレットを使用して、使用する Azure Migrate プロジェクトを含む Azure サブスクリプションを選択します。

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Azure Migrate プロジェクトを取得する

Azure Migrate のプロジェクトは、評価または移行しようとしている環境から収集された検出、評価、移行のメタデータを格納するために使用されます。
プロジェクト内で、検出された資産を追跡し、評価を調整して、移行を実行することができます。

前提条件の一部として、Azure Migrate プロジェクトを既に作成しているでしょう。 `Get-AzMigrateProject` コマンドレットを使用して、Azure Migrate プロジェクトの詳細を取得します。 Azure Migrate プロジェクトの名前 (`Name`) と Azure Migrate プロジェクトのリソース グループの名前 (`ResourceGroupName`) を指定する必要があります。

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Azure Migrate プロジェクト内の検出された VM を取得する

Azure Migrate には軽量の [Azure Migrate アプライアンス](migrate-appliance-architecture.md)が使用されます。 前提条件の一部として、Azure Migrate アプライアンスを VMware VM としてデプロイしたでしょう。

Azure Migrate プロジェクト内の特定の VMware VM を取得するには、Azure Migrate プロジェクトの名前 (`ProjectName`)、Azure Migrate プロジェクトのリソース グループ (`ResourceGroupName`)、VM 名 (`DisplayName`) を指定します。 

> [!NOTE]
> **VM 名 (`DisplayName`) パラメーター値では、大文字と小文字が区別されます**。

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
このチュートリアルでは、この VM を移行します。

`ProjectName` および `ResourceGroupName` パラメーターを使用して、Azure Migrate プロジェクト内のすべての VMware VM を取得することもできます。

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Azure Migrate プロジェクト内に複数のアプライアンスがある場合は、`ProjectName`、`ResourceGroupName`、`ApplianceName` の各パラメーターを使用することで、特定の Azure Migrate アプライアンスを使用して検出されたすべての VM を取得できます。 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>レプリケーション インフラストラクチャを初期化する

[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) での VM の移行には、複数の Azure リソースを利用します。 Server Migration によって、プロジェクトと同じリソース グループに次のリソースがプロビジョニングされます。

- **サービス バス**: Server Migration では、サービス バスを使用して、レプリケーション オーケストレーション メッセージをアプライアンスに送信します。
- **ゲートウェイ ストレージ アカウント**: Server Migration では、ゲートウェイ ストレージ アカウントを使用して、レプリケートされる VM に関する状態情報を格納します。
- **ログ ストレージ アカウント**: Azure Migrate アプライアンスでは、VM のレプリケーション ログをログ ストレージ アカウントにアップロードします。 Azure Migrate により、レプリケーション情報がレプリカ マネージド ディスクに適用されます。
- **キー コンテナー**: Azure Migrate アプライアンスでは、キー コンテナーを使用して、サービス バスの接続文字列と、レプリケーションで使用されるストレージ アカウントのアクセス キーを管理します。

Azure Migrate プロジェクト内の最初の VM をレプリケートする前に、次のスクリプトを実行してレプリケーション インフラストラクチャをプロビジョニングします。 このスクリプトを使用して、VMware VM の移行を開始できるように、前述のリソースをプロビジョニングして構成します。

> [!NOTE]
> 1 つの Azure Migrate プロジェクトによって、1 つの Azure リージョンへの移行のみがサポートされます。 このスクリプトを実行すると、VMware VM の移行先となるターゲット リージョンを変更することはできなくなります。
> Azure Migrate プロジェクト内に新しいアプライアンスを構成する場合は、`Initialize-AzMigrateReplicationInfrastructure` スクリプトを実行する必要があります。 

この記事では、VM を `Central US` リージョンに移行できるようにレプリケーション インフラストラクチャを初期化します。 GitHub リポジトリから[ファイルをダウンロードする](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles)か、次のスニペットを使用して実行することができます。 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>VM をレプリケートする

レプリケーションインフラストラクチャの検出と初期化が完了したら、Azure への VMware VM のレプリケーションを開始できます。 最大 300 件のレプリケーションを同時に実行できます。

レプリケーションのプロパティは、次のように指定できます。

- **ターゲット サブスクリプションとリソース グループ** - `TargetResourceGroupId` パラメーターを使用してリソース グループ ID を指定することにより、VM の移行先となるサブスクリプションとリソース グループを指定します。 
- **ターゲット仮想ネットワークとサブネット** - `TargetNetworkId` および `TargetSubnetName` パラメーターを使用して、VM の移行先となる Azure 仮想ネットワークの ID とサブネットの名前を指定します。 
- **ターゲット VM 名** - `TargetVMName` パラメーターを使用して、作成する Azure VM の名前を指定します。
- **ターゲット VM サイズ** - `TargetVMSize` パラメーターを使用して、レプリケートする VM に使用する Azure VM のサイズを指定します。 たとえば、VM を Azure の D2_v2 VM に移行するには、`TargetVMSize` の値を "Standard_D2_v2" と指定します。  
- **ライセンス** - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンの Azure ハイブリッド特典を使用するには、`LicenseType` パラメーターの値として "WindowsServer" を指定します。 それ以外の場合は、`LicenseType` パラメーターの値を "NoLicenseType" と指定します。
- **OS ディスク** - オペレーティング システムのブートローダーとインストーラーがあるディスクの一意識別子を指定します。 使用するディスク ID は、`Get-AzMigrateServer` コマンドレットを使用して取得したディスクの一意識別子 (UUID) プロパティです。
- **ディスクの種類** - `DiskType` パラメーターの値を次のように指定します。
    - Premium マネージド ディスクを使用するには、`DiskType` パラメーターの値として "Premium_LRS" を指定します。 
    - Standard SSD ディスクを使用するには、`DiskType` パラメーターの値として "StandardSSD_LRS" を指定します。 
    - Standard HDD ディスクを使用するには、`DiskType` パラメーターの値として "Standard_LRS" を指定します。 
- **インフラストラクチャの冗長性** - 次のようにインフラストラクチャ冗長オプションを指定します。 
    - 可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。 可用性ゾーンを使用するには、`TargetAvailabilityZone` パラメーターの可用性ゾーンの値を指定します。
    - 可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択したターゲット リソース グループに 1 つ以上の可用性セットが必要です。 可用性セットを使用するには、`TargetAvailabilitySet` パラメーターの可用性セット ID を指定します。 

### <a name="replicate-vms-with-all-disks"></a>すべてのディスクを使用して VM をレプリケートする
このチュートリアルでは、検出された VM のすべてのディスクをレプリケートし、Azure での VM の新しい名前を指定します。 検出されたサーバーの最初のディスクを OS ディスクとして指定し、すべてのディスクを Standard HDD として移行します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>選択したディスクを使用して VM をレプリケートする
`New-AzMigrateDiskMapping` コマンドレットを使用して検出された VM のディスクを、`New-AzMigrateServerReplication` コマンドレットで `DiskToInclude` パラメーターに入力として指定することで、選択的にレプリケートすることもできます。 `New-AzMigrateDiskMapping` コマンドレットを使用して、レプリケートする個々のディスクに対して異なるターゲット ディスクの種類を指定することもできます。 

`New-AzMigrateDiskMapping` コマンドレットの次のパラメーターの値を指定します。

- **DiskId** - 移行するディスクの一意識別子を指定します。 使用するディスク ID は、`Get-AzMigrateServer` コマンドレットを使用して取得したディスクの一意識別子 (UUID) プロパティです。  
- **IsOSDisk** - 移行するディスクが VM の OS ディスクである場合は "true" を指定し、それ以外の場合は "false" を指定します。
- **DiskType** - Azure で使用するディスクの種類を指定します。 

次の例では、検出された VM の 2 つのディスクのみをレプリケートします。 OS ディスクを指定し、レプリケートするディスクごとに異なるディスクの種類を使用します。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>レプリケーションを監視します 

レプリケーションは、次のように行われます。

- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

`Get-AzMigrateServerReplication` コマンドレットを使用してレプリケーションの状態を追跡します。 

> [!NOTE]
> 検出された VM の ID とレプリケートする VM の ID は、2 つの異なる一意識別子です。 これらの両方の識別子を使用して、レプリケートするサーバーの詳細を取得できます。  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>検出された VM の識別子を使用してレプリケーションを監視する
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>レプリケートする VM の識別子を使用してレプリケーションを監視する

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

出力の "移行状態" と "移行状態の説明" を示すプロパティを追跡できます。 
- 初期レプリケーションの場合、移行状態と移行状態の説明のプロパティの値はそれぞれ "InitialSeedingInProgress" \(初期シード処理進行中\) と "Initial replication" \(初期レプリケーション\) になります。 
- 差分レプリケーションの間、移行状態および移行状態の説明プロパティの値は、それぞれ "Replicating" \(レプリケーション中\) と "Ready to migrate" \(移行の準備完了\) になります。
- 移行が完了すると、移行状態および移行状態の説明プロパティの値はそれぞれ "Migration succeeded" \(移行に成功しました\) と "Migrated" \(移行しました\) になります。

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

レプリケーションの進行状況の詳細を確認するには、次のコマンドレットを実行します。

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
出力の "初期シード処理の割合" を示すプロパティを使用して、初期レプリケーションの進行状況を追跡できます。

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

レプリケーションは、次のように行われます。

- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

## <a name="retrieve-the-status-of-a-job"></a>ジョブの状態を取得する

`Get-AzMigrateJob` コマンドレットを使用して、ジョブの状態を監視できます。 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>レプリケートする VM のプロパティを更新する

[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) を使用すると、レプリケートする VM の名前、サイズ、リソース グループ、NIC 構成などのターゲット プロパティを変更できます。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
VM に関する次のプロパティを更新できます。

- **VM 名** - `TargetVMName` パラメーターを使用して、作成される Azure VM の名前を指定します。
- **VM サイズ** - `TargetVMSize` パラメーターを使用して、レプリケートする VM に使用する Azure VM のサイズを指定します。 たとえば、VM を Azure の D2_v2 VM に移行するには、`TargetVMSize` の値を "Standard_D2_v2" と指定します。  
- **仮想ネットワーク** - `TargetNetworkId` パラメーターを使用して、VM の移行先となる Azure 仮想ネットワークの ID を指定します。 
- **リソース グループ** - `TargetResourceGroupId` パラメーターを使用してリソース グループ ID を指定することにより、VM の移行先となるリソース グループの ID を指定します。
- **ネットワーク インターフェイス** - `New-AzMigrateNicMapping` コマンドレットを使用して NIC の構成を指定できます。 その後、オブジェクトに `Set-AzMigrateServerReplication` コマンドレットの `NicToUpdate` パラメーターへの入力を渡します。 

    - **IP 割り当ての変更** - NIC の静的 IP を指定するには、`TargetNicIP` パラメーターを使用して、VM の静的 IP として使用する IPv4 アドレスを指定します。 NIC の IP を動的に割り当てるには、`TargetNicIP` パラメーターの値として "auto" を指定します。
    - `TargetNicSelectionType` パラメーターには、"Primary"、"Secondary"、または "DoNotCreate" を使用して、移行された VM 上で NIC をプライマリにするか、セカンダリにするか、または作成されないようにするかを指定します。 VM のプライマリ NIC として指定できる NIC は 1 つだけです。 
    - NIC をプライマリにするには、移行された VM 上でセカンダリにする、または作成されないようにする他の NIC も指定する必要があります。  
    - NIC のサブネットを変更するには、`TargetNicSubnet` パラメーターを使用してサブネットの名前を指定します。

 - **可用性ゾーン** - 可用性ゾーンを使用するには、`TargetAvailabilityZone` パラメーターの可用性ゾーン値を指定します。
 - **可用性セット** - 可用性セットを使用するには、`TargetAvailabilitySet` パラメーターの可用性セット ID を指定します。

次の例では、最初の NIC をプライマリにして、それに静的 IP を割り当てることによって、NIC 構成を更新します。 移行対象の 2 番目の NIC を破棄し、ターゲット VM の名前とサイズを更新します。 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

また、Azure Migrate プロジェクト内のレプリケートするすべてのサーバーを一覧表示してから、レプリケートする VM の識別子を使用して VM のプロパティを更新することもできます。

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>テスト移行を実行する

差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にテスト移行を実行することを強くお勧めします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。 テスト移行を行ってもオンプレミスのマシンには影響がなく稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

`TestNetworkID` パラメーターを使用して仮想ネットワークの ID を指定することによって、テストに使用する Azure 仮想ネットワークを選択します。

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

テストが完了したら、`Start-AzMigrateTestMigrationCleanup` コマンドレットを使用してテスト移行をクリーンアップします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、次のコマンドレットを使用して、レプリケートするサーバーを移行できます。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

移行元サーバーの電源がオフにならないようにするには、`TurnOffSourceServer` パラメーターを使用しないでください。

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、次のコマンドレットを使用して、オンプレミスのマシンのレプリケーションを停止し、VM のレプリケーション状態情報をクリーンアップします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. マシンの OS が Linux の場合は、移行されたマシンに [Linux](../virtual-machines/extensions/agent-linux.md) エージェントをインストールします。 Windows VM の VM エージェントは、移行中に自動的にインストールされます。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細については、こちらを参照してください](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細については、こちらを参照してください](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](../security-center/security-center-just-in-time.md)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) をデプロイして、リソースの使用率と消費量を監視します。