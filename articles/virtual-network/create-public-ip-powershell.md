---
title: パブリック IP を作成する - Azure PowerShell
titleSuffix: Azure Virtual Network
description: Azure PowerShell を使用してパブリック IP を作成する方法について説明します
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/03/2021
ms.author: allensu
ms.openlocfilehash: dc9a6aa398a8cab2927bc8890ceaeb4d0026714c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434809"
---
# <a name="create-a-public-ip-address-using-azure-powershell"></a>Azure PowerShell を使用してパブリック IP アドレスを作成する

この記事では、Azure PowerShell を使用してパブリック IP アドレス リソースを作成する方法について説明します。 

パブリック IP をサポートするリソースの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して **myResourceGroup** という名前のリソース グループを **eastus2** に作成します。

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-standard-sku-public-ip-with-zones"></a>ゾーンを含む Standard SKU パブリック IP を作成する

このセクションでは、ゾーンを含むパブリック IP を作成します。 パブリック IP アドレスは、ゾーン冗長またはゾーン ベースにすることができます。

### <a name="zone-redundant"></a>ゾーン冗長

>[!NOTE]
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている Powershell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myStandardZRPublicIP** という名前の Standard ゾーン冗長パブリック IPv4 アドレスを作成します。 

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZRPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> 4\.5.0 より前の Az.Network モジュールに対しては、ゾーン パラメーターを指定せずに上記のコマンドを実行して、ゾーン冗長 IP アドレスを作成します。 
>

### <a name="zonal"></a>ゾーン ベース

**myResourceGroup** で **myStandardZonalPublicIP** という名前の Standard ゾーン ベースのパブリック IPv4 アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZonalPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

## <a name="create-standard-public-ip-without-zones"></a>ゾーンのない標準パブリック IP を作成する

このセクションでは、非ゾーン IP アドレスを作成します。  

>[!NOTE]
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている Powershell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myStandardPublicIP** という名前の Standard パブリック IPv4 アドレスを非ゾーン リソースとして作成します。 

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
コマンドでの **zone** パラメーターの削除は、すべてのリージョンで有効です。  

**zone** パラメーターの削除は、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの標準パブリック IP アドレスの既定の選択です。

## <a name="create-a-basic-public-ip"></a>Basic パブリック IP を作成する

このセクションでは、Basic IP を作成します。 Basic パブリック IP では、Availability Zones はサポートされません。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myBasicPublicIP** という名前の Basic 静的パブリック IPv4 アドレスを作成します。  

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。 

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

>[!NOTE]
> Basic IPv6 アドレスは、常に "動的" である必要があります。

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。

### <a name="routing-preference"></a>ルーティング設定

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワーク経由でユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。代わりに転送 ISP ネットワークを使用して、コスト最適化された速度でトラフィックが配信されます。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](./routing-preference-overview.md)」を参照してください。

このコマンドによって、ルーティング設定の種類に **インターネット** が指定された標準のゾーン冗長パブリック IPv4 アドレスが作成されます。

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = ${
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
### <a name="tier"></a>階層

パブリック IP アドレスは、1 つのリージョンに関連付けられています。 **グローバル** 階層では 1 つの IP アドレスが、複数のリージョンにまたがって使用されます。 **グローバル** 階層は、リージョン間ロード バランサーのフロントエンドに必要です。  

詳細については、「[リージョン間ロード バランサー](../load-balancer/cross-region-overview.md)」を参照してください。

次のコマンドでは、グローバル IPv4 アドレスが作成されます。 このアドレスは、リージョン間ロードバランサーのフロントエンドに関連付けることができます。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>グローバル階層アドレスでは、Availability Zones はサポートされません。

## <a name="additional-information"></a>関連情報 

この記事で紹介した個々のパラメーターの詳細については、[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
