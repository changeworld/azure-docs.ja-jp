---
title: "Azure VM をスケール セットに変換する | Microsoft Docs"
description: "Azure CLI を使用して Linux Azure 仮想マシン スケール セットを作成およびデプロイします。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>既存の Azure 仮想マシンをスケール セットに変換する

このチュートリアルでは、Azure CLI 2.0 を使用して仮想マシンを仮想マシン スケール セットに変換する方法を示します。 また、スケール セット内の仮想マシンの構成を自動化する方法についても説明します。 Azure CLI 2.0 について詳しくは、「[Getting Started with Azure CLI 2.0 (Azure CLI 2.0 の概要)](/cli/azure/get-started-with-azure-cli.md)」をご覧ください。 スケール セットの詳細については、「[仮想マシン スケール セットの概要](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)」を参照してください。

## <a name="step-1---deprovision-the-vm"></a>手順 1 - VM のプロビジョニングを解除する

SSH を使用して VM に接続します。

Azure VM エージェントを使用している VM のプロビジョニングを解除し、ファイルとデータを削除します。 プロビジョニング解除について詳しくは、[Linux 仮想マシンのキャプチャ](capture-image.md)に関する記事をご覧ください。

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>手順 2 - VM のイメージをキャプチャする

キャプチャについて詳しくは、[Linux 仮想マシンのキャプチャ](capture-image.md)に関する記事をご覧ください。

[az vm deallocate](/cli/azure/vm#deallocate) で VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm generalize](/cli/azure/vm#generalize) で VM を一般化します。

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

[az image create](/cli/azure/image#create) で VM リソースからイメージを作成します。

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>手順 3 - スケール セットを作成する

イメージの **id** を取得します。

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

[az vmss create](/cli/azure/vmss#create) でイメージ リソースから VM を作成します。

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

このコマンドは、10 gb のデータ ディスクにも接続します。 選択した VM のサイズによって許可されるデータ ディスクの数が異なることに注意してください (ここでは **Standard_DS1_v2** を使用)。 詳しくは、[仮想マシンのサイズ](sizes.md)に関する記事をご覧ください。

スケール セットが完了したら、それに接続します。 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) を使用して SSH のインスタンスの IP アドレスの一覧を取得します。

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

これで仮想マシンに接続して、データ ディスクを初期化できるようになりました。

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>手順 4 - データ ディスクを初期化する

仮想マシンに接続中に、`fdisk` を使用してディスクを分割します。

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

`mkfs` コマンドを使用してパーティションにファイル システムを書き込みます。

```bash
sudo mkfs -t ext4 /dev/sdc1
```

オペレーティング システムでアクセスできるように、新しいディスクをマウントします。

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

これで datadrive マウント ポイントを通じてディスクにアクセスできるようになりました。`ls /datadrive/` で確認できます。

SSH セッションを終了します。


## <a name="step-5---configure-firewall"></a>手順 5 - ファイアウォールを構成する

ファイアウォールを通じて、スケール セットでホストされている Web サーバーに穴を開けます。 スケール セットを作成したときにロード バランサーも作成され、個々の仮想マシンにその **SSH** を使用します。 ポートを開くには、2 つの情報が必要です。Azure CLI を使用して取得できます。

* **フロントエンド IP アドレス プール**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **バックエンド IP アドレス プール**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

これら 2 つの名前により、ポート **80** を開くことができます。

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>手順 6 – 構成を自動化する

データ ディスクは、各仮想マシン インスタンスで構成する必要があります。 **CustomScript** 拡張機能を使用して仮想マシンの構成を自動化できます。

まず、ディスク形式のコマンドを含む *.sh* スクリプトを作成します。

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

次に、**CustomScript** 拡張機能がアクセスできる場所にそのスクリプト ファイルをアップロードします。 コピーは[こちら](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4)で入手できます。

**settings.json** という名前のローカル ファイルを作成し、次の JSON ブロックを配置します。 `flieUris` プロパティは、スクリプト ファイルがアップロードされた場所に設定する必要があります。

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

このコマンドを先ほど作成した **settings.json** を参照する **CustomScript** 拡張機能とともにスケール セットにデプロイします。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

この拡張機能は現在のすべてのインスタンス、およびスケーリングにより後で作成されるすべてのインスタンスで自動的に実行されます。

## <a name="step-7---configure-autoscale-rules"></a>手順 7 - 自動スケール ルールを構成する

現時点では、自動スケール ルールは Azure CLI では設定できません。 自動スケールを構成するには [Azure Portal](https://portal.azure.com) を使用します。

## <a name="step-8---management-tasks"></a>手順 8 - 管理タスク

スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、さまざまなライフ サイクル タスクを自動化するスクリプトを作成する必要がある場合に、Azure CLI はこれらのタスクを簡単に実行できる方法を提供します。 一般的なタスクには、次のようなものがあります。

### <a name="get-connection-info"></a>接続情報を取得する

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>インスタンス数を設定する (手動でのスケーリング)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Delete resource group

リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ
このチュートリアルで紹介した仮想マシン スケール セットのいくつかの機能について詳しくは、次の情報をご覧ください。

- [Azure 仮想マシン スケール セットの概要](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure Load Balancer の概要](../../load-balancer/load-balancer-overview.md)
- [ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御](../../virtual-network/virtual-networks-nsg.md)
