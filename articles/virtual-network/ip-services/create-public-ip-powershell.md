---
title: 'クイック スタート: パブリック IP を作成する - PowerShell'
titleSuffix: Azure Virtual Network
description: このクイック スタートでは、Azure PowerShell を使用してパブリック IP を作成する方法について説明します
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 51a59615d0f87943e195a3f189d96eccaf67623f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369422"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>クイック スタート: PowerShell を使用してパブリック IP アドレスを作成する

このクイック スタートでは、Azure パブリック IP アドレスを作成する方法について説明します。 Azure のパブリック IP アドレスは、Azure リソースへのパブリック接続に使用されます。 パブリック IP アドレスは Basic と Standard の 2 つの SKU で使用できます。 リージョン、グローバルの 2 つのレベルのパブリック IP アドレスを使用できます。 パブリック IP アドレスのルーティング優先設定は、作成時に設定されます。 インターネット ルーティングと Microsoft ネットワーク ルーティングが利用可能な選択肢です。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して **QuickStartCreateIP-rg** という名前のリソース グループを場所 **eastus2** に作成します。

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>パブリック IP を作成する

# <a name="standard-sku"></a>[**Standard SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>運用環境のワークロードには Standard SKU パブリック IP が推奨されます。  SKU の詳細については、 **[パブリック IP アドレス](public-ip-addresses.md)** に関するページを参照してください。
>
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている Powershell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

このセクションでは、ゾーンを含むパブリック IP を作成します。 パブリック IP アドレスは、ゾーン冗長またはゾーン ベースにすることができます。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**QuickStartCreateIP-rg** に **myStandardPublicIP** という名前の Standard ゾーン冗長パブリック IPv4 アドレスを作成します。 

IPv6 アドレスを作成するには、 **`--IpAddressVersion`** パラメーターを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

# <a name="basic-sku"></a>[**Basic SKU**](#tab/create-public-ip-basic)

>[!NOTE]
>運用環境のワークロードには Standard SKU パブリック IP が推奨されます。  SKU の詳細については、 **[パブリック IP アドレス](public-ip-addresses.md)** に関するページを参照してください。

このセクションでは、Basic IP を作成します。 Basic パブリック IP では、Availability Zones はサポートされません。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**QuickStartCreateIP-rg** に **myBasicPublicIP** という名前の Basic 静的パブリック IPv4 アドレスを作成します。  

IPv6 アドレスを作成するには、 **`--IpAddressVersion`** パラメーターを **IPv6** に変更します。 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、 **`-AllocationMethod`** を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

>[!NOTE]
> Basic IPv6 アドレスは、常に "動的" である必要があります。

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>ゾーンまたは非ゾーン パブリック IP アドレスを作成する

このセクションでは、ゾーンまたは非ゾーンのパブリック IP アドレスを作成する方法について説明します。

# <a name="zonal"></a>[**ゾーン ベース**](#tab/create-public-ip-zonal)

**QuickStartCreateIP-rg** で **myStandardPublicIP-zonal** という名前の Standard ゾーンのパブリック IPv4 アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

IPv6 アドレスを作成するには、 **`--IpAddressVersion`** パラメーターを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal"></a>[**非ゾーン**](#tab/create-public-ip-non-zonal)

このセクションでは、非ゾーン IP アドレスを作成します。  

>[!NOTE]
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている PowerShell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**QuickStartCreateIP-rg** に **myStandardPublicIP-nozone** という名前の Standard パブリック IPv4 アドレスを非ゾーン リソースとして作成します。 

IPv6 アドレスを作成するには、 **`-IpAddressVersion`** パラメーターを **IPv6** に変更します。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
コマンドでの **`-Zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`-Zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

---

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。

# <a name="routing-preference"></a>[**ルーティングの優先順位**](#tab/routing-preference)

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワーク経由でユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。代わりに転送 ISP ネットワークを使用して、コスト最適化された速度でトラフィックが配信されます。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](routing-preference-overview.md)」を参照してください。

このコマンドによって、ルーティング設定の種類に **インターネット** が指定された標準のゾーン冗長パブリック IPv4 アドレスが作成されます。

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**階層**](#tab/tier)

パブリック IP アドレスは、1 つのリージョンに関連付けられています。 **グローバル** 階層では 1 つの IP アドレスが、複数のリージョンにまたがって使用されます。 **グローバル** 階層は、リージョン間ロード バランサーのフロントエンドに必要です。  

詳細については、「[リージョン間ロード バランサー](../../load-balancer/cross-region-overview.md)」を参照してください。

次のコマンドでは、グローバル IPv4 アドレスが作成されます。 このアドレスは、リージョン間ロードバランサーのフロントエンドに関連付けることができます。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想マシンとパブリック IP プレフィックスを使い終えたら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用してリソース グループとそれに含まれるすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>次のステップ

次の記事に進んで、パブリック IP プレフィックスの作成方法を学習してください。
> [!div class="nextstepaction"]
> [PowerShell を使用してパブリック IP プレフィックスを作成する](create-public-ip-prefix-powershell.md)