---
title: 'クイック スタート: パブリック IP アドレス プレフィックスの作成 - Azure CLI'
titlesuffix: Azure Virtual Network
description: Azure CLI を使用してパブリック IP アドレス プレフィックスを作成する方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369185"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>クイックスタート: Azure CLI を使用してパブリック IP アドレス プレフィックスを作成する

パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 パブリック IP アドレス プレフィックスは、Standard SKU のパブリック IP アドレスの連続した範囲です。 

パブリック IP アドレス リソースを作成するときに、プレフィックスから静的なパブリック IP アドレスを割り当てて、そのアドレスを仮想マシン、ロード バランサーやその他のリソースに関連付けることができます。 詳細については、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して **QuickStartCreateIPPrefix-rg** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

このセクションでは、Azure PowerShell を使用してゾーン冗長、ゾーンまたは非ゾーンのパブリック IP プレフィックスを作成します。 

例のプレフィックスは次のとおりです。

* **IPv4** - /28 (16 個のアドレス)

* **IPv6** - /124 (16 個のアドレス)

使用可能なプレフィックス サイズの詳細については、「[プレフィックス サイズ](public-ip-address-prefix.md#prefix-sizes)」を参照してください。

[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) を使用して、**eastus2** の場所に **myPublicIpPrefix** という名前のパブリック IP プレフィックスを作成します。

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**ゾーン冗長 IPv4 プレフィックス**](#tab/ipv4-zone-redundant)

IPv4 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv4**」を入力します。 ゾーン冗長 IPv4 プレフィックスを作成するには、 **`--zone`** パラメーターに「**1,2,3**」を入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**ゾーン IPv4 プレフィックス**](#tab/ipv4-zonal)

IPv4 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv4**」を入力します。 **`--zone`** パラメーターに「**2**」を入力して、ゾーン 2 にゾーン IP プレフィックスを作成します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal-ipv4-prefix"></a>[**非ゾーン IPv4 プレフィックス**](#tab/ipv4-non-zonal)

IPv4 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv4**」を入力します。 **`--zone`** パラメーターを削除して、非ゾーン IP プレフィックスを作成します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

このコマンドの **`--zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`--zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) のないリージョンの標準パブリック IP アドレスに対する既定の選択です。

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**ゾーン冗長 IPv6 プレフィックス**](#tab/ipv6-zone-redundant)

IPv4 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv6**」を入力します。 ゾーン冗長 IPv6 プレフィックスを作成するには、 **`--zone`** パラメーターに「**1,2,3**」を入力します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**ゾーン IPv6 プレフィックス**](#tab/ipv6-zonal)

IPv6 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv6**」を入力します。 **`--zone`** パラメーターに「**2**」を入力して、ゾーン 2 にゾーン IP プレフィックスを作成します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal-ipv6-prefix"></a>[**非ゾーン IPv6 プレフィックス**](#tab/ipv6-non-zonal)

IPv6 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv6**」を入力します。 **`--zone`** パラメーターを削除して、非ゾーン IP プレフィックスを作成します。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

このコマンドの **`--zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`--zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) のないリージョンの標準パブリック IP アドレスに対する既定の選択です。

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する

プレフィックスを作成したら、プレフィックスから静的 IP アドレスを作成する必要があります。 このセクションでは、以前に作成したプレフィックスから静的 IP アドレスを作成します。

**myPublicIpPrefix** プレフィックスで [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用してパブリック IP アドレスを作成します。

# <a name="ipv4-address"></a>[**IPv4 アドレス**](#tab/ipv4-address)

IPv4 パブリック IP アドレスを作成するには、 **`--version`** パラメーターに「**IPv4**」を入力します。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**IPv6 アドレス**](#tab/ipv6-address)

IPv6 パブリック IP プレフィックスを作成するには、 **`--version`** パラメーターに「**IPv6**」を入力します。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)」をご覧ください。

## <a name="delete-a-prefix"></a>プレフィックスを削除する

このセクションでは、プレフィックスを削除する方法を説明します。

パブリック IP プレフィックスを削除するには、[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) を使用します。

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>プレフィックス内のアドレスがパブリック IP アドレス リソースに関連付けられている場合は、まずパブリック IP アドレス リソースを削除する必要があります。 [パブリック IP アドレスの削除](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、パブリック IP プレフィックスとそのプレフィックスからパブリック IP を作成しました。 


パブリック IP プレフィックスを使い終えたら、[az group delete](/cli/azure/group#az_group_delete) を使用してリソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure PowerShell を使用して、パブリック IP プレフィックスを作成する方法を学習してください。
> [!div class="nextstepaction"]
> [Azure CLI を使用してパブリック IP を作成する](create-public-ip-cli.md)
