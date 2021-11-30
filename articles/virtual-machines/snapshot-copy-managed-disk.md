---
title: 仮想ハード ディスクの Azure スナップショットを作成する
description: バックアップまたは問題のトラブルシューティングに使うために、ポータル,、PowerShell、または CLI を使用して Azure VM のコピーを作成する方法について説明します。
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: cf523879cd4bf5279d7c07d094ef6f24069c2c0b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709066"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>仮想ハード ディスクのスナップショットを作成する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット

スナップショットは、仮想ハード ディスク (VHD) の完全な読み取り専用コピーです。 スナップショットは、ポイントインタイム バックアップとして使用したり、仮想マシン (VM) の問題のトラブルシューティングに役立てたりすることができます。 オペレーティング システム (OS) またはデータ ディスク VHD の両方のスナップショットを作成できます。

## <a name="create-a-snapshot-of-a-vhd"></a>VHD のスナップショットを作成する

スナップショットを使用して新しい VM を作成する場合は、最初に VM をクリーン シャットダウンしてください。 このアクションにより、進行中のすべてのプロセスがクリアされます。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal を使用してスナップショットを作成するには、これらの手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]** を選択します。
1. **[スナップショット]** を探して選択します。
1. **スナップショット** ウィンドウで、 **[作成]** を選択します。 **[スナップショットの作成]** ウィンドウが表示されます。
1. **[リソース グループ]** では、既存の [リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)を選択するか、新しいグループの名前を入力します。
1. **[名前]** に入力し、新しいスナップショットの **[リージョン]** と **[スナップショットの種類]** を選択します。 スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../availability-zones/az-overview.md)をサポートするリージョンを選択する必要があります。 サポートされるリージョンの一覧については、「[可用性ゾーンを持つ Azure リージョン](../availability-zones/az-region.md#azure-regions-with-availability-zones)」をご覧ください。
1. **[ソース サブスクリプション]** では、バックアップするマネージド ディスクが含まれているサブスクリプションを選択します。
1. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
1. **[ストレージの種類]** では、スナップショットにゾーン冗長ストレージまたは高パフォーマンスストレージが必要な場合を除き、 **[Standard HDD]** を選択します。
1. 必要に応じて、 **[暗号化]** 、 **[ネットワーク]** 、および **[タグ]** の各タブの設定を構成します。 それ以外の場合は、スナップショットに既定の設定が使用されます。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

この例では、[Cloud Shell](https://shell.azure.com/bash) を使用するか、[Azure CLI](/cli/azure/) をインストールする必要があります。

以下の手順に従って、`New-AzSnapshotConfig` コマンドレットと `New-AzSnapshot` コマンドレットを使用してスナップショットを作成します。 この例では、*myResourceGroup* リソース グループに *myVM* という名前の VM があるものとします。 提供されているコード サンプルでは、ソース VM と同じリソース グループおよび同じリージョン内にスナップショットが作成されます。

まず、[New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) コマンドレットを使用して、構成可能なスナップショット オブジェクトを作成します。 その後、[New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) コマンドレットを使用して、ディスクのスナップショットを取得できます。

1. 必要なパラメーターを設定します。 実際の環境に合わせて値を更新してください。

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. [Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットを使用して、コピーする VHD を含む VM を取得します。

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. スナップショットの構成を作成します。 この例では、スナップショットは OS ディスクのものです。 既定では、スナップショットはローカル冗長 Standard Storage を使用します。 親ディスクまたはターゲット ディスクのストレージの種類に関係なく、Premium Storage ではなく Standard Storage にスナップショットを格納することをお勧めします。 プレミアム スナップショットには追加コストが発生します。

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   ゾーン回復性のあるストレージにスナップショットを格納する場合は、[availability zones](../availability-zones/az-overview.md) をサポートしているリージョン内にスナップショットを作成し、`-SkuName Standard_ZRS` パラメーターを含める必要があります。 Availability Zones をサポートするリージョンのリストについては、「[Azure のリージョンと Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones)」をご覧ください。

1. スナップショットを取得する。

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) コマンドレットを使用して、スナップショットが存在することを確認します。

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

この例では、[Cloud Shell](https://shell.azure.com/bash) を使用するか、[Azure CLI](/cli/azure/) をインストールする必要があります。

以下の手順に従って、`az snapshot create` コマンドと `--source-disk` パラメーターを使用してスナップショットを取得します。 この例では、*myResourceGroup* リソース グループに *myVM* という名前の VM があるものとします。 提供されているコード サンプルでは、ソース VM と同じリソース グループおよび同じリージョン内にスナップショットが作成されます。

1. [az vm show](/cli/azure/vm#az_vm_show) を使用して、ディスク ID を取得します。

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. [az snapshot create](/cli/azure/snapshot#az_snapshot_create) を使用して、*osDisk-backup* という名前のスナップショットを取得します。 この例では、スナップショットは OS ディスクのものです。 既定では、スナップショットはローカル冗長 Standard Storage を使用します。 親ディスクまたはターゲット ディスクのストレージの種類に関係なく、Premium Storage ではなく Standard Storage にスナップショットを格納することをお勧めします。 プレミアム スナップショットには追加コストが発生します。

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、オプションの `--sku Standard_ZRS` パラメーターを含める必要があります。 [Availability Zones](../availability-zones/az-region.md#azure-regions-with-availability-zones) のリストは、ここで確認できます。
    
1. [az snapshot list](/cli/azure/snapshot#az_snapshot_list) を使用して、スナップショットが存在することを確認します。
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>次のステップ

スナップショットから仮想マシンをデプロイします。 スナップショットからマネージド ディスクを作成し、新しいマネージド ディスクを OS ディスクとして添付します。

# <a name="portal"></a>[ポータル](#tab/portal)

詳細については、[Azure portal を使用して VHD から VM を作成する](windows/create-vm-specialized-portal.md)方法に関するページの例をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

詳細については、「[PowerShell を使用して特殊化されたディスクから Windows VM を作成する](windows/create-vm-specialized.md)」にある例をご覧ください。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

詳細については、「[Azure CLI を使用した完全な Linux 仮想マシンの作成](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)」をご覧ください。

---