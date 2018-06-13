---
title: 複数のサブネットを含む Azure 仮想ネットワークの作成 - Azure CLI | Microsoft Docs
description: Azure CLI を使用して、複数のサブネットを含む仮想ネットワークを作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880532"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Azure CLI を使用して複数のサブネットを含む仮想ネットワークを作成する

仮想ネットワークでは、複数の種類の Azure リソースが、インターネットと通信したり、プライベートに相互通信したりできます。 仮想ネットワークに複数のサブネットを作成すると、ネットワークをセグメント化して、サブネット間のトラフィックのフローをフィルター処理または制御できます。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * サブネットの作成
> * 仮想マシン間のネットワーク通信のテスト

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli.md)」を参照してください。 

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。 このコマンドでは、アドレス プレフィックスが *10.0.0.0/24* の、*Public* という名前の 1 つのサブネットを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

上記のコマンドでは場所が指定されていないため、仮想ネットワークは *myResourceGroup* リソース グループと同じ場所に作成されます。 **address-prefixes** と **subnet-prefix** は CIDR 表記で指定します。 指定したアドレス プレフィックスには、IP アドレス 10.0.0.0 ～ 10.0.255.254 が含まれます。 サブネットに指定するプレフィックスは、仮想ネットワーク用に定義されたアドレス プレフィックスの範囲内である必要があります。 Azure DHCP は、サブネットにデプロイされるリソースに、サブネットのアドレス プレフィックスから IP アドレスを割り当てます。 Azure は、**パブリック** サブネット内でデプロイされるリソースに、アドレス 10.0.0.4 ～ 10.0.0.254 のみを割り当てます。なぜなら、Azure は、各サブネット内の最初の 4 つのアドレス (この例のサブネットでは 10.0.0.0 ～ 10.0.0.3) と最後のアドレス (この例のサブネットでは 10.0.0.255) を予約するからです。

## <a name="create-a-subnet"></a>サブネットの作成

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) を使用してサブネットを作成します。 次の例では、*myVirtualNetwork* 仮想ネットワーク内に、アドレス プレフィックスが *10.0.1.0/24* の、*Private* という名前のサブネットを作成します。 このアドレス プレフィックスは、仮想ネットワーク用に定義されたアドレス プレフィックスの範囲内である必要があり、仮想ネットワークのその他のサブネットのアドレス プレフィックスと重複することはできません。

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

運用で使用するために Azure 仮想ネットワークとサブネットをデプロイする前に、アドレス空間に関する[考慮事項](manage-virtual-network.md#create-a-virtual-network)や[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に十分精通しておくことをお勧めします。 リソースがサブネットにデプロイされた後に、仮想ネットワークやサブネットの変更 (アドレス範囲の変更など) を行うと、サブネットにデプロイされている既存の Azure リソースを再デプロイする必要が生じる場合があります。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

仮想ネットワークでは、インターネットとの通信、および仮想ネットワーク間のプライベート通信のために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 仮想ネットワークに 2 つの仮想マシンを作成して、後の手順でそれらの間のネットワーク通信と、インターネットとのネットワーク通信を検証できるようにします。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して仮想マシンを作成します。 次の例では、*Public* サブネットに *myVmWeb* という名前の仮想マシンを作成します。 `--no-wait` パラメーターを使用すると、Azure はバックグラウンドでコマンドを実行できるので、次のコマンドに進むことができます。 このチュートリアルを効率化するために、パスワードを使用します。 通常、キーは運用環境デプロイで使用されます。 キーを使用する場合は、残りの手順を実行するために、SSH エージェント転送も構成する必要があります。 SSH エージェント転送の詳細については、お使いの SSH クライアントのドキュメントをご覧ください。 次のコマンドの `<replace-with-your-password>` を、使用するパスワードに置き換えます。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

10.0.0.4 が *Public* サブネットで使用できる最初の IP アドレスであるため、仮想マシンのプライベート IP アドレスとして 10.0.0.4 が自動的に割り当てられます。 

*プライベート* サブネットに仮想マシンを作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
仮想マシンの作成には、数分かかります。 仮想マシンが作成された後、Azure CLI は次の例のような出力を返します。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

この出力例では、**privateIpAddress** が *10.0.1.4* であることがわかります。 Azure は、[ネットワーク インターフェイス](virtual-network-network-interface.md)を作成し、それを仮想マシンに接続して、ネットワーク インターフェイスにプライベート IP アドレスと  **macAddress** を割り当てました。 10.0.1.4 が *Private* サブネットで使用できる最初の IP アドレスであるため、Azure DHCP はネットワーク インターフェイスにこのアドレスを自動的に割り当てました。 プライベート IP アドレスと MAC アドレスは、ネットワーク インターフェイスが削除されるまで、ネットワーク インターフェイスに割り当てられたままになります。 

**publicIpAddress** を書き留めておきます。 このアドレスは、以降の手順で、インターネットから仮想マシンにアクセスするときに使用されます。 仮想マシンへのパブリック IP アドレスの割り当ては必須ではありませんが、Azure の既定では、作成したすべての仮想マシンにパブリック IP アドレスが割り当てられます。 インターネットから仮想マシンに通信するには、仮想マシンにパブリック IP アドレスが割り当てられている必要があります。 仮想マシンにパブリック IP アドレスが割り当てられているかどうかにかかわらず、すべての仮想マシンがインターネットとの送信方向の通信を行うことができます。 インターネットへの送信接続の詳細については、「[Azure の送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

この記事で作成した仮想マシンは、1 つの[ネットワーク インターフェイス](virtual-network-network-interface.md)を備えています。このネットワーク インターフェイスには、1 つの IP アドレスが動的に割り当てられます。 VM をデプロイした後に、[複数のパブリックおよびプライベート IP アドレスを追加したり、IP アドレスの割り当て方法を静的に変更](virtual-network-network-interface-addresses.md#add-ip-addresses)したりできます。 仮想マシンの作成時に選択した [VM サイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)でサポートされている最大数まで、[ネットワーク インターフェイスを追加](virtual-network-network-interface-vm.md#vm-add-nic)できます。 また、VM に対して[シングル ルート I/O 仮想化 (SR-IOV)](create-vm-accelerated-networking-cli.md) を有効にすることもできます (ただし、作成した VM の VM サイズがこの機能に対応している場合に限ります)。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>仮想マシン間およびインターネットとの通信を行う

次のコマンドを使用して、*myVmMgmt* 仮想マシンとの SSH セッションを作成します。 `<publicIpAddress>` をお使いの仮想マシンのパブリック IP アドレスに置き換えます。 前の例では、パブリック IP アドレスは *13.90.242.231* です。 パスワードの入力を求められたら、「[仮想マシンを作成する](#create-virtual-machines)」で入力したパスワードを入力します。

```bash 
ssh azureuser@<publicIpAddress>
```

セキュリティ上の理由により、仮想ネットワークにリモートで接続できる仮想マシンの数を制限するのが一般的です。 このチュートリアルでは、*myVmMgmt* 仮想マシンは仮想ネットワークで *myVmWeb* 仮想マシンを管理するために使用されます。 次のコマンドを使用して、*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンに SSH 接続します。

```bash 
ssh azureuser@myVmWeb
```

*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンに通信するには、コマンド プロンプトで次のコマンドを入力します。

```
ping -c 4 myvmmgmt
```

次の出力例のような出力が返されます。
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
*myVmMgmt* 仮想マシンのアドレスは 10.0.1.4 であることがわかります。 10.0.1.4 は、前の手順で *myVmMgmt* 仮想マシンをデプロイした *Private* サブネットのアドレス範囲内にある、使用可能な 最初の IP アドレスでした。  この仮想マシンの完全修飾ドメイン名は、*myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net* になっています。 ドメイン名の *hxehizax3z1udjnrx1r4gr30pg* の部分は仮想マシンと異なりますが、ドメイン名の残りの部分は同じです。 既定では、すべての Azure 仮想マシンが既定の Azure DNS サービスを使用します。 仮想ネットワーク内のすべての仮想マシンは、Azure の既定の DNS サービスを使用して、同じ仮想ネットワーク内にある他のすべての仮想マシンの名前を解決できます。 Azure の既定の DNS サービスを使用する代わりに、独自の DNS サーバーや、Azure DNS サービスのプライベート ドメイン機能を使用することもできます。 詳細については、「[独自の DNS サーバーを使用する名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」または「[プライベート ドメインに Azure DNS を使用する](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

次のコマンドを使用して、*myVmWeb* 仮想マシンに nginx Web サーバーをインストールします。

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

nginx がインストールされたら、*myVmWeb* の SSH セッションを閉じ、*myVmMgmt* 仮想マシンのプロンプトに戻ります。 次のコマンドを入力して、*myVmWeb* 仮想マシンから nginx のようこそ画面を取得します。

```bash
curl myVmWeb
```

nginx のようこそ画面が返されます。

*myVmMgmt* 仮想マシンとの SSH セッションを閉じます。

*myVmWeb* 仮想マシンの作成時に、*myVmWebPublicIP* という名前のパブリック IP アドレスも作成され、仮想マシンに割り当てられています。 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) を使用して、割り当てられているパブリック アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

お使いのコンピューターから、次のコマンドを入力します。`<publicIpAddress>` は、前のコマンドから返されたアドレスに置き換えます。

```bash
curl <publicIpAddress>
```

お使いのコンピューターから nginx のようこそ画面を取得しようとすると失敗します。 この試みが失敗するのは、仮想マシンをデプロイしたときに、既定で仮想マシンごとにネットワーク セキュリティ グループが作成されたためです。 

ネットワーク セキュリティ グループには、ポートと IP アドレスによって受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則が含まれています。 Azure が作成した既定のネットワーク セキュリティ グループでは、同じ仮想ネットワーク内のリソース間の、すべてのポート経由の通信が許可されます。 Linux 仮想マシンの場合、既定のネットワーク セキュリティ グループでは、TCP ポート 22 (SSH) を除くすべてのポート経由の、インターネットからのすべての受信トラフィックが拒否されます。 そのため、既定では、Web サーバーに対してポート 22 を開くことが望ましくない場合でも、インターネットから *myVmWeb* 仮想マシンへの SSH セッションを直接作成することもできます。 `curl` コマンドはポート 80 経由で通信するので、インターネットからの通信は失敗します。既定のネットワーク セキュリティ グループには、ポート 80 経由のトラフィックを許可する規則がないためです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、複数のサブネットを含んだ仮想ネットワークをデプロイする方法を学習しました。 また、Linux 仮想マシンを作成すると、その仮想マシンに接続するネットワーク インターフェイスが作成され、インターネットからの、ポート 22 経由のトラフィックのみを許可するネットワーク セキュリティ グループが作成されることも説明しました。 次のチュートリアルに進み、個々の仮想マシンではなく、サブネットへのネットワーク トラフィックをフィルター処理する方法を確認してください。

> [!div class="nextstepaction"]
> [サブネットへのネットワーク トラフィックをフィルター処理する](./virtual-networks-create-nsg-arm-cli.md)
