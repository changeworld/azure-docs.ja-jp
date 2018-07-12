---
title: 'チュートリアル: Azure CLI を使用して Azure ディスクを管理する | Microsoft Docs'
description: このチュートリアルでは、Azure CLI 2.0 を使用して、仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 889facbf9612f2462a10c886a428ac052becefd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704489"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli-20"></a>チュートリアル - Azure CLI 2.0 を使用して Azure ディスクを管理する

Azure Virtual Machines (VM) では、オペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクのデプロイと管理方法を示します。 内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備
> * ディスクのサイズ変更
> * ディスクのスナップショット

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="default-azure-disks"></a>既定の Azure ディスク

Azure 仮想マシンを作成すると、2 つのディスクが仮想マシンに自動的に接続されます。

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 2 TB までサイズを変更でき、VM のオペレーティング システムをホストします。 OS ディスクには既定で */dev/sda* というラベルが付けられています。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 この構成のため、アプリケーションまたはデータ用に OS ディスクを**使用しないでください**。 アプリケーションとデータ用には、このチュートリアルの後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM と同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 一時ディスクは、パフォーマンスが高く、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM を新しいホストに移動すると、一時ディスクに格納されているデータは削除されます。 一時ディスクのサイズは VM のサイズによって決まります。 一時ディスクには */dev/sdb* のラベルが付けられており、*/mnt* というマウント ポイントがあります。

### <a name="temporary-disk-sizes"></a>一時ディスクのサイズ

| type | 一般的なサイズ | 一時ディスクの最大サイズ (GiB) |
|----|----|----|
| [汎用](sizes-general.md) | A、B、D シリーズ | 1600 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 576 |
| [メモリの最適化](sizes-memory.md) | D、E、G、M シリーズ | 6144 |
| [ストレージの最適化](sizes-storage.md) | L シリーズ | 5630 |
| [GPU](sizes-gpu.md) | N シリーズ | 1440 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 2000 |

## <a name="azure-data-disks"></a>Azure データ ディスク

アプリケーションのインストールとデータの格納を行うために、データ ディスクを追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 各データ ディスクの最大容量は 4 TB です。 仮想マシンのサイズによって、VM に接続できるデータ ディスクの数が決まります。 各 VM vCPU に、2 つのデータ ディスクを接続できます。

### <a name="max-data-disks-per-vm"></a>VM あたりの最大データ ディスク数

| type | VM サイズ | VM あたりの最大データ ディスク数 |
|----|----|----|
| [汎用](sizes-general.md) | A、B、D シリーズ | 64 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 64 |
| [メモリの最適化](../virtual-machines-windows-sizes-memory.md) | D、E、および G シリーズ | 64 |
| [ストレージの最適化](../virtual-machines-windows-sizes-storage.md) | L シリーズ | 64 |
| [GPU](sizes-gpu.md) | N シリーズ | 64 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 64 |

## <a name="vm-disk-types"></a>VM ディスクの種類

Azure では、2 種類のディスクを提供しています。

### <a name="standard-disk"></a>Standard ディスク

Standard Storage では、HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

### <a name="premium-disk"></a>Premium ディスク

Premium ディスクは、SSD ベースの高性能で待機時間の短いディスクによってサポートされています。 実稼働ワークロードを実行する VM に最適です。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、ディスク サイズが 128 GB 未満の場合、ディスクの種類は P10 です。 ディスク サイズが 129 ～ 512 GB の場合、サイズは P20 です。 512 GB を超えた場合、サイズは P30 です。

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス

|Premium Storage ディスクの種類 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ディスク サイズ (切り上げ) | 32 GB | 64 GB | 128 GB | 512 GB | 1,024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| ディスクあたりの最大 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
ディスクあたりのスループット | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 150 MB/秒 | 200 MB/s | 250 MB/秒 | 250 MB/秒 |

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、Standard_GS5 VM では、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS の詳細については、[Linux VM のサイズ](sizes.md)に関するページを参照してください。

## <a name="create-and-attach-disks"></a>ディスクを作成して接続する

データ ディスクの作成および接続は、VM の作成時や、既存の VM に対して実行することができます。

### <a name="attach-disk-at-vm-creation"></a>VM の作成時にディスクを接続する

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

[az vm create](/cli/azure/vm#az-vm-create) コマンドを使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成し、*azureuser* という名前のユーザー アカウントを追加します。さらに、まだ SSH キーが存在しない場合は SSH キーを生成します。 `--datadisk-sizes-gb` 引数は、追加のディスクを作成してこの仮想マシンに接続するように指定するために使用します。 複数のディスクを作成して接続するには、ディスク サイズ値をスペースで区切ったリストを使用します。 次の例では、どちらも 128 GB のデータ ディスクを 2 つ備えた VM が作成されます。 ディスク サイズが 128 GB であるため、両方のディスクが P10 として構成され、ディスクあたり最大 500 IOPS を実現します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>既存の VM にディスクを接続する

新しいディスクを作成して既存の仮想マシンに接続するには、[az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) コマンドを使用します。 次の例では、サイズが 128 ギガバイトの Premium ディスクが作成され、最後の手順で作成した VM に接続されます。

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>データ ディスクを準備する

ディスクが仮想マシンに接続されたら、そのディスクを使用するようにオペレーティング システムを構成する必要があります。 次の例は、ディスクを手動で構成する方法を示しています。 この処理は、cloud-init を使用して自動化することもできます。詳細については、[後のチュートリアル](./tutorial-automate-vm-deployment.md)で説明します。

### <a name="manual-configuration"></a>手動構成

仮想マシンとの SSH 接続を作成します。 この例の IP アドレスは、仮想マシンのパブリック IP で置き換えてください。

```azurecli-interactive
ssh azureuser@52.174.34.95
```

`fdisk` を使用してディスクをパーティション分割します。

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

`mkfs` コマンドを使用してパーティションにファイル システムを書き込みます。

```bash
sudo mkfs -t ext4 /dev/sdc1
```

オペレーティング システムでアクセスできるように、新しいディスクをマウントします。

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

これで *datadrive* マウント ポイントを通じてディスクにアクセスできるようになりました。これは `df -h` コマンドを実行することで確認できます。

```bash
df -h
```

出力には、*/datadrive* にマウントされた新しいドライブが示されます。

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

再起動後にドライブが確実に再マウントされるように、そのドライブを */etc/fstab* ファイルに追加する必要があります。 これを行うには、`blkid` ユーティリティを使用してディスクの UUID を取得します。

```bash
sudo -i blkid
```

出力には、ドライブ (この例では `/dev/sdc1`) の UUID が表示されます。

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

次のような行を */etc/fstab* ファイルに追加します。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

ディスクが構成されたので、SSH セッションを閉じます。

```bash
exit
```

## <a name="resize-vm-disk"></a>VM のディスク サイズを変更する

VM をデプロイしたら、オペレーティング システム ディスクまたは接続されたデータ ディスクのサイズを増やすことができます。 ストレージの容量を増やすかパフォーマンスのレベル (P10、P20、P30 など) を高める必要がある場合は、ディスク サイズを大きくすると有益です。 ディスク サイズは縮小できません。

ディスク サイズを増やす前に、ディスクの ID または名前が必要です。 [az disk list](/cli/azure/disk#az-disk-list) コマンドを使用して、リソース グループ内のすべてのディスクを取得します。 サイズ変更するディスク名を書き留めます。

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

VM の割り当てを解除する必要があります。 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) コマンドを使用して、VM を停止し割り当てを解除します。

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

[az disk update](/cli/azure/vm/disk#az-vm-disk-update) コマンドを使用して、ディスクのサイズを変更します。 この例では、*myDataDisk* という名前のディスクのサイズを 1 TB に変更します。

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

サイズ変更操作が完了したら、VM を起動します。

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

オペレーティング システム ディスクのサイズを変更した場合、パーティションが自動的に拡張されます。 データ ディスクのサイズを変更した場合、VM のオペレーティング システムの現在のパーティションを拡張する必要があります。

## <a name="snapshot-azure-disks"></a>Azure ディスクのスナップショットを作成する

ディスクのスナップショットを作成すると、特定の時点のディスクに対する読み取り専用のコピーが作成されます。 Azure VM のスナップショットは、構成に変更を加える前に、VM の状態を簡単に保存するときに役立ちます。 構成の変更が望ましくないとわかった場合は、スナップショットを使用して VM の状態を復元できます。 VM に複数のディスクがある場合は、各ディスクのスナップショットが個別に作成されます。 アプリケーション整合性のあるバックアップを取得するには、ディスクのスナップショットを作成する前に、VM を停止することを検討してください。 または、[Azure Backup サービス](/azure/backup/)を使用して、VM の実行中に自動的にバックアップを実行できます。

### <a name="create-snapshot"></a>スナップショットの作成

仮想マシンのディスクのスナップショットを作成する前に、ディスクの ID または名前が必要です。 ディスク ID を取得するには、[az vm show](/cli/azure/vm#az-vm-show) コマンドを使用します。 この例では、ディスク ID を変数に格納して、後の手順で使用できるようにしています。

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

仮想マシンのディスク ID を取得したら、次のコマンドでディスクのスナップショットを作成します。

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>スナップショットからのディスクの作成

このスナップショットをディスクに変換すれば、それを使って仮想マシンを作成し直すことができます。

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>スナップショットからの仮想マシンの復元

実際に仮想マシンを復元してみましょう。既存の仮想マシンは削除します。

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

スナップショット ディスクから新しい仮想マシンを作成します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>データ ディスクの再接続

すべてのデータ ディスクを仮想マシンに再度接続する必要があります。

まず、[az disk list](/cli/azure/disk#az-disk-list) コマンドを使用して、データ ディスクの名前を見つけます。 この例では、このディスク名を *datadisk* という変数に格納しています。次の手順でこの変数を使用します。

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

ディスクを接続するには、[az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) コマンドを使用します。

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、VM ディスクについて、次のようなトピックを学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備
> * ディスクのサイズ変更
> * ディスクのスナップショット

次のチュートリアルに進み、VM 構成を自動化する方法について学習してください。

> [!div class="nextstepaction"]
> [VM 構成の自動化](./tutorial-automate-vm-deployment.md)
