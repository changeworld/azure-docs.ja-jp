---
title: "Azure CLI 2.0 (プレビュー) を使用して Linux VM をコピーする | Microsoft Azure"
description: "Azure CLI 2.0 (プレビュー) を使用して Resource Manager デプロイメント モデルで Azure Linux 仮想マシンのコピーを作成する方法を説明します"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) を使用して Linux 仮想マシンのコピーを作成する
この記事では、Resource Manager デプロイ モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを 作成する方法について説明します。 はじめにオペレーティング システムとデータ ディスクを新しいコンテナーにコピーし、次にネットワーク リソースをセットアップして、仮想マシンを作成します。

[カスタム ディスク イメージをアップロードして VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- Azure CLI 2.0 (プレビュー) - Resource Manager デプロイメント モデル用の次世代 CLI (この記事)

## <a name="prerequisites"></a>前提条件
- 最新の [Azure CLI 2.0 (プレビュー)](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。
- コピーを作成する際にソースとして使用する Azure VM が必要です。

## <a name="stop-the-vm"></a>VM を停止する
[az vm deallocate](/cli/azure/vm#deallocate) でソース VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>VM をコピーする
VM をコピーするには、基となる仮想ハード ディスクのコピーを作成します。 これにより、ソース VM と同じ構成と設定が含まれた特殊な VM を作成できます。 仮想ディスクをコピーする手順は Azure Managed Disks と非管理対象ディスクとで異なります。 管理ディスクは Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 管理ディスクは最上位のリソースであるため、操作が容易で、ディスク リソースを直接コピーできます。 Azure Managed Disks の詳細については、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」をご覧ください。 以下の手順のうち、ソース VM のストレージの種類に対応したものを実行してください。

- [管理ディスク](#managed-disks)
- [非管理対象ディスク](#unmanaged-disks) 

### <a name="managed-disks"></a>管理ディスク
[az vm list](/cli/azure/vm#list) で、各 VM とその OS 管理ディスクの名前を一覧表示できます。 次の例では、`myResourceGroup` という名前のリソース グループに含まれるすべての VM の一覧を取得します。

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

出力は次の例のようになります。

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

ディスクをコピーするために、[az disk create](/cli/azure/disk#create) を実行して新しい管理ディスクを作成します。 次の例では、`myDisk` という名前の管理ディスクから `myCopiedDisk` という名前のディスクを作成します。

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

[az disk list](/cli/azure/disk#list) でリソース グループ内に管理ディスクがあることを確認します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる管理ディスクの一覧を取得します。

```azurecli
az disk list --resource-group myResourceGroup --output table
```

[仮想ネットワーク設定の作成と確認](#set-up-the-virtual-network)に進んでください。


### <a name="unmanaged-disks"></a>非管理対象ディスク
VHD のコピーを作成するために、ストレージ アカウント キーとディスクの URI が必要です。 ストレージ アカウント キーを確認するには、[az storage account keys list](/cli/azure/storage/account/keys#list) を実行します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `mystorageaccount` という名前のストレージ アカウントのキーの一覧を取得します。

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --name mystorageaccount --output table
```

出力は次の例のようになります。

```azurecli
KeyName    Permissions    Value
---------  -------------  ----------------------------------------------------------------------------------------
key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
```

[az vm list](/cli/azure/vm#list) を実行して、VM とその URI の一覧を表示します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる VM の一覧を取得します。

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

出力は次の例のようになります。

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

[az storage blob copy start](/cli/azure/storage/blob/copy#start) を実行して VHD をコピーします。 **az storage account keys list** と **az vm list** で取得した情報に基づいて、必要なストレージ アカウント キーと VHD の URI を指定します。

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>仮想ネットワークをセットアップする
この手順では、新しい仮想ネットワーク、サブネット、パブリック IP アドレス、仮想ネットワーク インターフェイス カード (NIC) を作成します。 この手順は必須ではありません。 トラブルシューティングや追加デプロイの目的で VM をコピーする場合は、既存の仮想ネットワークで VM を使用しないほうがよいことがあります。 コピーする VM 用に仮想ネットワーク インフラストラクチャを作成する場合は以下の手順に従ってください。その必要がない場合は「[VM を作成する](#create-a-vm)」に進んでください。

[az network vnet create](/cli/azure/network/vnet#create) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成します。

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

[az network public-ip create](/cli/azure/network/public-ip#create) でパブリック IP を作成します。 次の例では、DNS 名が `mypublicdns` で `myPublicIP` という名前のパブリック IP を作成します。 (DNS 名は一意である必要があるため、独自の一意の名前を入力してください。)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

[az network nic create](/cli/azure/network/nic#create) で、ネットワーク インターフェイス カード (NIC) を作成します。 次の例では、`mySubnet` サブネットに接続する `myNic` という名前の NIC を作成します。

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>VM を作成します
これで、[az vm create](/cli/azure/vm#create) を実行して VM を作成できます。 ディスクをコピーする場合は、管理ディスクと非管理対象ディスクとでプロセスが若干異なります。 以下のコマンドのうち、いずれか適切なほうを使用して VM を作成してください。

### <a name="managed-disks"></a>管理ディスク
[az vm create](/cli/azure/vm#create) を実行して VM を作成します。 次のコマンドを使用して、コピーした管理ディスクを OS ディスク (`--attach-os-disk`) として使用するよう指定します。

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>非管理対象ディスク
[az vm create](/cli/azure/vm#create) を実行して VM を作成します。 次のコマンドを使用して、**az storage blob copy start** (`--image`) でコピーする際に使用したストレージ アカウント、コンテナー名、および VHD を指定します。

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>次のステップ
Azure CLI を使用して新しい仮想マシンを管理する方法については、 [Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。




<!--HONumber=Feb17_HO2-->


