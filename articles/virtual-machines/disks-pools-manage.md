---
title: Azure ディスク プール (プレビュー) を管理する
description: Azure ディスク プールにマネージド ディスクを追加する方法、またはディスクで iSCSI サポートを無効にする方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59f03b2484bed39a3c562efc6cfb4176b5173964
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083221"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Azure ディスク プール (プレビュー) を管理する

この記事では、Azure ディスク プール (プレビュー) にマネージド ディスクを追加する方法と、ディスク プールに追加されたディスクで iSCSI サポートを無効にする方法について説明します。

## <a name="add-a-disk-to-a-pool"></a>プールにディスクを追加する

ディスクをディスク プールに追加するには、ディスクが次の要件を満たしている必要があります。
- ディスク プールと同じリージョンおよび可用性ゾーンにある Premium SSD、Standard SSD、または Ultra Disk である必要があります。
    - Ultra Disk のディスク セクター サイズは 512 バイトである必要があります。
- maxShares の値が 2 以上の共有ディスクである必要があります。
- [ディスク プールに追加するディスクに StoragePool リソース プロバイダーの RBAC アクセス許可を提供する](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions)必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ディスク プールに移動し、 **[設定]** で **[ディスク]** を選びます。
1. **[既存のディスクの接続]** を選び、ディスクを選択します。
1. 接続するディスクをすべて選択したら、 **[保存]** を選びます。

    :::image type="content" source="media/disk-pools-manage/manage-disk-pool-add.png" alt-text="ディスク プールの [ディスク] ブレードのスクリーンショット。":::

    ディスクを接続したので、それらの LUN を有効にする必要があります。

1. **[設定]** で **[iSCSI]** を選択します。
1. **[Disks enabled for iSCSI]\(iSCSI に対して有効なディスク\)** で **[Add LUN]\(LUN の追加\)** を選びます。
1. 前の手順で接続したディスクを選びます。
1. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

    :::image type="content" source="media/disk-pools-manage/enable-disk-luns.png" alt-text="iSCSI ブレードのスクリーンショット。ディスクの LUN が追加されて有効化されています。":::

ディスクを接続し、LUN を有効にしたので、これを iSCSI データストアとして作成し、Azure VMware Solution プライベート クラウドに接続する必要があります。 詳細については、「[iSCSI LUN をアタッチする](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="prerequisites"></a>前提条件

Azure PowerShell モジュールの[バージョン 6.1.0 以降](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)をインストールします。

次のコマンドを使って、ディスク プール モジュールをインストールします。

```azurepowershell
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery
```

### <a name="add-a-disk-pool"></a>ディスク プールを追加する

次のスクリプトでは、ディスク プールにディスクを追加し、iSCSI 経由で公開します。 ディスク プール内の既存のディスクは変更されることなく保持されます。

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='<LunName>' #Provide the Lun name of the added disk
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

ディスクを接続し、LUN を有効にしたので、これを iSCSI データストアとして作成し、Azure VMware Solution プライベート クラウドに接続する必要があります。 詳細については、「[iSCSI LUN をアタッチする](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>前提条件

Azure CLI の[最新バージョン](/cli/azure/disk-pool)をインストールします。

まだインストールしていない場合は、次のコマンドを使ってディスク プール拡張機能をインストールします。

```azurecli
az extension add -n diskpool
```

### <a name="add-a-disk-pool---cli"></a>ディスク プールを追加する - CLI

次のスクリプトでは、ディスク プールにディスクを追加し、iSCSI 経由で公開します。 ディスク プール内の既存のディスクは変更されることなく保持されます。

```azurecli
# Add a disk to a disk pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --Name $diskPoolName)

diskIds=$(echo $(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json) | sed -e 's/\[ //g' -e 's/\ ]//g' -e 's/\,//g')
for disk in $diskIds; do
    diskPoolUpdateArgs+=(--disks $(echo $disk | sed 's/"//g'))
done

diskId=$(az disk show --resource-group $resourceGroupName --name $diskName --query id | sed 's/"//g')
diskPoolUpdateArgs+=(--disks $diskId)

az disk-pool update "${diskPoolUpdateArgs[@]}"

# Get existing iSCSI LUNs and expose added disk as a new LUN
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunsCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
    targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
done

targetUpdateArgs+=(--luns name=$lunName managed-disk-azure-resource-id=$diskId)

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"
```

ディスクを接続し、LUN を有効にしたので、これを iSCSI データストアとして作成し、Azure VMware Solution プライベート クラウドに接続する必要があります。 詳細については、「[iSCSI LUN をアタッチする](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun)」を参照してください。

---

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>ディスクで iSCSI を無効にし、プールから削除する

ディスクで iSCSI サポートを無効にする前に、ディスクが公開されている iSCSI LUN への未処理の iSCSI 接続がないことを確認します。 ディスクをディスク プールから削除する場合、ディスクは自動的には削除されません。 これにより、データの損失を防ぐことができますが、データの格納に対して引き続き課金されます。 ディスクに格納されているデータが不要な場合は、手動でディスクを削除できます。 これにより、ディスクとそこに格納されているすべてのデータが削除され、それ以上課金されることはなくなります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ディスク プールに移動し、 **[設定]** で **[iSCSI]** を選びます。
1. **[Disks enabled for iSCSI]\(iSCSI に対して有効なディスク\)** で削除したいディスクを選び、 **[Remove LUN]\(LUN の削除\)** を選びます。
1. **[保存]** を選び、処理が完了するまで待ちます。

    :::image type="content" source="media/disk-pools-manage/remove-disk-lun.png" alt-text="ディスク プールの iSCSI ブレードのスクリーンショット。ディスクの LUN を削除します。":::

    LUN を無効にしたので、ディスク プールからディスクを削除できます。

1. **[設定]** で **[ディスク]** を選びます。
1. **[Remove disk from disk pool]\(ディスク プールからディスクを削除する\)** を選び、ディスクを選択します。
1. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

処理が完了すると、ディスクがディスク プールから完全に削除されます。

:::image type="content" source="media/disk-pools-manage/remove-disks-from-pool.png" alt-text="ディスク プールの [ディスク] ブレードのスクリーンショット。プールからディスクが削除されています。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LunForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# Disable iSCSI on a disk and remove it from the pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

# Get existing iSCSI LUNs and remove it from iSCSI target
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    if [ $tmpLunName != $lunName ]; then
        tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
        targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
    fi
done

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"

# Remove disk from pool
diskId=$(az disk show --resource-group $resourceGroupName --name $diskName -- query id | sed 's/"//g')

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --name $diskPoolName)

diskIds=$(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json)
diskLength=$(echo diskIds | jq length)

for (( i=0; i < $diskLength; i++ )); do
    tmpDiskId=$(echo $diskIds | jq .[$i] | sed 's/"//g')

    if [ $tmpDiskId != $diskId ]; then
        diskPoolUpdateArgs+=(--disks $tmpDiskId)
    fi
done

az disk-pool update "${diskPoolUpdateArgs[@]}"
```

---
## <a name="next-steps"></a>次のステップ

- ディスク プールを別のサブスクリプションに移動する方法については、「[ディスク プールを別のサブスクリプションに移動する](disks-pools-move-resource.md)」を参照してください。
- ディスク プールをプロビジョニング解除する方法については、「[Azure ディスク プールをプロビジョニング解除する](disks-pools-deprovision.md)」を参照してください。
