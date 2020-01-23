---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ff3409fad12e54be5ac00ead3ca44c1f24bb0af8
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268314"
---
Azure Ultra ディスクは、Azure IaaS 仮想マシン (VM) に高スループット、高 IOPS、および一貫性のある低待機時間のディスク ストレージを提供します。 この新しいオファリングは、Microsoft の既存のディスク オファリングと同じレベルの可用性で最上のパフォーマンスを提供します。 Ultra ディスクの 1 つの主なメリットは、VM を再起動することなく、SSD のパフォーマンスをワークロードと共に動的に変更する機能です。 Ultra ディスクは、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなどのデータ集中型のワークロードに適しています。

## <a name="ga-scope-and-limitations"></a>GA の範囲と制限事項

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM のサイズと利用可能なリージョンの確認

Ultra ディスクを利用するには、ご使用の可用性ゾーンを確認する必要があります。 すべてのリージョンで、Ultra ディスクに対してすべての VM サイズがサポートされるわけではありません。 ご使用のリージョン、ゾーン、および VM サイズで Ultra ディスクがサポートされているかどうかを確認するには、次のコマンドのいずれかを実行します。最初に、必ず **region**、**vmSize**、および **subscription** の値を置き換えてください。

CLI:

```bash
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

この応答は、下のフォームのようになります。ここで X は、選択したリージョンでのデプロイのために使用するゾーンです。 X は 1、2、3 のいずれかになります。

**[ゾーン]** 値を保持します。これは可用性ゾーンを表し、後で Ultra ディスクをデプロイするために必要になります。

|ResourceType  |Name  |Location  |ゾーン  |Restriction  |機能  |値  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> コマンドから応答がなかった場合、選択した VM サイズは、選択したリージョンの Ultra ディスクではサポートされていません。

これでデプロイ先のゾーンがわかったので、この記事のデプロイ手順に従って、Ultra ディスクが接続された VM をデプロイするか、または Ultra ディスクを既存の VM に接続します。

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure Resource Manager を使用して Ultra ディスクをデプロイする

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA の範囲と制限事項](#ga-scope-and-limitations)」を参照してください。

複数の Ultra ディスクを含む VM を作成する場合は、[複数の Ultra ディスクを含む VM の作成](https://aka.ms/ultradiskArmTemplate)に関するページにあるサンプルを参照してください。

独自のテンプレートを使用する場合は、`Microsoft.Compute/virtualMachines` と `Microsoft.Compute/Disks` の **apiVersion** が `2018-06-01` (またはそれ以降) に設定されていることを確認します。

ディスク SKU を **UltraSSD_LRS** に設定し、ディスク容量、IOPS、可用性ゾーン、およびスループット (MBps) を設定して、Ultra ディスクを作成します。

VM がプロビジョニングされたら、データ ディスクをパーティション分割してフォーマットし、ワークロード用に構成できます。


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Azure portal を使用して Ultra Disk をデプロイする

このセクションでは、データ ディスクとして Ultra ディスクを備えた仮想マシンをデプロイする方法について説明します。 仮想マシンのデプロイに関する知識があることを前提としています。そうでない場合は、「[クイックスタート: Azure portal で Windows 仮想マシンを作成する](../articles/virtual-machines/windows/quick-create-portal.md)」を参照してください。

- [Azure portal](https://portal.azure.com/) にサインインし、[仮想マシン (VM) のデプロイ] に移動します。
- [サポートされている VM サイズとリージョン](#ga-scope-and-limitations)を必ず選択してください。
- **[可用性オプション]** で **[可用性ゾーン]** を選択します。
- 任意の選択を行って、残りの項目を入力します。
- **[ディスク]** を選択します。

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- [ディスク] ブレードで、 **[Ultra Disk の互換性を有効にする]** に対して **[はい]** を選択します。
- **[新しいディスクの作成と接続]** を選択して、Ultra ディスクを今すぐ接続します。

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- **[新しいディスクの作成]** ブレードで、名前を入力し、 **[サイズの変更]** を選択します。
- **[アカウントの種類]** を **[Ultra Disk]** に変更します。
- **[カスタム ディスク サイズ (GiB)]** 、 **[ディスク IOPS]** 、および **[ディスク スループット]** を任意のものに変更します。
- 両方のブレードで **[OK]** を選択します。
- VM のデプロイを続行します。それは、他の VM をデプロイする場合と同じになります。

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Azure portal を使用して Ultra ディスクをデプロイする

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。 既存の VM 上で Ultra ディスクを有効にした後、データ ディスクとして接続します。

- VM に移動し、 **[ディスク]** を選択します。
- **[編集]** を選択します。

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- **[Ultra Disk の互換性を有効にする]** に対して **[はい]** を選択します。

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- **[保存]** を選択します。
- **[データ ディスクの追加]** を選択し、 **[名前]** ドロップダウンから **[ディスクの作成]** を選択します。

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 新しいディスクの名前を入力し、 **[サイズの変更]** を選択します。
- **[アカウントの種類]** を **[Ultra Disk]** に変更します。
- **[カスタム ディスク サイズ (GiB)]** 、 **[ディスク IOPS]** 、および **[ディスク スループット]** を任意のものに変更します。
- **[OK]** を選択し、 **[作成]** を選択します。

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- ディスクのブレードに戻ったら、 **[保存]** を選択します。

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Azure portal を使用して Ultra ディスクのパフォーマンスを調整する

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。 これらの調整は、Azure portal から実行することも、ディスク上で実行することもできます。

- VM に移動し、 **[ディスク]** を選択します。
- パフォーマンスを変更する Ultra ディスクを選択します。

![selecting-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- **[構成]** を選択し、変更を行います。
- **[保存]** を選択します。

![configuring-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>CLI を使用して Ultra ディスクをデプロイする

最初に、デプロイする VM のサイズを決定します。 サポートされている VM サイズの一覧については、「[GA のスコープと制限事項](#ga-scope-and-limitations)」セクションを参照してください。

Ultra ディスクを接続するには、Ultra ディスクを使用できる VM を作成する必要があります。

**$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** の各変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-vm-size-and-region-availability)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の CLI コマンドを実行して、Ultra 対応 VM を作成します。

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>CLI を使用して Ultra ディスクを作成する

これで Ultra ディスクを接続できる VM が用意されたので、Ultra ディスクを作成し、その VM に接続できます。

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>CLI を使用して Ultra ディスクを VM に接続する

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>CLI を使用して Ultra ディスクのパフォーマンスを調整する

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。次のコマンドは、この機能を使用する方法を示しています。

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>PowerShell を使用して Ultra ディスクをデプロイする

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

### <a name="create-an-ultra-disk-using-powershell"></a>PowerShell を使用して Ultra ディスクを作成する

これで Ultra ディスクを使用できる VM が用意されたので、Ultra ディスクを作成し、その VM に接続できます。

```powershell
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
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>PowerShell を使用して Ultra ディスクを VM に接続する

あるいは、既存の VM が Ultra ディスクを使用できるリージョン/可用性ゾーンに存在する場合は、新しい VM を作成しなくても Ultra ディスクを使用できます。

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell を使用して Ultra ディスクのパフォーマンスを調整する

Ultra ディスクには、ユーザーがパフォーマンスを調整できる固有の機能があります。次のコマンドは、ディスクを切断することなくパフォーマンスを調整する例を示しています。

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>次のステップ

新しいディスクの種類を試してみたい場合は、[この調査を使用してアクセスを要求](https://aka.ms/UltraDiskSignup)してください。
