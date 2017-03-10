---
title: "Azure CLI 2.0 を使用した Linux VM のコピー | Microsoft Docs"
description: "Azure CLI 2.0 (プレビュー) を使用して Resource Manager デプロイメント モデルで Azure Linux VM のコピーを作成する方法を説明します。"
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
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) を使用した Linux VM のコピーの作成
この記事では、Azure Resource Manager デプロイメント モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを作成する方法について説明します。

オペレーティング システム ディスクとデータ ディスクを新しいコンテナーにコピーし、次にネットワーク リソースを設定して、VM を作成します。

[カスタム ディスク イメージをアップロードして VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="cli-versions"></a>CLI のバージョン
このタスクは、次のいずれかのコマンドライン インターフェイス (CLI) バージョンを使用して完了できます。

* [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用。
* Azure CLI 2.0 (プレビュー): Resource Manager デプロイメント モデル用の次世代 CLI (この記事で説明)。

## <a name="prerequisites"></a>前提条件
* [Azure CLI 2.0 (プレビュー)](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにサインインしている。
* コピーのソースとして使用する Azure VM。

## <a name="step-1-stop-the-source-vm"></a>手順 1: ソース VM を停止する
[az vm deallocate](/cli/azure/vm#deallocate) を使用して、ソース VM の割り当てを解除します。 次の例では、リソース グループ `myResourceGroup` に含まれる VM `myVM` の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>手順 2: ソース VM をコピーする
VM をコピーするには、基となる仮想ハード ディスクのコピーを作成します。 この手順では、ソース VM と同じ構成と設定が含まれた特殊な VM を作成します。

仮想ディスクをコピーする手順は Azure Managed Disks と非管理対象ディスクで異なります。 管理ディスクは Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 管理ディスクは最上位のリソースであるため、操作が容易で、 ディスク リソースを直接コピーできます。

Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。

ソース VM のストレージの種類に応じて、次の 2 つの手順のどちらかを実行した後、「手順 3: 仮想ネットワークをセットアップする」に進んでください。

### <a name="managed-disks"></a>管理ディスク

1. [az vm list](/cli/azure/vm#list) で、各 VM とその OS 管理ディスクの名前を一覧表示します。 次の例では、リソース グループ `myResourceGroup` に含まれるすべての VM の一覧を取得します。

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    出力は次の例のようになります。

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. ディスクをコピーするために、[az disk create](/cli/azure/disk#create) を実行して新しい管理ディスクを作成します。 次の例では、管理ディスク `myDisk` からディスク `myCopiedDisk` を作成します。

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. [az disk list](/cli/azure/disk#list) を使用して、リソース グループ内に管理ディスクがあることを確認します。 次の例では、リソース グループ `myResourceGroup` に含まれる管理ディスクの一覧を取得します。

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. 「[手順 3: 仮想ネットワークをセットアップする](#set-up-the-virtual-network)」に進みます。


### <a name="unmanaged-disks"></a>非管理対象ディスク

1. 仮想ハード ディスクのコピーを作成するには、Azure ストレージ アカウント キーとディスクの URI が必要です。 ストレージ アカウント キーを確認するには、[az storage account keys list](/cli/azure/storage/account/keys#list) を使います。

 次の例では、リソース グループ `myResourceGroup` 内にあるストレージ アカウント `mystorageaccount` のキーの一覧を取得します。

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

2. VM とその URI の一覧を表示するには、[az vm list](/cli/azure/vm#list) を使用します。 次の例では、リソース グループ `myResourceGroup` に含まれる VM の一覧を取得します。

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    出力は次の例のようになります。

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. [az storage blob copy start](/cli/azure/storage/blob/copy#start) を使用して、仮想ハード ディスクをコピーします。 必要なストレージ アカウント キーと仮想ハード ディスクの URI を指定するには、`az storage account keys` と `az vm` で取得した一覧の情報を使用します。

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>手順 3: 仮想ネットワークをセットアップする
省略可能な次の手順では、新しい仮想ネットワーク、サブネット、パブリック IP アドレス、仮想ネットワーク インターフェイス カード (NIC) を作成します。

トラブルシューティングまたは追加デプロイの目的で VM をコピーする場合は、既存の仮想ネットワークで VM を使用しないほうがよいことがあります。

コピーする VM のために仮想ネットワーク インフラストラクチャを作成する場合、次の手順に従ってください。 仮想ネットワークを作成しない場合は、「[手順 4: VM を作成する](#create-a-vm)」に進んでください。

1. [az network vnet create](/cli/azure/network/vnet#create) を使用して、仮想ネットワークを作成します。 次の例では、仮想ネットワーク `myVnet` とサブネット `mySubnet` を作成します。

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. [az network public-ip create](/cli/azure/network/public-ip#create) を使用して、パブリック IP を作成します。 次の例では、DNS 名が `mypublicdns` のパブリック IP `myPublicIP` を作成します  (DNS 名は一意である必要があるため、一意の名前を入力してください)。

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. [az network nic create](/cli/azure/network/nic#create) を使用して、NIC を作成します。 次の例では、`mySubnet` サブネットに接続される NIC `myNic` を作成します。

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>手順 4: VM を作成する
これで、[az vm create](/cli/azure/vm#create) を使用して VM を作成できます。 ディスクをコピーする場合は、管理ディスクと非管理対象ディスクとでプロセスが若干異なります。 ソース VM のストレージの種類に応じて、次の&2; つの手順のいずれかを実行してください。

### <a name="managed-disks"></a>管理ディスク
1. [az vm create](/cli/azure/vm#create) を使用して VM を作成します。
2. 次のコマンドを使用して、コピーした管理ディスクを OS ディスク (`--attach-os-disk`) として使用するよう指定します。

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>非管理対象ディスク
1. [az vm create](/cli/azure/vm#create) を実行して VM を作成します。
2. 次のコマンドを使用して、`az storage blob copy start` (`--image`) でコピーしたディスクを作成するときに使用したストレージ アカウント、コンテナー名、仮想ハード ディスクを指定します。

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>次のステップ
Azure CLI を使用して新しい VM を管理する方法については、[Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。

