---
title: 近接配置グループで実行されている Azure VM をレプリケートする
description: Azure Site Recovery を使用して、近接配置グループで実行されている Azure VM をレプリケートする方法について説明します。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: c721014e2f0a61e46ad599bccdc26388aa706a3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444954"
---
# <a name="replicate-virtual-machines-running-in-a-proximity-placement-group-to-another-region"></a>近接配置グループで実行されている仮想マシンを別のリージョンにレプリケートする

この記事では、近接配置グループで実行されている Azure 仮想マシン (VM) をセカンダリ リージョンにレプリケート、フェールオーバー、およびフェールバックする方法について説明します。

[近接配置グループ](../virtual-machines/windows/proximity-placement-groups-portal.md)は、Azure 仮想マシンの論理的なグループ化機能です。 これらを使用すると、アプリケーションに関連付けられている VM 間のネットワーク待機時間を減らすことができます。 

同じ近接配置グループ内にデプロイされている VM は、可能な限り物理的に接近して配置されています。 近接配置グループが役立つのは、待機時間の影響を受けやすいワークロードの要件に対応する場合です。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>近接配置グループによるディザスター リカバリー

一般的なシナリオでは、アプリケーションの階層間でネットワーク待機時間が発生しないようにするために、複数の仮想マシンを 1 つの近接配置グループで実行することがあります。 この方法では、アプリケーションに最適なネットワーク待機時間を実現できますが、Azure Site Recovery を使用すると、リージョン レベルの障害からこれらのアプリケーションを保護することができます。 

Site Recovery を使用すると、Azure リージョンから別のリージョンにデータをレプリケートできます。 フェールオーバー イベント発生時には、ディザスター リカバリー (DR) リージョンのマシンが起動されます。

## <a name="considerations"></a>考慮事項

- ベスト エフォートは、仮想マシンを近接配置グループにフェールオーバーおよびフェールバックすることです。 容量割り当ての制約があるために近接配置グループ内の VM を起動できない場合、フェールオーバーとフェールバックは実行されますが、VM は近接配置グループの外に作成されます。
- 近接配置グループに可用性セットが固定されていて、かつフェールオーバーまたはフェールバックの際に可用性セット内の VM に割り当て制約がある場合は、可用性セットと近接配置グループの両方の外部に VM が作成されます。
- アンマネージド ディスクでは、近接配置グループでの Site Recovery がサポートされません。

> [!NOTE]
> Azure Site Recovery は、Hyper-V から Azure への移動シナリオでマネージド ディスクからのフェールバックをサポートしていません。 Azure の近接配置グループから Hyper-V へのフェールバックはサポートされていません。

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-the-azure-portal"></a>Azure ポータル経由で近接配置グループの VM に対するディザスター リカバリーを設定する

### <a name="azure-to-azure"></a>Azure から Azure

VM ディザスター リカバリー ページで仮想マシンのレプリケーションの有効にすることを選択できます。 または、事前に作成されたコンテナーに移動し、Site Recovery セクションに移動して、レプリケーションを有効にすることで、レプリケーションを有効にすることもできます。 ここでは、両方の方法を使用して近接配置グループ内に Site Recovery VM を設定する方法を見てみましょう。

サービスとしてのインフラストラクチャ (IaaS) VM の DR ページを使用してレプリケーションを有効にしながら、DR リージョンで近接配置グループを選択するには、次の手順を実行します。

1. 仮想マシンに移動します。 左側のウィンドウの **[操作]** で **[ディザスター リカバリー]** を選択します。
2. **[基本]** タブで、VM のレプリケート先となる DR リージョンを選択します。 **[詳細設定]** へ移動します。
3. お使いの VM の近接配置グループと、DR リージョンの近接配置グループを選択するオプションが表示されます。 Site Recovery には、新しい近接配置グループを使用するオプションも用意されており、この既定のオプションの使用を選択すると自動的に作成されます。 
 
   必要な近接配置グループを選択します。 次に、 **[レプリケーションを確認して開始する]** を選択します。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="レプリケーションを有効にするための詳細設定を示す画面のスクリーンショット。":::

コンテナー ページを使用してレプリケーションを有効にしながら、DR リージョンで近接配置グループを選択するには、次の手順を実行します。

1. Recovery Services コンテナーに移動し、 **[Site Recovery]** タブに移動します。
2. **[+ Site Recovery を有効にする]** を選択します。 次に、 **[Azure 仮想マシン]** で **[1: Enable Replication]\(1: レプリケーションを有効にする\)** を選択します (Azure VM をレプリケートするため)。
3. **[ソース]** タブの必須フィールドに入力し、 **[次へ]** を選択します。
4. レプリケーションを有効にする VM の一覧を **[仮想マシン]** タブで選択して、 **[次へ]** を選択します。
5. DR リージョン内の近接配置グループを選択するオプションが表示されます。 Site Recovery には、新しい近接配置グループを使用するオプションも用意されており、この既定のオプションの使用を選択すると自動的に作成されます。 

   必要な近接配置グループを選択してから、レプリケーションの有効化に進みます。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="ターゲット設定をカスタマイズするための選択内容を示すスクリーンショット":::

VM のレプリケーションを有効にした後で、DR リージョンの近接配置グループの選択を簡単に更新できます。

1. 仮想マシンに移動します。 左側のウィンドウの **[操作]** で **[ディザスター リカバリー]** を選択します。
2. **[コンピューティング]** ペインに移動し、 **[編集]** を選択します。
3. ターゲットの近接配置グループを含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる近接配置グループを選択し、 **[保存]** を選択します。

### <a name="vmware-to-azure"></a>VMware から Azure

VM のレプリケーションを有効にした後で、ターゲット VM の近接配置グループを設定できます。 要件に応じて、ターゲット リージョンに近接配置グループを個別に作成したことを確認してください。 

VM のレプリケーションを有効にした後で、DR リージョンの近接配置グループの選択を簡単に更新できます。

1. コンテナーから仮想マシンを選択します。 左側のウィンドウの **[操作]** で **[ディザスター リカバリー]** を選択します。
2. **[コンピューティングとネットワーク]** ウィンドウに移動し、 **[編集]** を選択します。
3. ターゲットの近接配置グループを含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる近接配置グループを選択し、 **[保存]** を選択します。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="VMware から Azure へのコンピューティングとネットワークの選択を示すスクリーンショット":::

### <a name="hyper-v-to-azure"></a>Hyper-V から Azure

VM のレプリケーションを有効にした後で、ターゲット VM の近接配置グループを設定できます。 要件に応じて、ターゲット リージョンに近接配置グループを個別に作成したことを確認してください。 

VM のレプリケーションを有効にした後で、DR リージョンの近接配置グループの選択を簡単に更新できます。

1. コンテナーから仮想マシンを選択します。 左側のウィンドウの **[操作]** で **[ディザスター リカバリー]** を選択します。
2. **[コンピューティングとネットワーク]** ウィンドウに移動し、 **[編集]** を選択します。
3. ターゲットの近接配置グループを含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる近接配置グループを選択し、 **[保存]** を選択します。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Hyper-V から Azure へのコンピューティングとネットワークの選択を示すスクリーンショット":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>PowerShell 経由で近接配置グループの VM に対するディザスター リカバリーを設定する

### <a name="prerequisites"></a>前提条件 

- Azure PowerShell Az モジュールがあることを確認します。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)に関するページを参照してください。
- Azure PowerShell Az のバージョンは 4.1.0 以上である必要があります。 現在のバージョンを確認するには、次のコマンドを使います。

    ```
    Get-InstalledModule -Name Az
    ```

> [!NOTE]
> ターゲットの近接配置グループの一意の ID が手元にあることを確認します。 使用するコマンドは、[新しい近接配置グループを作成するか](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group)、[既存の近接配置グループを使用するか](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)によって異なります。

### <a name="azure-to-azure"></a>Azure から Azure

1. [お使いのアカウントにサインインし、サブスクリプションを設定します。](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)
2. [レプリケートする予定の仮想マシンの詳細を取得します。](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)
3. [Recovery Services のコンテナーを作成し](./azure-to-azure-powershell.md#create-a-recovery-services-vault)、[コンテナーのコンテキストを設定します。](./azure-to-azure-powershell.md#set-the-vault-context)
4. 仮想マシンのレプリケートを開始するようにコンテナーを準備します。 この手順では、プライマリと復旧リージョンの両方に対して [Azure サービス ファブリック オブジェクト](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)を作成します。
5. プライマリと復旧ファブリックの両方に対して [Site Recovery 保護コンテナーを作成します](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric)。
6. [レプリケーション ポリシーを作成する](./azure-to-azure-powershell.md#create-a-replication-policy)。
7. [プライマリと復旧保護コンテナーの間の保護コンテナー マッピングを作成し](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container)、[フェールバック用の保護コンテナー マッピングを作成します](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)。
8. [キャッシュ ストレージ アカウントを作成します](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account)。
9. [必要なネットワークのマッピングを作成します](./azure-to-azure-powershell.md#create-network-mappings)。
10. 次の PowerShell コマンドレットを使用して、マネージド ディスクと Azure 仮想マシンをレプリケートします。

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    複数のデータ ディスクのレプリケーションを有効にする場合は、次の PowerShell コマンドレットを使用します。

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
        $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    近接配置グループによるゾーン間レプリケーションを有効にすると、レプリケーションを開始するコマンドが次のように PowerShell コマンドレットと交換されます。

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    レプリケーションを開始する操作が成功すると、仮想マシンのデータが復旧リージョンにレプリケートされます。

    レプリケーション プロセスは、最初に、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、*初期レプリケーション* フェーズと呼ばれます。

    初期レプリケーションが完了すると、レプリケーションは *差分同期* フェーズに移行します。 この時点では、仮想マシンは保護されていて、それに対してテスト フェールオーバーの操作を実行できます。 初期レプリケーションの完了後、仮想マシンを表すレプリケートされた項目のレプリケーション状態は、保護された状態に移行します。

    それに対応するレプリケーションの保護された項目の詳細を取得することによって、仮想マシンのレプリケーション状態とレプリケーション正常性を監視します。

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

11. [テスト フェールオーバーを実行、検証、クリーンアップします](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover)。
12. [仮想マシンをフェールオーバーします](./azure-to-azure-powershell.md#fail-over-to-azure)。
13. 次の PowerShell コマンドレットを使用して、ソース リージョンへの再保護とフェールバックを実行します。

    ```azurepowershell
    #Create a cache storage account for replication logs in the primary region.
    $WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


    #Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
    Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
    ```

14. [レプリケーションを無効にする](./azure-to-azure-powershell.md#disable-replication)。

### <a name="vmware-to-azure"></a>VMware から Azure

1. Azure へのディザスター リカバリーのために[オンプレミス VMware サーバーを準備します](./vmware-azure-tutorial-prepare-on-premises.md)。
2. [お使いのアカウントにサインインし、サブスクリプションを設定します](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)。
3. [Recovery Services コンテナーを設定し](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)、[コンテナーのコンテキストを設定します](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)。
4. [コンテナーの登録を検証します](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)。
5. [レプリケーション ポリシーを作成する](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy)。
6. [vCenter Server を追加し、仮想マシンを検出し](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms)、[レプリケーション用にストレージ アカウントを作成します](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication)。
7. 次の PowerShell コマンドレットを使用して、VMware 仮想マシンをレプリケートし、詳細を確認します。

   ```azurepowershell
   #Get the target resource group to be used.
   $ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

   #Get the target virtual network to be used.
   $RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

   #Get the protection container mapping for the replication policy named ReplicationPolicy.
   $PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

   #Get the protectable item that corresponds to the virtual machine CentOSVM1.
   $VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

   # Enable replication for virtual machine CentOSVM1 by using the Az.RecoveryServices module 2.0.0 onward to replicate to managed disks.
   # The name specified for the replicated item needs to be unique within the protection container. Use a random GUID to ensure uniqueness.
   $Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
   ```

8. `Get-ASRReplicationProtectedItem` コマンドレットを使用して仮想マシンのレプリケーション状態とレプリケーションの正常性を確認します。

   ```azurepowershell
   Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
   ```

9. [フェールオーバー設定を構成します](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)。
10. [テスト フェールオーバーを実行します](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)。
11. [Azure にフェールオーバーします](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure)。

### <a name="hyper-v-to-azure"></a>Hyper-V から Azure

1. [Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備します](./hyper-v-prepare-on-premises-tutorial.md)。
2. [Azure にサインイン](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)します。
3. [コンテナーを設定し](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)、[Recovery Services コンテナーのコンテキストを設定します](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)。
4. [Hyper-V サイトを作成します](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site)。
5. [プロバイダーとエージェントをインストールします](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)。
6. [レプリケーション ポリシーを作成する](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy)。
7. 次の手順を使用して、レプリケーションを有効にします。 
    
   a. 保護する VM に対応する保護可能な項目を取得します。

      ```azurepowershell
      $VMFriendlyName = "Fabrikam-app"          #Name of the VM
      $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
      ```
   b. VM を保護します。 保護対象の VM に複数のディスクが接続されている場合は、`OSDiskName` パラメーターを使用してオペレーティング システム ディスクを指定します。
    
      ```azurepowershell
      $OSType = "Windows"          # "Windows" or "Linux"
      $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId     $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
      ```
   c. 初回レプリケーション後、VM が保護された状態になるまで待ちます。 このプロセスには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 
   
      保護された状態になると、ジョブの `State` および `StateDescription` は次のように更新されます。 
    
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
8. [テスト フェールオーバーを実行します](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)。


## <a name="next-steps"></a>次のステップ

VMware から Azure への再保護とフェールバックを実行する方法については、「[VMware の再保護とフェールバックを準備する](./vmware-azure-prepare-failback.md)」を参照してください。

Hyper-v から Azure にフェールオーバーするには、「[オンプレミスから Azure へのフェールオーバーを実行する](./site-recovery-failover.md)」を参照してください。 フェールバックについては、「[Hyper-V VM のフェールバックの実行](./hyper-v-azure-failback.md)」を参照してください。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。
