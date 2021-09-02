---
title: パブリック IP の作成 - Azure CLI
titleSuffix: Azure Virtual Network
description: Azure CLI を使用してパブリック IP を作成する方法について説明します
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/05/2021
ms.author: allensu
ms.openlocfilehash: 8b68597abaf4a715dc55a92f2445000c1aaed0d0
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435130"
---
# <a name="create-a-public-ip-address-using-azure-cli"></a>Azure CLI を使用してパブリック IP アドレスを作成する

この記事では、Azure CLI を使用してパブリック IP アドレス リソースを作成する方法について説明します。 

パブリック IP をサポートするリソースの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。

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

## <a name="create-standard-sku-public-ip-with-zones"></a>ゾーンを含む Standard SKU パブリック IP を作成する

このセクションでは、ゾーンを含むパブリック IP を作成します。 パブリック IP アドレスは、ゾーン冗長またはゾーン ベースにすることができます。

### <a name="zone-redundant"></a>ゾーン冗長

>[!NOTE]
>次のコマンドは、API バージョン 2020-08-01 以降で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

**myResourceGroup** に **myStandardZRPublicIP** という Standard ゾーン冗長パブリック IPv4 アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用します。  

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 より前の API のバージョンでは、ゾーン冗長 IP アドレスを作成する際、ゾーン パラメーターを指定せずにコマンドを実行します。 
>

### <a name="zonal"></a>ゾーン ベース

**myResourceGroup** で **myStandardZonalPublicIP** という名前の Standard ゾーン ベースのパブリック IPv4 アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。


## <a name="create-standard-public-ip-without-zones"></a>ゾーンのない Standard パブリック IP を作成する

このセクションでは、非ゾーン IP アドレスを作成します。  

>[!NOTE]
>次のコマンドは、API バージョン 2020-08-01 以降で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**myResourceGroup** に **myStandardPublicIP** という名前の Standard パブリック IPv4 アドレスを非ゾーン リソースとして作成します。 

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard
```
コマンドでの **zone** パラメーターの削除は、すべてのリージョンで有効です。  

**zone** パラメーターの削除は、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

## <a name="create-a-basic-public-ip"></a>Basic パブリック IP を作成する

このセクションでは、Basic IP を作成します。 Basic パブリック IP では、Availability Zones はサポートされません。

**myResourceGroup** に **myBasicPublicIP** という Basic 静的パブリック IPv4 アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用します。

IPv6 アドレスを作成するには、**version** パラメータを **IPv6** に変更します。 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

>[!NOTE]
> Basic IPv6 アドレスは、常に "動的" である必要があります。

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。

### <a name="routing-preference"></a>ルーティングの優先順位

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワークを通してユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。その代わりに、トラフィックをコスト最適化された速度で配信するために、転送 ISP ネットワークを使用します。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](./routing-preference-overview.md)」を参照してください。

このコマンドによって、ルーティングの優先順位の種類に **インターネット** が指定された新しい Standard ゾーン冗長パブリック IPv4 アドレスが作成されます。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

### <a name="tier"></a>階層

パブリック IP アドレスは、1 つのリージョンに関連付けされます。 **グローバル** 階層では、1 つの IP アドレスが複数のリージョンにまたがって使用されます。 リージョン間ロード バランサーのフロントエンドには、**グローバル** 階層が必要です。  

詳細については、[リージョン間ロード バランサー](../load-balancer/cross-region-overview.md)に関するページを参照してください。

次のコマンドでは、グローバル IPv4 アドレスが作成されます。 このアドレスは、リージョン間ロードバランサーのフロントエンドに関連付けることができます。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>グローバル階層アドレスでは、Availability Zones はサポートされません。

## <a name="additional-information"></a>関連情報 

この記事で紹介した個々のパラメーターの詳細については、[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける。
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
