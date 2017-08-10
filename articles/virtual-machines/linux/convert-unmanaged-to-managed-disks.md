---
title: "Azure の Linux 仮想マシンを非管理対象ディスクから管理ディスクに変換する - Azure Managed Disks |Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Azure CLI 2.0 を使用して Linux VM を非管理対象から管理ディスクに変換する方法"
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
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3109da1dac6ebb6564c94b5c6635ded77ea9be8d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux 仮想マシンを非管理対象ディスクから管理ディスクに変換する

非管理対象ディスクを使用する既存の Linux 仮想マシン (VM) を所有している場合、[Azure Managed Disks](../../storage/storage-managed-disks-overview.md) サービスを使用して、管理ディスクを使用するように VM を変換できます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure CLI を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>単一インスタンスの VM を変換する
このセクションでは、単一インスタンスの Azure VM を非管理対象ディスクから Managed Disks に変換する方法について説明します。 (VM が可用性セットに含まれている場合は次のセクションを参照してください。)このプロセスを使用して、VM を非管理対象の Premium (SSD) ディスクから Premium 管理ディスクに、または非管理対象の標準 (HDD) ディスクから標準管理ディスクに変換できます。

1. [az vm deallocate](/cli/azure/vm#deallocate) を使用して VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm#convert) を使用して VM を管理ディスクに変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. 管理ディスクに変換した後、[az vm start](/cli/azure/vm#start) で VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>可用性セットの VM を変換する

Managed Disks に変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

可用性セットを変換する前に、可用性セット内のすべての VM の割り当てを解除する必要があります。 可用性セット自体を管理対象の可用性セットに変換したあと、すべての VM を管理ディスクに変換する計画を立てます。 その後、すべての VM を起動し、通常どおり操作を続行します。

1. [az vm availability-set list](/cli/azure/vm/availability-set#list) を使用して、可用性セット内のすべての VM を一覧表示します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットに含まれるすべての VM の一覧を取得します。

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate) を使用して、すべての VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set#convert) を使用して、可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを変換します。

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm#convert) を使用して、すべての VM を管理ディスクに変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. 管理ディスクに変換した後、[az vm start](/cli/azure/vm#start) ですべての VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks と Azure Storage Service Encryption
[Azure Storage Service Encryption](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) で暗号化された履歴があるストレージ アカウントに非管理対象ディスクが存在する場合は、前述の手順で非管理対象ディスクを管理ディスクに変換することはできません。 次の手順では、暗号化されたストレージ アカウントにある非管理対象ディスクをコピーして使用する方法について詳しく説明します。

1. Azure Storage Service Encryption が有効になった履歴がないストレージ アカウントに、[az storage blob copy start](/cli/azure/storage/blob/copy#start) を使用して VHD をコピーします。

2. コピーした VM は、次の方法のいずれかで使用します。

   * [az vm create](/cli/azure/vm#create) を使用して、管理ディスクを使用する VM を作成し、その VHD ファイルを指定します。

   * [az vm disk attach](/cli/azure/vm/disk#attach) を使用して、管理ディスクを使用する実行中の VM にコピーした VHD を接続します。

## <a name="next-steps"></a>次のステップ
ストレージのオプションについて詳しくは、「[Azure Managed Disks の概要](../../storage/storage-managed-disks-overview.md)」をご覧ください。

