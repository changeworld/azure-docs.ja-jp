---
title: "Azure の Linux VM を非管理対象ディスクから Managed Disks に変換する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Azure CLI 2.0 を使用して Linux VM を非管理対象ディスクから Azure Managed Disks に変換する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 37c47061b0774d9670b9a2d304d069d5f683c2d2
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Linux VM を非管理対象ディスクから Azure Managed Disks に変換する

非管理対象ディスクを使用する既存の Linux 仮想マシン (VM) を所有している場合、[Azure Managed Disks](../../storage/storage-managed-disks-overview.md) を使用するように VM を変換することができます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure CLI を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli.md)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>単一インスタンスの VM を変換する
このセクションでは、単一インスタンスの Azure VM を非管理対象ディスクから Managed Disks に変換する方法について説明します。 (VM が可用性セットに含まれている場合は次のセクションを参照してください。)このプロセスを使って、Premium (SSD) の非管理対象ディスクから Premium Managed Disks に、または Standard (HDD) の非管理対象ディスクから Standard Managed Disks に変換できます。

1. [az vm deallocate](/cli/azure/vm#deallocate) で VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm#convert) で VM を Managed Disks に変換します。 次の例では、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Managed Disks に変換した後、[az vm start](/cli/azure/vm#start) で VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>可用性セットの VM を変換する

Managed Disks に変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

可用性セットを変換する前に、可用性セット内のすべての VM の割り当てを解除する必要があります。 可用性セット自体を管理対象の可用性セットに変換した後、Managed Disks へのすべての VM の変換を計画します。 その後、すべての VM を起動し、通常どおり操作を続行します。

1. [az vm availability-set list](/cli/azure/vm/availability-set#list) で、可用性セット内のすべての VM の一覧を取得します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットに含まれるすべての VM の一覧を取得します。

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate) ですべての VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set#convert) で、可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを変換します。

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm#convert) ですべての VM を Managed Disks に変換します。 次の例では、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Managed Disks に変換した後、[az vm start](/cli/azure/vm#start) ですべての VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks と Azure Storage Service Encryption
非管理対象ディスクが [Azure Storage Service Encryption](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使って暗号化されたことがあるストレージ アカウントに存在する場合は、前述の手順を使って非管理対象ディスクを管理ディスクに変換することはできません。 次の手順では、暗号化されたストレージ アカウントにある非管理対象ディスクをコピーして使用する方法について詳しく説明します。

1. [az storage blob copy start](/cli/azure/storage/blob/copy#start) を使用して、Azure Storage Service Encryption が有効にされたことのないストレージ アカウントに仮想ハード ディスク (VHD) をコピーします。

2. コピーした VM は、次の方法のいずれかで使用します。

* Managed Disks を使う VM を作成し、[az vm create](/cli/azure/vm#create) での作成時にその VHD ファイルを指定します。

* [az vm disk attach](/cli/azure/vm/disk#attach) を使って、コピーした VHD を Managed Disks で実行中の VM に接続します。

## <a name="next-steps"></a>次のステップ
記憶域のオプションについて詳しくは、「[Azure Managed Disks の概要](../../storage/storage-managed-disks-overview.md)」をご覧ください。

