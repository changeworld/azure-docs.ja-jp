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
ms.openlocfilehash: 783299359b1b7b9cbe75fd36f534ac18563c0fee
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102929"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Azure PowerShell モジュールまたは Azure CLI を使用してパフォーマンス レベルを変更します

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

### <a name="prerequisites"></a>前提条件

ダウンタイムなしでディスクのパフォーマンス レベルを変更するには、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、サブスクリプションの機能を有効にしてください。

1.  次のコマンドを実行して、お使いのサブスクリプションにこの機能を登録します

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  この機能を試す前に、次のコマンドを使用して、登録状態が **Registered** であることを確認してください (数分かかる場合があります)。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ダウンタイムなしでディスクのパフォーマンス レベルを変更するには、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、サブスクリプションの機能を有効にしてください。

1.  次のコマンドを実行して、お使いのサブスクリプションにこの機能を登録します

    ```azurepowershell
     Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```
 
1.  この機能を試す前に、次のコマンドを使用して、登録状態が **Registered** であることを確認してください (数分かかる場合があります)。

    ```azurepowershell
    Register-AzProviderFeature -FeatureName "LiveTierChange" -ProviderNamespace "Microsoft.Compute" 
    ```

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
