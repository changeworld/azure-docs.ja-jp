---
title: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する - Azure CLI | Microsoft Docs
description: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する方法。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Azure CLI を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 仮想ネットワークをピアリングすると、それぞれの仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような待ち時間と帯域幅で相互に通信できます。 この記事では、2 つの仮想ネットワークの作成とピアリングについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 2 つの仮想ネットワークを作成する
> * 仮想ネットワーク間のピアリングを作成する
> * ピアリングをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork1* という名前の仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

アドレス プレフィックスが *10.1.0.0/16* の、*myVirtualNetwork2* という名前の仮想ネットワークを作成します。 アドレス プレフィックスは、*myVirtualNetwork1* 仮想ネットワークのアドレス プレフィックスとは重複しません。 アドレス プレフィックスが重複する仮想ネットワークをピアリングすることはできません。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>仮想ネットワークをピアリングする

ピアリングは仮想ネットワーク ID 間で確立されるため、最初に [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) を使用して各仮想ネットワーク の ID を取得し、その ID を変数に格納する必要があります。

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

[az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) を使用して、*myVirtualNetwork1* から *myVirtualNetwork2* へのピアリングを作成します。 `--allow-vnet-access` パラメーターが指定されていない場合、ピアリングは確立されますが、通信を行うことはできません。

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

前のコマンドの実行後に返された出力では、**peeringState** が *Initiated* です。 このピアリングは、*myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成するまで *Initiated* 状態のままです。 *myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成します。 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

前のコマンドの実行後に返された出力では、**peeringState** が *Connected* です。 Azure によって、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態も *Connected* に変更されました。 [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) を使用して、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態が *Connected* に変更されたことを確認します。

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

両方の仮想ネットワークのピアリングの **peeringState** が *Connected* になるまで、一方の仮想ネットワークのリソースともう一方の仮想ネットワークのリソースは通信できません。 

ピアリングは 2 つの仮想ネットワークの間で行われますが、推移的ではありません。 このため、たとえば *myVirtualNetwork2* と *myVirtualNetwork3* をピアリングしたい場合は、仮想ネットワーク *myVirtualNetwork2* と *myVirtualNetwork3* の間に追加ピアリングを作成する必要があります。 *myVirtualNetwork1* が *myVirtualNetwork2* とピアリングされていても、*myVirtualNetwork1* が *myVirtualNetwork3* ともピアリングされている場合は、*myVirtualNetwork1* のリソースがアクセスできるのは *myVirtualNetwork3* のリソースだけです。 

運用仮想ネットワークをピアリングする前に、[ピアリング概要](virtual-network-peering-overview.md)、[ピアリングの管理](virtual-network-manage-peering.md)、および[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)について十分に理解しておくことをお勧めします。 この記事は、同じサブスクリプションや同じ場所にある 2 つの仮想ネットワーク間のピアリングについて説明していますが、[異なるリージョン](#register)や[異なる Azure サブスクリプション](create-peering-different-subscriptions.md#cli)の仮想ネットワークをピアリングすることもできます。 [ハブとスポークのネットワーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)をピアリングで作成することもできます。

## <a name="test-peering"></a>ピアリングをテストする

異なる仮想ネットワーク内にある仮想マシン間のピアリングを使用したネットワーク通信をテストするには、仮想マシンを各サブネットにデプロイし、仮想マシン間で通信します。 

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して仮想マシンを作成します。 次の例では、*myVirtualNetwork1* 仮想ネットワークで *myVm1* という名前の仮想マシンを作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 `--no-wait` オプションを使用すると、仮想マシンはバックグラウンドで作成されるため、次の手順に進むことができます。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

*myVirtualNetwork1* の *Subnet1* で使用できる最初の IP アドレスが 10.0.0.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.0.4 が自動的に割り当てられます。 

*myVirtualNetwork2* 仮想ネットワークに仮想マシンを作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

仮想マシンの作成には、数分かかります。 仮想マシンが作成された後、Azure CLI は次の例のような情報を表示します。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

この出力例では、**privateIpAddress** が *10.1.0.4* であることがわかります。 *myVirtualNetwork2* の *Subnet1* で使用できる最初のアドレスが 10.1.0.4 であるため、Azure DHCP では 10.1.0.4 が仮想マシンに自動的に割り当てられます。 **publicIpAddress** を書き留めておきます。 このアドレスは、以降の手順で、インターネットから仮想マシンにアクセスするときに使用されます。

### <a name="test-virtual-machine-communication"></a>仮想マシンの通信をテストする

次のコマンドを使用して、 *myVm2* 仮想マシンとの SSH セッションを作成します。 `<publicIpAddress>` をお使いの仮想マシンのパブリック IP アドレスに置き換えます。 前の例では、パブリック IP アドレスは *13.90.242.231* です。

```bash 
ssh <publicIpAddress>
```

*myVirtualNetwork1* で仮想マシンに ping を実行します。

```bash 
ping 10.0.0.4 -c 4
```

4 つの応答を受信します。 仮想マシンの IP アドレスではなく名前 (*myVm1*) で ping を実行すると、ping に失敗します。これは *myVm1* が不明なホスト名であるためです。 Azure の既定の名前解決は、同じ仮想ネットワークの仮想マシン間で機能しますが、異なる仮想ネットワークの仮想マシン間では機能しません。 仮想ネットワークの枠を越えて名前を解決するには、[独自の DNS サーバーをデプロイ](virtual-networks-name-resolution-for-vms-and-role-instances.md)するか、[Azure DNS プライベート ドメイン](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用する必要があります。

*myVm2* 仮想マシンへの SSH セッションを閉じます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>グローバル仮想ネットワーク ピアリング プレビューへの登録**

同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。 異なるリージョン内の複数の仮想ネットワークのピアリングは、現在プレビュー段階です。 使用可能なリージョンについては、[仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)を参照してください。 リージョンの枠を越えて仮想ネットワークをピアリングするには、まず (ピアリングする各仮想ネットワークがあるサブスクリプション内で) 次の手順を実行して、プレビューに登録する必要があります。

1. 次のコマンドを入力して、プレビューに登録します。

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. 次のコマンドを入力して、プレビューに登録されていることを確認します。

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  上記のコマンドを入力した後に受信する **RegistrationState** 出力が、両方のサブスクリプションに対して **Registered** になる前に、異なるリージョンの複数の仮想ネットワークをピアリングしようとすると、ピアリングは失敗します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク ピアリングを使用して 2 つのネットワークを接続する方法を学習しました。 [ユーザーのコンピューターを VPN 経由で仮想ネットワークに接続](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)し、仮想ネットワーク、またはピアリングされた仮想ネットワークのリソースを操作できます。

再利用可能なスクリプトのスクリプト サンプルに進み、仮想ネットワークの記事で説明する多くのタスクを完了します。

> [!div class="nextstepaction"]
> [仮想ネットワークのサンプル スクリプト](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
