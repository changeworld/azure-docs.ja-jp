---
title: ネットワーク トラフィックをルーティングする - Azure CLI | Microsoft Docs
description: Azure CLI を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Azure CLI を使用してルート テーブルでネットワーク トラフィックをルーティングする

Azure は、既定では、トラフィックを仮想ネットワーク内のすべてのサブネット間で自動的にルーティングします。 独自のルートを作成すると、Azure の既定のルーティングを上書きできます。 カスタム ルートを作成する機能は、たとえば、サブネット間のトラフィックをファイアウォール経由でルーティングする場合に役立ちます。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * ルート テーブルの作成
> * ルートの作成
> * ルート テーブルの仮想ネットワーク サブネットへの関連付け
> * ルーティングのテスト
> * ルーティングのトラブルシューティング

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このクイック スタートでは、Azure CLI バージョン 2.0.28 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-route-table"></a>ルート テーブルの作成

Azure は、既定では、トラフィックを仮想ネットワーク内のすべてのサブネット間でルーティングします。 Azure の既定のルートの詳細については、「[システム ルート](virtual-networks-udr-overview.md)」を参照してください。 Azure の既定のルーティングを上書きするには、ルートを含むルート テーブルを作成し、そのルート テーブルを仮想ネットワーク サブネットに関連付けます。

ルート テーブルを作成するには、この記事で作成されるすべてのリソースのリソース グループを [az group create](/cli/azure/group#az_group_create) で作成しておきます。 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

[az network route-table create](/cli/azure/network/route#az_network_route_table_create) でルート テーブルを作成します。 次の例では、*myRouteTablePublic* という名前のルート テーブルを作成します。 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>ルートの作成

ルート テーブルには、0 個以上のルートが含まれます。 [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) でルート テーブル内にルートを作成します。 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

このルートは、10.0.1.0/24 アドレス プレフィックス宛てのすべてのトラフィックを、IP アドレス 10.0.2.4 を持つネットワーク仮想アプライアンス経由で転送します。 指定されたアドレス プレフィックスを持つネットワーク仮想アプライアンスとサブネットは、後の手順で作成されます。 このルートは、トラフィックをサブネット間で直接ルーティングする Azure の既定のルーティングを上書きします。 各ルートが次ホップの種類を指定します。 次ホップの種類は、Azure にトラフィックのルーティング方法を指示します。 この例では、次ホップの種類は *VirtualAppliance* です。 Azure で使用可能なすべての次ホップの種類、およびそれらをいつ使用するかの詳細については、[次ホップの種類](virtual-networks-udr-overview.md#custom-routes)に関するページを参照してください。

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付けるには、仮想ネットワークとサブネットを作成しておく必要があります。 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) で、1 つのサブネットを含む仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) で 2 つの追加のサブネットを作成します。

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

1 つのルート テーブルを 0 個以上のサブネットに関連付けることができます。 サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。 サブネットからの送信トラフィックは、Azure の既定のルートと、サブネットに関連付けるルート テーブルに追加したすべてのカスタム ルートに基づいてルーティングされます。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) で、*myRouteTablePublic* ルート テーブルを*パブリック* サブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

運用で使用するためにルート テーブルをデプロイする前に、[Azure でのルーティング](virtual-networks-udr-overview.md)と [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に十分精通しておくことをお勧めします。

## <a name="test-routing"></a>ルーティングのテスト

ルーティングをテストするには、前の手順で作成したルートが経由するネットワーク仮想アプライアンスとして機能する仮想マシンを作成します。 ネットワーク仮想アプライアンスを作成した後、*パブリック*および*プライベート* サブネットに仮想マシンをデプロイします。 次に、そのネットワーク仮想アプライアンス経由で*パブリック* サブネットから*プライベート* サブネットにトラフィックをルーティングします。

### <a name="create-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンスを作成する

前の手順では、次ホップの種類としてネットワーク仮想アプライアンスを指定したルートを作成しました。 ネットワーク アプリケーションを実行する仮想マシンは多くの場合、ネットワーク仮想アプライアンスと呼ばれます。 運用環境では、デプロイするネットワーク仮想アプライアンスは多くの場合、事前に構成された仮想マシンです。 いくつかのネットワーク仮想アプライアンスは [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) から入手できます。 この記事では、基本的な仮想マシンが作成されます。 

[az vm create](/cli/azure/vm#az_vm_create) で、*DMZ* サブネット内にネットワーク仮想アプライアンスを作成します。 仮想マシンを作成すると、既定では、Azure はパブリック IP アドレスを作成し、その仮想マシンに割り当てます。 この仮想マシンにはインターネットから接続する必要がないため、`--public-ip-address ""` パラメーターは Azure に、パブリック IP アドレスを作成して仮想マシンに割り当てないように指示します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

仮想マシンの作成には、数分かかります。 Azure が仮想マシンの作成を完了し、その仮想マシンに関する出力を返すまでは、次の手順に進まないでください。 運用環境では、デプロイするネットワーク仮想アプライアンスは多くの場合、事前に構成された仮想マシンです。 いくつかのネットワーク仮想アプライアンスは [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) から入手できます。

ネットワーク インターフェイスに割り当てられていないすべての IP アドレス宛てのトラフィックを転送する仮想マシンに接続された Azure [ネットワーク インターフェイス](virtual-network-network-interface.md)ごとに IP 転送を有効にする必要があります。 *DMZ* サブネット内にネットワーク仮想アプライアンスを作成したとき、Azure は *myVmNvaVMNic* という名前のネットワーク インターフェイスを自動的に作成し、そのネットワーク インターフェイスを仮想マシンに接続し、そのネットワーク インターフェイスにプライベート IP アドレス *10.0.2.4* を割り当てました。 [az network nic update](/cli/azure/network/nic#az_network_nic_update) でネットワーク インターフェイスの IP 転送を有効にします。

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

仮想マシン内では、オペレーティング システム、または仮想マシン内で実行されているアプリケーションもネットワーク トラフィックを転送できる必要があります。 運用環境内にネットワーク仮想アプライアンスをデプロイすると、そのアプライアンスは通常、トラフィックを転送する前にその他の何らかの機能をフィルタ処理、ログ記録、または実行します。 ただし、この記事では、オペレーティング システムは単純に、受信したすべてのトラフィックを転送します。 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) (これは、オペレーティング システム内の IP 転送を有効にするコマンドを実行します) で、仮想マシンのオペレーティング システム内の IP 転送を有効にします。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
このコマンドは、実行するのに最大 1 分かかることがあります。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

後の手順で、*パブリック* サブネットからのトラフィックがネットワーク仮想アプライアンス経由で*プライベート* サブネットにルーティングされることを検証できるように、仮想ネットワーク内に 2 つの仮想マシンを作成します。 

[az vm create](/cli/azure/vm#az_vm_create) で*パブリック* サブネット内に仮想マシンを作成します。 `--no-wait` パラメーターを使用すると、Azure はバックグラウンドでコマンドを実行できるので、次のコマンドに進むことができます。 この記事を効率化するために、パスワードが使用されています。 通常、キーは運用環境デプロイで使用されます。 キーを使用する場合は、SSH エージェント転送も構成する必要があります。 詳細については、SSH クライアントのドキュメントを参照してください。 次のコマンドの `<replace-with-your-password>` を、使用するパスワードに置き換えます。

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

仮想マシンの作成には、数分かかります。 仮想マシンが作成された後、Azure CLI は、次の例のような情報を表示します。 

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
**publicIpAddress** を書き留めておきます。 このアドレスは、以降の手順で、インターネットから仮想マシンにアクセスするときに使用されます。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンス経由のトラフィックのルーティング

次のコマンドを使用して、*myVmMgmt* 仮想マシンとの SSH セッションを作成します。 *<publicIpAddress>* を仮想マシンのパブリック IP アドレスに置き換えます。 上の例では、IP アドレスは *13.90.242.231* です。

```bash 
ssh azureuser@<publicIpAddress>
```

パスワードの入力を求められたら、「[仮想マシンを作成する](#create-virtual-machines)」で選択したパスワードを入力します。

*myVmMgmt* 仮想マシンに traceroute をインストールするには、次のコマンドを使用します。

```bash 
sudo apt-get install traceroute
```

*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンへのネットワーク トラフィックのルーティングをテストするには、次のコマンドを使用します。

```bash
traceroute myvmweb
```

応答は次の例のようになります。

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

トラフィックが *myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンに直接ルーティングされていることを確認できます。 Azure の既定のルートは、トラフィックをサブネット間で直接ルーティングします。 

次のコマンドを使用して、*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンに SSH 接続します。

```bash 
ssh azureuser@myVmWeb
```

*myVmWeb* 仮想マシンに traceroute をインストールするには、次のコマンドを使用します。

```bash 
sudo apt-get install traceroute
```

*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンへのネットワーク トラフィックのルーティングをテストするには、次のコマンドを使用します。

```bash
traceroute myvmmgmt
```

応答は次の例のようになります。

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
最初のホップが 10.0.2.4 であることを確認できます。これは、ネットワーク仮想アプライアンスのプライベート IP アドレスです。 2 番目のホップは、*myVmMgmt* 仮想マシンのプライベート IP アドレスである 10.0.1.4 です。 *myRouteTablePublic* ルート テーブルに追加され、*パブリック* サブネットに関連付けられたルートにより、Azure はトラフィックを*プライベート* サブネットに直接ではなく、NVA 経由でルーティングするようになります。

*myVmWeb* 仮想マシンと *myVmMgmt* 仮想マシンの両方への SSH セッションを閉じます。

## <a name="troubleshoot-routing"></a>ルーティングのトラブルシューティング

前の手順で説明したように、Azure は既定のルートを適用します。これは必要に応じて、独自のルートで上書きできます。 場合によっては、トラフィックが期待どおりにルーティングされないことがあります。 トラフィックが 2 つの仮想マシン間でどのようにルーティングされるかを判定するには、[az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) を使用します。 たとえば、次のコマンドは、*myVmWeb* (10.0.0.4) 仮想マシンから *myVmMgmt* (10.0.1.4) 仮想マシンへのトラフィック ルーティングをテストします。

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
少し待つと、次の出力が返されます。

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

この出力により、*myVmWeb* から *myVmMgmt* へのトラフィックの次ホップの IP アドレスが 10.0.2.4 (*myVmNva* 仮想マシン) であり、次ホップの種類が *VirtualAppliance* であり、ルーティングを発生させるルート テーブルが *myRouteTablePublic* であることがわかります。

各ネットワーク インターフェイスの効果的なルートは、Azure の既定のルートとユーザーが定義する任意のルートの組み合わせです。 [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) で、仮想マシン内のネットワーク インターフェイスのすべての効果的なルートを表示します。 たとえば、*myVmWeb* 仮想マシン内の *MyVmWebVMNic* ネットワーク インターフェイスの効果的なルートを表示するには、次のコマンドを入力します。

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

すべての既定のルートと、前の手順で追加したルートが返されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングするネットワーク仮想アプライアンスを作成しました。 仮想ネットワーク内では多数の Azure リソースをデプロイできますが、一部の Azure PaaS サービスのリソースは仮想ネットワークにデプロイできません。 ただし、一部の Azure PaaS サービスのリソースへのアクセスを、引き続き仮想ネットワーク サブネットからのトラフィックのみに制限できます。 Azure PaaS リソースへのネットワーク アクセスを制限する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [PaaS リソースへのネットワーク アクセスを制限する](virtual-network-service-endpoints-configure.md#azure-cli)
