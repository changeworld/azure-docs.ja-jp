---
title: 'クイック スタート: パブリック IP アドレス プレフィックスを作成する - PowerShell'
titlesuffix: Azure Virtual Network
description: PowerShell を使用してパブリック IP アドレス プレフィックスを作成する方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 24c046aa7aab0932065f87ba3179185e9c5ed2fc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369653"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-powershell"></a>クイック スタート: PowerShell を使用してパブリック IP アドレス プレフィックスを作成する

パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 パブリック IP アドレス プレフィックスは、Standard SKU のパブリック IP アドレスの連続した範囲です。 

パブリック IP アドレス リソースを作成するときに、プレフィックスから静的なパブリック IP アドレスを割り当てて、そのアドレスを仮想マシン、ロード バランサーやその他のリソースに関連付けることができます。 詳細については、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、**QuickStartCreateIPPrefix-rg** という名前のリソース グループを場所 **eastus2** に作成します。

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

このセクションでは、Azure PowerShell を使用してゾーン冗長、ゾーンまたは非ゾーンのパブリック IP プレフィックスを作成します。 

例のプレフィックスは次のとおりです。

* **IPv4** - /28 (16 個のアドレス)

* **IPv6** - /124 (16 個のアドレス)

使用可能なプレフィックス サイズの詳細については、「[プレフィックス サイズ](public-ip-address-prefix.md#prefix-sizes)」を参照してください。

[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) を使用して、**eastus2** の場所に **myPublicIpPrefix** という名前のパブリック IP プレフィックスを作成します。

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**ゾーン冗長 IPv4 プレフィックス**](#tab/ipv4-zone-redundant)

IPv4 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv4**」と入力します。 ゾーン冗長 IPv4 プレフィックスを作成するには、 **`-Zone`** パラメーターに「**1,2,3**」と入力します。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

# <a name="zonal-ipv4-prefix"></a>[**ゾーン IPv4 プレフィックス**](#tab/ipv4-zonal)

IPv4 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv4**」と入力します。 ゾーン 2 にゾーン IP プレフィックスを作成するには、 **`-Zone`** パラメーターに「**2**」と入力します。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal-ipv4-prefix"></a>[**非ゾーン IPv4 プレフィックス**](#tab/ipv4-non-zonal)

IPv4 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv4**」と入力します。 非ゾーン IP プレフィックスを作成するには、 **`-Zone`** パラメーターを削除します。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

コマンドでの **`-Zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`-Zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

---

## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**ゾーン冗長 IPv6 プレフィックス**](#tab/ipv6-zone-redundant)

IPv4 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv6**」と入力します。 ゾーン冗長 IPv6 プレフィックスを作成するには、 **`-Zone`** パラメーターに「**1,2,3**」と入力します。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

# <a name="zonal-ipv6-prefix"></a>[**ゾーン IPv6 プレフィックス**](#tab/ipv6-zonal)

IPv6 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv6**」と入力します。 ゾーン 2 にゾーン IP プレフィックスを作成するには、 **`-Zone`** パラメーターに「**2**」と入力します。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal-ipv6-prefix"></a>[**非ゾーン IPv6 プレフィックス**](#tab/ipv6-non-zonal)

IPv6 パブリック IP プレフィックスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv6**」と入力します。 非ゾーン IP プレフィックスを作成するには、 **`-Zone`** パラメーターを削除します。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

コマンドでの **`-Zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`-Zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する

プレフィックスを作成したら、プレフィックスから静的 IP アドレスを作成する必要があります。 このセクションでは、以前に作成したプレフィックスから静的 IP アドレスを作成します。

**myPublicIpPrefix** プレフィックスで [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用してパブリック IP アドレスを作成します。

# <a name="ipv4-address"></a>[**IPv4 アドレス**](#tab/ipv4-address)

IPv4 パブリック IP アドレスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv4**」と入力します。

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

# <a name="ipv6-address"></a>[**IPv6 アドレス**](#tab/ipv6-address)

IPv6 パブリック IP アドレスを作成するには、 **`-IpAddressVersion`** パラメーターに「**IPv6**」と入力します。
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

---

>[!NOTE]
>プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)」をご覧ください。

## <a name="delete-a-prefix"></a>プレフィックスを削除する

このセクションでは、プレフィックスを削除する方法を説明します。

パブリック IP プレフィックスを削除するには、[Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) を使用します。

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>プレフィックス内のアドレスがパブリック IP アドレス リソースに関連付けられている場合は、まずパブリック IP アドレス リソースを削除する必要があります。 [パブリック IP アドレスの削除](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、パブリック IP プレフィックスを作成し、さらにそのプレフィックスからパブリック IP を作成しました。 

パブリック IP プレフィックスの処理が終わったら、リソース グループと、それに含まれているすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'QuickStartCreateIPPrefix-rg'
```

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure CLI を使用して、パブリック IP プレフィックスを作成する方法を学習してください。
> [!div class="nextstepaction"]
> [PowerShell を使用してパブリック IP アドレスを作成する](create-public-ip-powershell.md)
