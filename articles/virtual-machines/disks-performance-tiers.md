---
title: Azure マネージド ディスクのパフォーマンスを変更する - CLI/PowerShell
description: Azure PowerShell モジュールまたは Azure CLI を使用し、既存のマネージド ディスクのパフォーマンス レベルを変更する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ebc655cc772f0d05ef44a453076d5e17d217a54d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038386"
---
# <a name="change-your-performance-tier-without-downtime-using-the-azure-powershell-module-or-the-azure-cli"></a>Azure PowerShell モジュールまたは Azure CLI を使用してダウンタイムなしでパフォーマンス レベルを変更する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="prerequisites"></a>前提条件
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにサインインします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
最新の [Azure PowerShell バージョン](/powershell/azure/install-az-ps)をインストールし、`Connect-AzAccount` を使用して Azure アカウントにサインインします。

---

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>ベースライン レベルよりも高いレベルで空のデータ ディスクを作成する
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace イメージから、ベースライン レベルよりも高いレベルで OS ディスクを作成する

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk-without-downtime"></a>ダウンタイムなしでディスクのレベルを更新する

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 実行中の VM に接続されている場合でも、ディスクのレベルを更新します

    ```azurecli
    resourceGroupName=<yourResourceGroupNameHere>
    diskName=<yourDiskNameHere>
    performanceTier=<yourDesiredPerformanceTier>

    az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 実行中の VM に接続されている場合でも、ディスクのレベルを更新します

    ```azurepowershell
    $resourceGroupName='yourResourceGroupName'
    $diskName='yourDiskName'
    $performanceTier='P1'

    $diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

    Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
    ```
---

## <a name="show-the-tier-of-a-disk"></a>ディスクのレベルを表示する

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>次のステップ

より高いパフォーマンス レベルを利用できるようにディスクのサイズを変更する必要がある場合は、次の記事を参照してください。

- [Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](linux/expand-disks.md)
- [Windows 仮想マシンにアタッチされたマネージド ディスクを拡張する](windows/expand-os-disk.md)
