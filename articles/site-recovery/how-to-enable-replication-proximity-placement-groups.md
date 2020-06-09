---
title: 近接配置グループで実行されている Azure VM をレプリケートする
description: Azure Site Recovery を使用して、近接配置グループで実行されている Azure VM をレプリケートする方法について説明します。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847635"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>近接配置グループで実行されている Azure 仮想マシンを別のリージョンにレプリケートする

この記事では、近接配置グループで実行されている仮想マシンをセカンダリ リージョンにレプリケート、フェールオーバー、およびフェールバックする方法について説明します。

[近接配置グループ](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal)は Azure 仮想マシンの論理的なグループ化機能であり、これを使用してアプリケーションに関連する VM 間のネットワーク待機時間を減らすことができます。 同じ近接配置グループ内にデプロイされている VM は、可能な限り物理的に接近させて配置されています。 近接配置グループが特に役立つのは、待機時間の影響を受けやすいワークロードの要件に対応する場合です。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>近接配置グループでのディザスター リカバリー

一般的なシナリオでは、アプリケーションの各層の間でネットワーク待機時間が発生しないようにするために、複数の仮想マシンを 1 つの近接配置グループで実行することがあります。 これでアプリケーションのネットワーク待機時間は最適化できますが、Site Recovery を使用してリージョン レベルの障害から当該アプリケーションを保護することが望まれます。 Site Recovery では、あるリージョンから別の Azure リージョンにデータがレプリケートされ、フェールオーバーの際にディザスター リカバリー リージョンでマシンが稼働されます。

## <a name="considerations"></a>考慮事項

- ベスト エフォートは、仮想マシンを近接配置グループにフェールオーバー/フェールバックすることです。 ただし、フェールオーバー/フェールバックの際に近接配置グループ内で VM を稼働できない場合でも、フェールオーバー/フェールバックは行われ、近接配置グループの外部に仮想マシンが作成されます。
-  近接配置グループに可用性セットが固定されていて、かつフェールオーバー/フェールバックの際に可用性セット内の VM の間に割り当て制約が適用される場合は、可用性セットと近接配置グループの外部に仮想マシンが作成されます。
-  アンマネージド ディスクでは、近接配置グループでの Site Recovery がサポートされません。

## <a name="prerequisites"></a>前提条件

1. Azure PowerShell Az モジュールがあることを確認します。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](https://docs.microsoft.com/powershell/azure/install-az-ps)に関するページをご覧ください。

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>近接配置グループ内の仮想マシンに対する Site Recovery の設定

### <a name="azure-to-azure"></a>Azure から Azure

1. お使いのアカウントに[サインイン](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription)し、サブスクリプションを設定します。
2. レプリケートする予定の仮想マシンの詳細を取得します ([こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated)を参照)。
3. Recovery Services のコンテナーを[作成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault)し、コンテナーのコンテキストを[設定](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context)します。
4. 仮想マシンのレプリケートを開始するようにコンテナーを準備します。 その際に、プライマリ リージョンと復旧リージョンの両方に対して[サービス ファブリック オブジェクト](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)を作成します。
5. プライマリ ファブリックと復旧ファブリックの両方に対して Site Recovery 保護コンテナーを[作成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric)します。
6. レプリケーション ポリシーを[作成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy)します。
7. プライマリ保護コンテナーと復旧保護コンテナーの間の保護コンテナー マッピングを作成し (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container))、フェールバック用の保護コンテナー マッピングを作成します ([こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)を参照)。
8. キャッシュ ストレージ アカウントを作成します (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account))。
9. 必要なネットワーク マッピングを作成します ([こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings)を参照)。
10. マネージド ディスクを使用する Azure 仮想マシンをレプリケートするには、次の PowerShell コマンドレットを使用します。 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
レプリケーションの開始操作が成功すると、仮想マシンのデータが復旧リージョンにレプリケートされます。

レプリケーション プロセスは、最初に、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、初期レプリケーション フェーズと呼ばれます。

初期レプリケーションが完了すると、レプリケーションは差分同期フェーズに移行します。 この時点では、仮想マシンは保護されていて、それに対してテスト フェールオーバーの操作を実行できます。 初期レプリケーションの完了後、仮想マシンを表すレプリケートされた項目のレプリケーション状態は、保護された状態に移行します。

それに対応するレプリケーションの保護された項目の詳細を取得することによって、仮想マシンのレプリケーション状態とレプリケーション正常性を監視します。 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. テスト フェールオーバーを実行し、検証して、クリーンアップします (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover))。
12. フェールオーバーを実行します (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure))。
13. 次の PowerShell コマンドレットを使用して、ソース リージョンを再保護します。

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. レプリケーションを無効にします (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication))。

### <a name="vmware-to-azure"></a>VMware から Azure

1. Azure へのディザスター リカバリーのために[オンプレミス VMware サーバーを準備](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises)してください。
2. お使いのアカウントにサインインし、[こちら](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure)で指定されているようにサブスクリプションを設定します。
3. Recovery Services のコンテナーを[設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault)し、[コンテナーのコンテキストを設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context)します。
4. コンテナーの登録を[検証](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration)します。
5. レプリケーション ポリシーを[作成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy)します。
6. vCenter Server を[追加](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms)し、仮想マシンを検出し、レプリケーション用にストレージ アカウントを[作成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication)します。
7. 次の PowerShell コマンドレットを使用して、VMware 仮想マシンをレプリケートします。詳細は以下をご覧ください。

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Get-ASRReplicationProtectedItem コマンドレットを使って、仮想マシンのレプリケーションの状態と正常性を確認することができます。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. フェールオーバーの設定を構成します (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings))。
10. テスト フェールオーバーを[実行](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover)します。 
11. Azure にフェールオーバーします (手順は[こちら](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure))。

### <a name="hyper-v-to-azure"></a>Hyper-V から Azure

1. Azure へのディザスター リカバリーのために[オンプレミス Hyper-V サーバーを準備](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)します。
2. Azure に[サインイン](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account)します。
3. コンテナーを[設定](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault)し、Recovery Services コンテナーのコンテキストを[設定](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context)します。
4. Hyper-V サイトを[作成](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site)します。
5. プロバイダーとエージェントを[インストール](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent)します。
6. レプリケーション ポリシーを[作成](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy)します。
7. 次の手順に従ってレプリケーションを有効にします。 
    
    a. 次のように、保護する VM に対応する保護可能な項目を取得します。

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. VM を保護します。 保護対象の VM に複数のディスクが接続されている場合は、OSDiskName パラメーターを使用してオペレーティング システム ディスクを指定します。
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. 初回レプリケーション後、VM が保護された状態になるまで待ちます。 これには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 保護された状態になると、ジョブの State および StateDescription は次のように更新されます。 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. 復旧のプロパティ (VM ロール サイズなど)、およびフェールオーバー後に VM NIC を接続する Azure ネットワークを更新します。

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. テスト [フェールオーバー](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover)を実行します。


## <a name="next-steps"></a>次のステップ

VMware の再保護と Azure へのフェールバックを実行する場合は、[こちら](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback)で説明されている手順に従います。

Hyper-V から Azure へのフェールオーバーを実行する場合は[こちら](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)で説明されている手順に従い、フェールバックを実行する場合は[こちら](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback)で説明されている手順に従います。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。
