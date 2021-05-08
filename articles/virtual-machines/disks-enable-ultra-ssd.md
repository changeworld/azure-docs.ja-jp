---
title: VM 用の Ultra ディスク - Azure マネージド ディスク
description: Azure VM 用の Ultra ディスクについて説明します。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 43dac1692dd6ee4ed1ab67a9b18ca69738e0a0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580505"
---
# <a name="using-azure-ultra-disks"></a>Azure Ultra ディスクの使用

この記事では、Ultra ディスクをデプロイして使用する方法を説明します。Ultra ディスクに関する概念的な情報については、「[Azure で使用できるディスクの種類](disks-types.md#ultra-disk)」を参照してください。

Azure Ultra ディスクは、Azure IaaS 仮想マシン (VM) に高スループット、高 IOPS、および一貫性のある低待機時間のディスク ストレージを提供します。 この新しいオファリングは、Microsoft の既存のディスク オファリングと同じレベルの可用性で最上のパフォーマンスを提供します。 Ultra ディスクの 1 つの主なメリットは、VM を再起動することなく、SSD のパフォーマンスをワークロードと共に動的に変更する機能です。 Ultra ディスクは、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなどのデータ集中型のワークロードに適しています。

## <a name="ga-scope-and-limitations"></a>GA の範囲と制限事項

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM のサイズと利用可能なリージョンの確認

### <a name="vms-using-availability-zones"></a>可用性ゾーンを使用する VM

Ultra ディスクを利用するには、ご使用の可用性ゾーンを確認する必要があります。 すべてのリージョンで、Ultra ディスクに対してすべての VM サイズがサポートされるわけではありません。 ご使用のリージョン、ゾーン、および VM サイズで Ultra ディスクがサポートされているかどうかを確認するには、次のコマンドのいずれかを実行します。最初に、必ず **region**、**vmSize**、および **subscription** の値を置き換えてください。

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

この応答は、下のフォームのようになります。ここで X は、選択したリージョンでのデプロイのために使用するゾーンです。 X は 1、2、3 のいずれかになります。

**[ゾーン]** 値を保持します。これは可用性ゾーンを表し、後で Ultra ディスクをデプロイするために必要になります。

|ResourceType  |名前  |場所  |ゾーン  |Restriction  |機能  |値  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> コマンドから応答がなかった場合、選択した VM サイズは、選択したリージョンの Ultra ディスクではサポートされていません。

これでデプロイ先のゾーンがわかったので、この記事のデプロイ手順に従って、Ultra ディスクが接続された VM をデプロイするか、または Ultra ディスクを既存の VM に接続します。

### <a name="vms-with-no-redundancy-options"></a>冗長オプションのない VM

一部のリージョンに Ultra ディスクをデプロイする場合、現行では、冗長オプションなしでデプロイする必要があります。 ただし、Ultra ディスク対応のすべてのディスク サイズがこれらのリージョンにあるとは限りません。 Ultra ディスクをサポートしているディスク サイズを判断する目的で、次のコード スニペットのいずれかを利用できます。 まず、`vmSize` と `subscription` の値を置き換えてください。

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

応答は次のフォームのようになります。`UltraSSDAvailable   True` は、このリージョンでは VM サイズで Ultra ディスクをサポートしているかどうかを示します。

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure Resource Manager を使用して Ultra ディスクをデプロイする

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA の範囲と制限事項](#ga-scope-and-limitations)」セクションを参照してください。

複数の Ultra ディスクを含む VM を作成する場合は、[複数の Ultra ディスクを含む VM の作成](https://aka.ms/ultradiskArmTemplate)に関するページにあるサンプルを参照してください。

独自のテンプレートを使用する場合は、`Microsoft.Compute/virtualMachines` と `Microsoft.Compute/Disks` の **apiVersion** が `2018-06-01` (またはそれ以降) に設定されていることを確認します。

ディスク SKU を **UltraSSD_LRS** に設定し、ディスク容量、IOPS、可用性ゾーン、およびスループット (MBps) を設定して、Ultra ディスクを作成します。

VM がプロビジョニングされたら、データ ディスクをパーティション分割してフォーマットし、ワークロード用に構成できます。


## <a name="deploy-an-ultra-disk"></a>Ultra ディスクをデプロイする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

このセクションでは、データ ディスクとして Ultra ディスクを備えた仮想マシンをデプロイする方法について説明します。 仮想マシンのデプロイに関する知識があることを前提としています。そうでない場合は、「[クイックスタート: Azure portal で Windows 仮想マシンを作成する](./windows/quick-create-portal.md)」を参照してください。

1. [Azure portal](https://portal.azure.com/) にサインインし、[仮想マシン (VM) のデプロイ] に移動します。
1. [サポートされている VM サイズとリージョン](#ga-scope-and-limitations)を必ず選択してください。
1. **[可用性オプション]** で **[可用性ゾーン]** を選択します。
1. 任意の選択を行って、残りの項目を入力します。
1. **[ディスク]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="VM 作成フロー、[基本] ブレードのスクリーンショット。" lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. [ディスク] ブレードで、 **[Ultra Disk の互換性を有効にする]** に対して **[はい]** を選択します。
1. **[新しいディスクの作成と接続]** を選択して、Ultra ディスクを今すぐ接続します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="VM 作成フロー、[ディスク] ブレードのスクリーンショット。Ultra が有効になっており、[新しいディスクの作成と接続] が強調表示されています。" :::

1. **[新しいディスクの作成]** ブレードで、名前を入力し、 **[サイズの変更]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="[新しいディスクの作成] ブレードのスクリーンショット。[サイズの変更] が強調表示されています。":::


1. **ディスク SKU** を **｢Ultra Disk** に変更します。
1. **[カスタム ディスク サイズ (GiB)]** 、 **[ディスク IOPS]** 、および **[ディスク スループット]** を任意のものに変更します。
1. 両方のブレードで **[OK]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="[ディスクサイズの選択] ブレードのスクリーンショット。ストレージの種類として選択された Ultra ディスクと、その他の値が強調表示されています。":::

1. VM のデプロイを続行します。それは、他の VM をデプロイする場合と同じになります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA のスコープと制限事項](#ga-scope-and-limitations)」セクションを参照してください。

Ultra ディスクを接続するには、Ultra ディスクを使用できる VM を作成する必要があります。

**$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** の各変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-vm-size-and-region-availability)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の CLI コマンドを実行して、Ultra 対応 VM を作成します。

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA のスコープと制限事項](#ga-scope-and-limitations)」セクションを参照してください。

Ultra ディスクを使用するには、Ultra ディスクを使用できる VM を作成する必要があります。 **$resourcegroup** 変数と **$vmName** 変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-vm-size-and-region-availability)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の [New-AzVm](/powershell/module/az.compute/new-azvm) コマンドを実行して、Ultra 対応 VM を作成します。

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>ディスクを作成して接続する

VM をデプロイしたら、次のスクリプトを使用して、Ultra ディスクを作成して接続できます。

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Ultra ディスクをデプロイする - 512 バイトのセクター サイズ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインし、**ディスク** を検索して選択します。
1. **[+ 新規]** を選択して新しいディスクを作成します。
1. Ultra Disk をサポートするリージョンを選択し、可用性ゾーンを選択して、残りの値を必要に応じて入力します。
1. **[サイズの変更]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="ディスクの作成ブレード、リージョン、可用性ゾーン、サイズの変更が強調表示されているスクリーンショット。":::

1. **[ディスク SKU]** で **[Ultra Disk]** を選択し、必要なパフォーマンスの値を入力して、 **[OK]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="Ultra Disk 作成のスクリーンショット。":::

1. **[基本]** ブレードで、 **[詳細設定]** タブを選択します。
1. **[Logical sector size]\(論理セクターサイズ\)** で **[512]** を選択し、 **[確認と作成]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="Ultra Disk の論理セクターサイズを 512 に変更するためのセレクターのスクリーンショット。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA のスコープと制限事項](#ga-scope-and-limitations)」セクションを参照してください。

Ultra ディスクを接続するには、Ultra ディスクを使用できる VM を作成する必要があります。

**$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** の各変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-vm-size-and-region-availability)に取得したご自身の可用性ゾーンの値に設定します。 次に、次の CLI コマンドを実行して、512 バイトのセクター サイズである Ultra ディスクを備えた VM を作成します。

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA のスコープと制限事項](#ga-scope-and-limitations)」セクションを参照してください。

Ultra ディスクを使用するには、Ultra ディスクを使用できる VM を作成する必要があります。 **$resourcegroup** 変数と **$vmName** 変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-vm-size-and-region-availability)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の [New-AzVm](/powershell/module/az.compute/new-azvm) コマンドを実行して、Ultra 対応 VM を作成します。

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

512 バイトのセクター サイズである Ultra ディスクを作成して接続するには、次のスクリプトを使用します。

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Ultra ディスクを接続する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。 既存の VM 上で Ultra ディスクを有効にした後、データ ディスクとして接続します。 Ultra ディスクの互換性を有効にするには、VM を停止する必要があります。 VM を停止したら、互換性を有効にし、VM を再起動できます。 互換性が有効になったら、Ultra ディスクを接続できます。

1. VM に移動して停止し、割り当てが解除されるのを待ちます。
1. VM の割り当てが解除されたら、 **[ディスク]** を選択します。
1. **[追加設定]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="[ディスク] ブレードのスクリーンショット、[追加設定] が強調表示されている。":::

1. **[Ultra Disk の互換性を有効にする]** に対して **[はい]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="[Ultra Disk の互換性を有効にする] のスクリーンショット。":::

1. **[保存]** を選択します。
1. **[新しいディスクを作成し接続する]** を選択し、新しいディスクの名前を入力します。
1. **[ストレージの種類]** で **[Ultra Disk]** を選択します。
1. **[サイズ (GiB)]** 、 **[最大 IOPS]** 、および **[最大スループット]** を任意のものに変更します。
1. ディスクのブレードに戻ったら、 **[保存]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="[ディスク] ブレードのスクリーンショット。新しい Ultra Disk を追加しています。":::

1. VM をもう一度起動します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>既存 VM で Ultra ディスクの互換性を有効にする - CLI

「[GA の範囲と制限事項](#ga-scope-and-limitations)」にある要件を VM が満たし、[アカウントの該当ゾーン](#determine-vm-size-and-region-availability)に置かれている場合、VM で Ultra ディスク互換性を有効にできます。

Ultra ディスクの互換性を有効にするには、VM を停止する必要があります。 VM を停止したら、互換性を有効にし、VM を再起動できます。 互換性が有効になったら、Ultra ディスクを接続できます。

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Ultra ディスクを作成する - CLI

これで Ultra ディスクを接続できる VM が用意されたので、Ultra ディスクを作成し、その VM に接続できます。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>ディスクを接続する - CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>既存 VM で Ultra ディスクの互換性を有効にする - PowerShell

「[GA の範囲と制限事項](#ga-scope-and-limitations)」にある要件を VM が満たし、[アカウントの該当ゾーン](#determine-vm-size-and-region-availability)に置かれている場合、VM で Ultra ディスク互換性を有効にできます。

Ultra ディスクの互換性を有効にするには、VM を停止する必要があります。 VM を停止したら、互換性を有効にし、VM を再起動できます。 互換性が有効になったら、Ultra ディスクを接続できます。

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Ultra ディスクを作成して接続する - PowerShell

これで Ultra ディスクを使用できる VM が用意されたので、Ultra ディスクを作成し、その VM に接続できます。

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Ultra ディスクのパフォーマンスを調整する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。 これらの調整は、Azure portal から実行することも、ディスク上で実行することもできます。

1. VM に移動し、 **[ディスク]** を選択します。
1. パフォーマンスを変更する Ultra ディスクを選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="VM の [ディスク] ブレードのスクリーンショット。Ultra ディスクが強調表示されています。":::

1. **[Size + performance]\(サイズとパフォーマンス\)** を選択し、変更を行います。
1. **[保存]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="Ultra ディスクの [構成] ブレードのスクリーンショット。ディスク サイズ、iops、スループットが強調表示され、[保存] が強調表示されています。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。次のコマンドは、この機能を使用する方法を示しています。

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell を使用して Ultra ディスクのパフォーマンスを調整する

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。次のコマンドは、ディスクを切断することなくパフォーマンスを調整する例を示しています。

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>次のステップ

- [Azure Kubernetes Service での Azure Ultra Disks の使用 (プレビュー)](../aks/use-ultra-disks.md)。
- [ログ ディスクを Ultra Disk に移行する](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md)。
