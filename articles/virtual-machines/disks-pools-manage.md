---
title: Azure ディスク プール (プレビュー) を管理する
description: Azure ディスク プールにマネージド ディスクを追加する方法、またはディスクで iSCSI サポートを無効にする方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436989"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Azure ディスク プール (プレビュー) を管理する

この記事では、Azure ディスク プール (プレビュー) にマネージド ディスクを追加する方法と、ディスク プールに追加されたディスクで iSCSI サポートを無効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure PowerShell モジュールの[バージョン 6.1.0 以降](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)をインストールします。

## <a name="add-a-disk-to-a-pool"></a>プールにディスクを追加する

ディスクをディスク プールに追加するには、ディスクが次の要件を満たしている必要があります。
- ディスク プールと同じリージョンおよび可用性ゾーンにある Premium SSD または Ultra Disk である必要があります。
    - Ultra Disk のディスク セクター サイズは 512 バイトである必要があります。
- maxShares の値が 2 以上の共有ディスクである必要があります。
- [ディスク プールに追加するディスクに StoragePool リソース プロバイダーの RBAC アクセス許可を提供する](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions)必要があります。

次のスクリプトでは、ディスク プールにディスクを追加し、iSCSI 経由で公開します。 ディスク プール内の既存のディスクは変更されることなく保持されます。

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>ディスクで iSCSI を無効にし、プールから削除する

ディスクで iSCSI サポートを無効にする前に、ディスクが公開されている iSCSI LUN への未処理の iSCSI 接続がないことを確認します。 ディスクをディスク プールから削除する場合、ディスクは自動的には削除されません。 これにより、データの損失を防ぐことができますが、データの格納に対して引き続き課金されます。 ディスクに格納されているデータが不要な場合は、手動でディスクを削除できます。 これにより、ディスクとそこに格納されているすべてのデータが削除され、それ以上課金されることはなくなります。

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>次のステップ

- ディスク プールを別のサブスクリプションに移動する方法については、「[ディスク プールを別のサブスクリプションに移動する](disks-pools-move-resource.md)」を参照してください。
- ディスク プールをプロビジョニング解除する方法については、「[Azure ディスク プールをプロビジョニング解除する](disks-pools-deprovision.md)」を参照してください。