---
title: パブリック IP アドレス プレフィックスの作成 - Azure CLI
titlesuffix: Azure Virtual Network
description: Azure CLI を使用してパブリック IP アドレス プレフィックスを作成する方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 1b95c4ac4a6dcf6b3f065a6d5952eeff122855f3
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652873"
---
# <a name="create-a-public-ip-address-prefix-using-azure-cli"></a>Azure CLI を使用してパブリック IP アドレス プレフィックスを作成する

パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 パブリック IP アドレス プレフィックスは、Standard SKU のパブリック IP アドレスの連続した範囲です。 

パブリック IP アドレス リソースを作成するときに、プレフィックスから静的なパブリック IP アドレスを割り当てて、そのアドレスを仮想マシン、ロード バランサーやその他のリソースに関連付けることができます。 詳細については、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、**myResourceGroup** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

このセクションでは、Azure PowerShell を使用してゾーン冗長、ゾーンまたは非ゾーンのパブリック IP プレフィックスを作成します。 

例のプレフィックスは次のとおりです。

* **IPv4** - /28 (16 個のアドレス)

* **IPv6** - /124 (16 個のアドレス)

使用可能なプレフィックス サイズの詳細については、「[プレフィックス サイズ](public-ip-address-prefix.md#prefix-sizes)」を参照してください。

[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) を使用して、**eastus2** の場所に **myPublicIpPrefix** という名前のパブリック IP プレフィックスを作成します。

### <a name="zone-redundant-ipv4-prefix"></a>ゾーン冗長 IPv4 プレフィックス

IPv4 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv4**」と入力します。 ゾーン冗長 IPv4 プレフィックスを作成するには、**Zone** パラメーターに「**1,2,3**」と入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

### <a name="zonal-ipv4-prefix"></a>ゾーン IPv4 プレフィックス

IPv4 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv4**」と入力します。 ゾーン 2 にゾーン IP プレフィックスを作成するには、**Zone** パラメーターに「**2**」と入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

### <a name="non-zonal-ipv4-prefix"></a>非ゾーン IPv4 プレフィックス

IPv4 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv4**」と入力します。 非ゾーン IP プレフィックスを作成するには、**Zone** パラメーターを削除します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4
```

コマンドでの **zone** パラメーターの削除は、すべてのリージョンで有効です。  

**zone** パラメーターの削除は、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの標準パブリック IP アドレスの既定の選択です。

### <a name="zone-redundant-ipv6-prefix"></a>ゾーン冗長 IPv6 プレフィックス

IPv4 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv6**」と入力します。 ゾーン冗長 IPv6 プレフィックスを作成するには、**Zone** パラメーターに「**1,2,3**」と入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

### <a name="zonal-ipv6-prefix"></a>ゾーン IPv6 プレフィックス

IPv6 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv6**」と入力します。 ゾーン 2 にゾーン IP プレフィックスを作成するには、**Zone** パラメーターに「**2**」と入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

### <a name="non-zonal-ipv6-prefix"></a>非ゾーン IPv6 プレフィックス

IPv6 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv6**」と入力します。 非ゾーン IP プレフィックスを作成するには、**Zone** パラメーターを削除します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6
```

コマンドでの **zone** パラメーターの削除は、すべてのリージョンで有効です。  

**zone** パラメーターの削除は、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの標準パブリック IP アドレスの既定の選択です。

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する

プレフィックスを作成したら、プレフィックスから静的 IP アドレスを作成する必要があります。 このセクションでは、以前に作成したプレフィックスから静的 IP アドレスを作成します。

**myPublicIpPrefix** プレフィックスで [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用してパブリック IP アドレスを作成します。

### <a name="ipv4-address"></a>IPv4 アドレス

IPv4 パブリック IP アドレスを作成するには、**IpAddressVersion** パラメーターに「**IPv4**」と入力します。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

### <a name="ipv6-address"></a>IPv6 アドレス

IPv6 パブリック IP プレフィックスを作成するには、**IpAddressVersion** パラメーターに「**IPv6**」と入力します。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

>[!NOTE]
>プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)」をご覧ください。

## <a name="delete-a-prefix"></a>プレフィックスを削除する

このセクションでは、プレフィックスを削除する方法を説明します。

パブリック IP プレフィックスを削除するには、[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) を使用します。

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup
```

>[!NOTE]
>プレフィックス内のアドレスがパブリック IP アドレス リソースに関連付けられている場合は、まずパブリック IP アドレス リソースを削除する必要があります。 [パブリック IP アドレスの削除](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、パブリック IP プレフィックスとそのプレフィックスからパブリック IP を作成しました。 


パブリック IP プレフィックスを使い終えたら、[az group delete](/cli/azure/group#az_group_delete) を使用してリソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- [パブリック IP プレフィックス](public-ip-address-prefix.md)を使用するシナリオと利点について確認します。
