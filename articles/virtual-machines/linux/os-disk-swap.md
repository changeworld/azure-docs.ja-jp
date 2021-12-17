---
title: Azure CLI を使用して OS ディスク間をスワップする
description: Azure CLI を使用して Azure 仮想マシンによって使用されるオペレーティング システム ディスクを変更します。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f186052117f40167c9e4e72e350bf689f43bd8b1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692841"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Azure CLI を使用して Azure VM によって使用される OS ディスクを変更する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブルなスケール セット 

既存の VM があり、そのディスクをバックアップ ディスクまたは別の OS ディスクに交換する場合、Azure CLI を使用して OS ディスクを交換できます。 VM を削除して再作成する必要はありません。 別のリソース グループ内のマネージド ディスクでも、まだ使用されていない場合に限り使用することができます。

この場合は、VM を停止するか、割り当てを解除する必要があります。その後は、マネージド ディスクのリソース ID を別のマネージド ディスクのリソース ID に置き換えることができます。 

VM のサイズとストレージの種類が、接続するディスクと互換性があることを確認します。 たとえば、使用するディスクが Premium Storage 内にある場合、VM は Premium Storage (DS シリーズのサイズなど) に対応している必要があります。

この記事では、Azure CLI バージョン 2.0.25 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 


[az disk list](/cli/azure/disk) を使用してリソース グループ内のディスクの一覧を取得します。

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


ディスクを交換する前に、[az vm stop](/cli/azure/vm) を使用して VM を停止するか、割り当てを解除します。

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


`--osdisk` パラメーターに新しいディスクの完全なリソース ID を指定して、[az vm update](/cli/azure/vm#az_vm_update) を使用します。 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
[az vm start](/cli/azure/vm) を使用して VM を再起動します。

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**次の手順**

ディスクのコピーを作成する方法については、「[スナップショットの作成](snapshot-copy-managed-disk.md)」を参照してください。
