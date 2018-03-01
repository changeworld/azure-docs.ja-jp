---
title: "仮想ネットワークの作成 - Azure CLI | Microsoft Docs"
description: "Azure CLI を使用した仮想ネットワークの作成について簡単に説明します。 仮想ネットワークでは、プライベートで相互通信するために、たくさんの種類の Azure リソースを使用できます。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2cb32ddc67060d9860d172b90cc399622c52b04b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Azure CLI を使用した仮想ネットワークの作成

この記事では、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成した後は、仮想ネットワークに 2 つの仮想マシンをデプロイし、それらのマシン間でプライベート通信を行います。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 Azure リソースはすべて、Azure の場所 (リージョン) 内に作成されます。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) コマンドを使って、仮想ネットワークを作成します。 次の例では、*既定* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。 場所が指定されていないため、Azure はリソース グループと同じ場所に仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

仮想ネットワークが作成された後、返される情報の一部を次に示します。

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

すべての仮想ネットワークには、1 つまたは複数のアドレス プレフィックスが割り当てられています。 仮想ネットワークを作成するときに、アドレス プレフィックスが指定されなかったので、Azure は既定で、10.0.0.0/16 アドレス空間を定義しました。 アドレス範囲は、CIDR 表記で指定されます。 アドレス空間 10.0.0.0/16 は、10.0.0.0 ～ 10.0.255.254 を含みます。

返された情報の別の箇所では、コマンドに指定された*既定*のサブネットの **addressPrefix** は *10.0.0.0/24* になっています。 仮想ネットワークは、0 個または 1 つ以上のサブネットを保持します。 コマンドによって *既定*という名前の単一のサブネットが作成されましたが、サブネットのアドレス プレフィックスは指定されませんでした。 仮想ネットワークまたはサブネットのアドレス プレフィックスが指定されていない場合、Azure では既定で、最初のサブネットのアドレス プレフィックスとして 10.0.0.0/24 を定義します。 結果として、サブネットは 10.0.0.0 ～ 10.0.0.254 を含みますが、Azure では各サブネットで最初の 4 つのアドレス (0 ～ 3) と最後のアドレスを予約しているため、10.0.0.4 ～ 10.0.0.254 のみが使用可能です。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワークでは、プライベートで相互通信するために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 以降の手順では、仮想ネットワークにある仮想マシン間の通信の仕組みについて検証して理解できるように、仮想ネットワークに 2 つの仮想マシンを作成します。

仮想マシンを作成するには、[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用します。 次の例では、*myVm1* という名前の仮想マシンを作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 `--no-wait` オプションを使用すると、仮想マシンはバックグラウンドで作成されるため、次の手順に進むことができます。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

仮想ネットワークがリソース グループ内に存在し、コマンドでは仮想ネットワークやサブネットが指定されていないため、Azure では自動的に、*myVirtualNetwork* 仮想ネットワークの*既定*のサブネットに仮想マシンを作成します。 *既定*のサブネットで使用できる最初のアドレスは 10.0.0.4 なので、Azure DHCP はこれを仮想マシンに自動的に割り当てました。 仮想マシンを作成する場所は、仮想ネットワークが存在する場所と同じ場所にする必要があります。 この記事では同じになっていますが、仮想マシンは、該当の仮想ネットワークと同じリソース グループ内に配置する必要はありません。

2 番目の仮想マシンを作成します。 また、既定では、Azure は*既定*のサブネットにこの仮想マシンを作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

仮想マシンの作成には、数分かかります。 仮想マシンが作成された後、Azure CLI は次の例のような出力を返します。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

例では、**privateIpAddress** は *10.0.0.5* であることがわかります。 *既定*のサブネットで使用できる次のアドレスは *10.0.0.5* なので、Azure DHCP はこれを仮想マシンに自動的に割り当てました。 **publicIpAddress** を書き留めておきます。 このアドレスは、以降の手順で、インターネットから仮想マシンにアクセスするときに使用されます。 パブリック IP アドレスは、仮想ネットワークまたはサブネットのアドレス プレフィックス内からは割り当てられません。 パブリック IP アドレスは、[各 Azure リージョンに割り当てられたアドレスのプール](https://www.microsoft.com/download/details.aspx?id=41653)から割り当てられます。 Azure ではどのパブリック IP アドレスが仮想マシンに割り当てられているかを把握していますが、仮想マシンで実行されているオペレーティング システムが、割り当てられたパブリック IP アドレスを認識することはありません。

## <a name="connect-to-a-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、 *myVm2* 仮想マシンとの SSH セッションを作成します。 `<publicIpAddress>` をお使いの仮想マシンのパブリック IP アドレスに置き換えます。 上記の例では、IP アドレスは *40.68.254.142* です。

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>通信を検証する

次のコマンドを使用して、*myVm2* から *myVm1* との通信を確認します。

```bash
ping myVm1 -c 4
```

*10.0.0.4* から 4 つの応答を受信します。 どちらの仮想マシンも*既定*のサブネットから割り当てられたプライベート IP アドレスを保持しているため、*myVm2* から *myVm1* と通信できます。 Azure は仮想ネットワーク内のすべてのホストに DNS 名前解決を自動的に提供するので、ホスト名で ping できます。

以下のコマンドを入力して、インターネットへの送信方向の通信を確認します。

```bash
ping bing.com -c 4
```

bing.com から 4 つの応答を受信します。既定では、仮想ネットワークにあるどの仮想マシンも、インターネットへの送信方向の通信が可能です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループと含まれているすべてのリソースを削除できます。 VM への SSH セッションを終了し、リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、1 つのサブネットと 2 つの仮想マシンを含む既定の仮想ネットワークをデプロイしました。 複数のサブネットでカスタム仮想ネットワークを作成し、基本の管理タスクを実行する方法を習得するには、カスタム仮想ネットワークの作成と管理に関するチュートリアルに進んでください。


> [!div class="nextstepaction"]
> [カスタム仮想ネットワークの作成および管理](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
