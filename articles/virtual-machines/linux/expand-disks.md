---
title: Azure の Linux VM の仮想ハード ディスクを拡張する | Microsoft Docs
description: Azure CLI 2.0 を使用して、Linux VM の仮想ハード ディスクを拡張する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: 96d50260663f00f5ae2e9b2e0495c91ecb5da4b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421190"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する方法
Azure の Linux 仮想マシン (VM) では、通常、オペレーティング システム (OS) の既定の仮想ハード ディスク サイズは 30 GB です。 [データ ディスクを追加](add-disk.md)して記憶域スペースを追加できますが、既存のデータ ディスクを拡張することもできます。 この記事では、Azure CLI 2.0 を使用して、Linux VM のマネージド ディスクを拡張する方法について詳しく説明します。 

> [!WARNING]
> ディスクのサイズ変更操作を実行する前に、常にデータをバックアップしていることを確認してください。 詳細については、「[Azure での Linux 仮想マシンのバックアップ](tutorial-backup-vms.md)」を参照してください。

## <a name="expand-azure-managed-disk"></a>Azure Managed Disks の拡張
[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/reference-index#az_login) を使用して Azure アカウントにログインしていることを確認します。

この記事では、少なくとも 1 つのデータ ディスクが接続され、準備ができている Azure の既存の VM が必要です。 使用できる VM をまだ用意していない場合は、[データ ディスク付きの VM の作成と準備](tutorial-manage-disks.md#create-and-attach-disks)に関するページを参照してください。

以下のサンプルでは、パラメーター名を独自の値に置き換えてください。 たとえば、*myResourceGroup* と *myVM* といったパラメーター名にします。

1. 仮想ハード ディスクに対する操作は、実行中の VM では実行できません。 [az vm deallocate](/cli/azure/vm#az_vm_deallocate) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > 仮想ハード ディスクを拡張するには、VM の割り当てを解除する必要があります。 `az vm stop` では、コンピューティング リソースは解放されません。 コンピューティング リソースを解放するには、`az vm deallocate` を使用します。

1. 
  [az disk list](/cli/azure/disk#az_disk_list) を使用して、リソース グループに含まれるマネージド ディスクの一覧を表示します。 次の例では、*myResourceGroup* という名前のリソース グループに含まれるマネージド ディスクの一覧を表示します。

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    [az disk update](/cli/azure/disk#az_disk_update) を使用して、必要なディスクを拡張します。 次の例では、*myDataDisk* という名前のマネージド ディスクのサイズを *200* GB に拡張します。

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > マネージド ディスクを拡張すると、更新されたサイズがマネージド ディスクの最も近いサイズにマップされます。 マネージド ディスクの利用可能なサイズとレベルの表については、「[Azure Managed Disks の概要 - 価格と課金](../windows/managed-disks-overview.md#pricing-and-billing)」をご覧ください。

1. [az vm start](/cli/azure/vm#az_vm_start) を使用して VM を起動します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>ディスク パーティションとファイル システムの拡張
拡張ディスクを使用するには、基になるパーティションとファイル システムを拡張する必要があります。

1. 適切な資格情報を使用して VM に SSH 接続します。 [az vm show](/cli/azure/vm#az_vm_show) で、VM のパブリック IP アドレスを取得できます。

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. 拡張ディスクを使用するには、基になるパーティションとファイル システムを拡張する必要があります。

    a. ディスクが既にマウントされている場合は、マウントを解除します。

    ```bash
    sudo umount /dev/sdc1
    ```

    b. `parted` を使用してディスク情報を表示し、パーティションのサイズを変更します。

    ```bash
    sudo parted /dev/sdc
    ```

    `print` を使用して、既存のパーティション レイアウトに関する情報を表示します。 出力は次の例のようになります。これは、基になるディスクのサイズが 215 GB であることを示しています。

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. `resizepart` を使用して、パーティションを拡張します。 パーティション番号 *1* と、新しいパーティションのサイズを入力します。

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. 終了するには、`quit` を入力します。

1. パーティションのサイズを変更したら、`e2fsck` を使用して、パーティションの整合性を確認します。

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. 次に、`resize2fs` を使用して、ファイル システムのサイズを変更します。

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. パーティションを目的の場所 (`/datadrive` など) にマウントします。

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. OS ディスクのサイズが変更されたことを確認するには、`df -h` を使用します。 次の出力例は、データ ドライブ */dev/sdc1* が 200 GB になったことを示しています。

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>次の手順
追加の記憶域が必要な場合に、[Linux VM にデータ ディスクを追加](add-disk.md)します。 ディスク暗号化の詳細については、「[Azure CLI を使って Linux VM のディスクを暗号化する](encrypt-disks.md)」をご覧ください。
