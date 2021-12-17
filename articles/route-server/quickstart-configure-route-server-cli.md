---
title: 'クイック スタート: Azure CLI を使用してルート サーバーを作成および構成する'
description: このクイック スタートでは、Azure CLI を使用して、ルート サーバーを作成して構成する方法について学習します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: ca21e367752451f01c7ee8d1fc9596cfcb74029b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094445"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>クイック スタート: Azure CLI を使用してルート サーバーを作成および構成する 

この記事は、Azure PowerShell を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Route Server を構成するのに役立ちます。 Route Server では、NVA からルートを学習し、仮想ネットワーク内の仮想マシン上にそれらをプログラムします。 また、Azure Route Server は NVA に仮想ネットワーク ルートをアドバタイズします。 詳細については、[Azure Route Server](overview.md) に関するページを参照してください。

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure CLI を使用した Route Server デプロイ環境の図。" border="false":::

> [!IMPORTANT]
> 9 月 1 日より前に作成した Azure Route Server があり、パブリック IP アドレスが関連付けられていない場合は、管理目的で IP アドレスを取得できるようにするため、Route Server を再作成する必要があります。

##  <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 最新の Azure CLI があることを確認してください。ポータルで Azure Cloud Shell を使用することもできます。 
* [Azure Route Server のサービスの制限](route-server-faq.md#limitations)を確認します。 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択します。 

構成を始めるには、Azure アカウントにサインインします。 Cloud Shell の "試してみる" オプションを使用している場合は、自動的にサインインします。 接続については、次の例を参照してください。

```azurecli-interactive
az login
```

アカウントのサブスクリプションを確認します。

```azurecli-interactive
az account list
```

ExpressRoute 回線を作成するサブスクリプションを選択します。

```azurecli-interactive
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する 

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Route Server を作成する前に、Route Server をホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、**myRouteServerRG** という名前のリソース グループを **westus** の場所に作成します。

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 この例では、**myVirtualNetwork** という名前の既定の仮想ネットワークを作成します。 既に仮想ネットワークがある場合は、次のセクションに進むことができます。

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>専用サブネットを追加する 

Azure Route Server には、*RouteServerSubnet* という名前の専用サブネットが必要です。 サブネットのサイズは、少なくとも /27 または短いプレフィックス (/26 や /25 など) である必要があります。そうでない場合は、Route Server をデプロイするときにエラー メッセージが表示されます。 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) を使用して **RouteServerSubnet** という名前のサブネット構成を作成します。

1. 次のコマンドを実行して、*RouteServerSubnet* を自分の仮想ネットワークに追加します。

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. RouteServerSubnet ID を書き留めておきます。 *RouteServerSubnet* のリソース ID を取得して `subnet_id` 変数に格納するには、[az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) を使用します。

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>ルート サーバーを作成する 

1. Route Server の構成が管理されるバックエンド サービスへの接続を確保するために、パブリック IP アドレスの割り当てが必要です。 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**RouteServerIP** という名前の標準パブリック IP を作成します。

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create) を使用して Azure Route Server を作成します。 この例では、**myRouteServer** という名前の Azure Route Server を作成します。 *hosted-subnet* は、前のセクションで作成した RouteServerSubnet のリソース ID です。

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>NVA との BGP ピアリングを作成する 

[az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create) を使用して、Route Server と NVA の間に BGP ピアリングを確立します。 

`peer-ip` は、NVA に割り当てられた仮想ネットワーク IP です。 `peer-asn` は、NVA で構成された自律システム番号 (ASN) です。 ASN は、65515 から 65520 の範囲に含まれているもの以外の 16 ビットの任意の数値にすることができます。 この ASN の範囲は Microsoft によって予約されています。

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

冗長性を確保するために、異なる NVA、または同じ NVA の別のインスタンスとのピアリングを設定するには、異なる *PeerName*、*PeerIp*、および *PeerAsn* を使って上記と同じコマンドを使用します。

## <a name="complete-the-configuration-on-the-nva"></a>NVA の構成を完了する 

NVA の構成を完了して BGP セッションを有効にするには、Azure Route Server の IP と ASN が必要です。 この情報は、[az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show) を使用して取得できます。

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

出力は次のようになります。

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>ルート交換を構成する 

同じ仮想ネットワーク内に ExpressRoute と Azure VPN ゲートウェイがあり、それらでルートを交換する場合は、Azure Route Server でルート交換を有効にできます。

> [!IMPORTANT]
> グリーンフィールド デプロイの場合は、Azure Route Server を作成する前に必ず Azure VPN ゲートウェイを作成してください。そうしないと、Azure VPN Gateway のデプロイが失敗します。
> 

1. Azure Route Server とゲートウェイ間のルート交換を有効にするには、`--allow-b2b-traffic`` フラグを **true** に設定して [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) を使用します。

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. Azure Route Server とゲートウェイ間のルート交換を無効にするには、`--allow-b2b-traffic`` フラグを **false** に設定して [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update) を使用します。

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>トラブルシューティング 

[az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes) を使用して、Azure Route Server によってアドバタイズされたルートを表示します。

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

[az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes) を使用して、Azure Route Server によって学習されたルートを表示します。

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Route Server が不要になった場合は、最初のコマンドを使用して BGP ピアリングを削除してから、2 番目のコマンドを使用して Route Server を削除します。 

1. [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete) を使用して、Azure Route Server と NVA の間の BGP ピアリングを削除します。

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete) を使用して Azure Route Server を削除します。 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>次の手順

Azure Route Server を作成した後、Azure Route Server と ExpressRoute および VPN Gateway との連携方法について引き続き学習するには、次を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
 
