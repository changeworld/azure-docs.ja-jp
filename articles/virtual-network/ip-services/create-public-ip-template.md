---
title: 'クイックスタート: Resource Manager テンプレートを使用してパブリック IP を作成する'
titleSuffix: Azure Virtual Network
description: Resource Manager テンプレートを使用してパブリック IP を作成する方法について説明します
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: cb90b716351802271a12fc72c1ead9e4fac2968e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456425"
---
# <a name="quickstart-create-a-public-ip-address-using-a-resource-manager-template"></a>クイックスタート: Resource Manager テンプレートを使用してパブリック IP アドレスを作成する

この記事では、Resource Manager テンプレート内にパブリック IP アドレス リソースを作成する方法について説明します。

このパブリック IP を関連付けることができるリソース、Basic と Standard SKU の違いについては、「[パブリック IP アドレス](public-ip-addresses.md)」を参照してください。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* Azure サブスクリプションのリソース グループ。
* パブリック IP セクション用の Azure Resource Manager テンプレート。

## <a name="create-standard-sku-public-ip-with-zones"></a>ゾーンを含む Standard SKU パブリック IP を作成する

このセクションでは、ゾーンを含むパブリック IP を作成します。 パブリック IP アドレスは、ゾーン冗長またはゾーン ベースにすることができます。

### <a name="zone-redundant"></a>ゾーン冗長

このセクションのコードでは、**myStandardPublicIP** という名前の Standard ゾーン冗長パブリック IPv4 アドレスを作成します。

IPv6 アドレスを作成するには、 **`publicIPAddressVersion`** パラメーターを **IPv6** に変更します。

追加するテンプレートのセクション:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> 2020-08-01 より前の API のバージョンでは、Standard SKU のゾーン パラメーターを指定せずに上記のコードを使用して、ゾーン冗長 IP アドレスを作成します。 
>

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

### <a name="zonal"></a>ゾーン ベース

このセクションのコードでは、**myStandardPublicIP-zonal** という名前の Standard ゾーン パブリック IPv4 アドレスを作成します。 

ゾーン 2 に標準のゾーン パブリック IP アドレスを作成するために、 **"zones"** プロパティに "2" が含まれています。

追加するテンプレートのセクション:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-zonal",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "2"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

## <a name="create-standard-public-ip-without-zones"></a>ゾーンのない標準パブリック IP を作成する

このセクションでは、非ゾーン IP アドレスを作成します。 

このセクションのコードでは、**myStandardPublicIP** という名前の Standard 非ゾーン パブリック IPv4 アドレスを作成します。 コード セクションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) の有無にかかわらず、すべてのリージョンに対して有効です。

IPv6 アドレスを作成するには、**publicIPAddressVersion** パラメーターを **IPv6** に変更します。

追加するテンプレートのセクション:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-nozone",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> 2020-08-01 より前の API のバージョンでは、Standard SKU にゾーン パラメーターを指定しないと、ゾーン冗長 IP アドレスが作成されます。 
>


## <a name="create-a-basic-public-ip"></a>Basic パブリック IP を作成する

このセクションでは、Basic IP を作成します。 Basic パブリック IP では、Availability Zones はサポートされません。 

このセクションのコードでは、**myBasicPublicIP** という名前の Basic パブリック IPv4 アドレスを作成します。

IPv6 アドレスを作成するには、**publicIPAddressVersion** パラメーターを **IPv6** に変更します。 

追加するテンプレートのセクション:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myBasicPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Basic"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を **Dynamic** に変更することで **publicIPAllocationMethod** の IP の割り当てを選択できます。 

>[!NOTE]
> Basic IPv6 アドレスは、常に "動的" である必要があります。

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。

### <a name="routing-preference"></a>ルーティング設定

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワーク経由でユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。代わりに転送 ISP ネットワークを使用して、コスト最適化された速度でトラフィックが配信されます。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](routing-preference-overview.md)」を参照してください。

Standard ゾーン冗長パブリック IPv4 アドレスにインターネット ルーティング設定を使用するためのテンプレート セクションは、次のようになります。

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardZRPublicIP-RP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4",
    "ipTags": [
      {
        "ipTagType": "RoutingPreference",
        "tag": "Internet"
      }
    ]
  }
}
```

### <a name="tier"></a>階層

パブリック IP アドレスは、1 つのリージョンに関連付けられています。 **グローバル** 階層では 1 つの IP アドレスが、複数のリージョンにまたがって使用されます。 **グローバル** 階層は、リージョン間ロード バランサーのフロントエンドに必要です。  

詳細については、[リージョン間ロード バランサー](../../load-balancer/cross-region-overview.md)に関するページを参照してください。

Standard グローバル パブリック IPv4 アドレスを使用するためのテンプレート セクションは、次のようになります。

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-Global",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard",
    "tier": "Global"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

## <a name="additional-information"></a>関連情報 

この記事に記載されているパブリック IP プロパティの詳細については、[パブリック IP アドレスの管理](virtual-network-public-ip-address.md#create-a-public-ip-address)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
