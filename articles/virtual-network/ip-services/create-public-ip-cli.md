---
title: 'クイック スタート: パブリック IP を作成する - Azure CLI'
titleSuffix: Azure Virtual Network
description: Azure CLI を使用してパブリック IP を作成する方法について説明します
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369402"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>クイック スタート: Azure CLI を使用してパブリック IP アドレスを作成する

このクイック スタートでは、Azure パブリック IP アドレスを作成する方法について説明します。 Azure のパブリック IP アドレスは、Azure リソースへのパブリック接続に使用されます。 パブリック IP アドレスは Basic と Standard の 2 つの SKU で使用できます。 リージョン、グローバルの 2 つのレベルのパブリック IP アドレスを使用できます。 パブリック IP アドレスのルーティング優先設定は、作成時に設定されます。 インターネット ルーティングと Microsoft ネットワーク ルーティングが利用可能な選択肢です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して **QuickStartCreateIP-rg** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**Standard SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>運用環境のワークロードには Standard SKU パブリック IP が推奨されます。  SKU の詳細については、 **[パブリック IP アドレス](public-ip-addresses.md)** に関するページを参照してください。
>
>次のコマンドは、API バージョン **2020-08-01** **以降** で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**QuickStartCreateIP-rg** に **myStandardPublicIP** という名前の Standard ゾーン冗長パブリック IPv4 アドレスを作成します。  

IPv6 アドレスを作成するには、 **`--version`** パラメーターを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 より前の API のバージョンでは、ゾーン冗長 IP アドレスを作成する際、 **`--zone`** パラメーターを指定せずにコマンドを実行します。 
>

# <a name="basic-sku"></a>[**Basic SKU**](#tab/create-public-ip-basic)

このセクションでは、Basic IP を作成します。 Basic パブリック IP では、Availability Zones はサポートされません。

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**QuickStartCreateIP-rg** に **myBasicPublicIP** という名前の Basic 静的パブリック IPv4 アドレスを作成します。

IPv6 アドレスを作成するには、 **`--version`** パラメーターを **IPv6** に変更します。 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、 **`--allocation-method`** を **Dynamic** に変更することで **動的** IP の割り当てを選択できます。 

>[!NOTE]
> Basic IPv6 アドレスは、常に "動的" である必要があります。

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>ゾーンまたは非ゾーン IP アドレスを作成する

このセクションでは、ゾーンまたは非ゾーンのパブリック IP アドレスを作成する方法について説明します。

# <a name="zonal"></a>[**ゾーン ベース**](#tab/create-public-ip-zonal)

**QuickStartCreateIP-rg** で **myStandardPublicIP** という名前の Standard ゾーンのパブリック IPv4 アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

IPv6 アドレスを作成するには、 **`--version`** パラメーターを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>上記のゾーンのオプションは、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択です。

# <a name="non-zonal"></a>[**非ゾーン**](#tab/create-public-ip-non-zonal)

このセクションでは、非ゾーン IP アドレスを作成します。  

>[!NOTE]
>次のコマンドは、API バージョン 2020-08-01 以降で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**QuickStartCreateIP-rg** に **myStandardPublicIP** という名前の Standard パブリック IPv4 アドレスを非ゾーン リソースとして作成します。 

IPv6 アドレスを作成するには、 **`--version`** パラメーターを **IPv6** に変更します。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
コマンドでの **`--zone`** パラメーターの削除は、すべてのリージョンで有効です。  

**`--zone`** パラメーターの削除は、[Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

---

## <a name="routing-preference-and-tier"></a>ルーティングの優先順位と階層

Standard SKU の静的パブリック IPv4 アドレスでは、ルーティングの優先順位またはグローバル階層の機能がサポートされます。

# <a name="routing-preference"></a>[**ルーティングの優先順位**](#tab/routing-preference)

既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワーク経由でユーザーにトラフィックが配信されます。  

**インターネット** を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。代わりに転送 ISP ネットワークを使用して、コスト最適化された速度でトラフィックが配信されます。  

ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](routing-preference-overview.md)」を参照してください。

このコマンドによって、ルーティング設定の種類に **インターネット** が指定された標準のゾーン冗長パブリック IPv4 アドレスが作成されます。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**階層**](#tab/tier)

パブリック IP アドレスは、1 つのリージョンに関連付けられています。 **グローバル** 階層では 1 つの IP アドレスが、複数のリージョンにまたがって使用されます。 **グローバル** 階層は、リージョン間ロード バランサーのフロントエンドに必要です。  

詳細については、「[リージョン間ロード バランサー](../../load-balancer/cross-region-overview.md)」を参照してください。

次のコマンドでは、グローバル IPv4 アドレスが作成されます。 このアドレスは、リージョン間ロードバランサーのフロントエンドに関連付けることができます。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>グローバル階層アドレスでは、Availability Zones はサポートされません。

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションの使用を継続しない場合は、次の手順でパブリック IP アドレスを削除します。

1. ポータル上部の [検索] ボックスに「**リソース グループ**」と入力します。

2. 検索結果から **[リソース グループ]** を選択します。

3. **[QuickStartCreateIP-rg]** を選択します。

4. **[リソース グループの削除]** を選択します。

5. **[リソース グループ名を入力してください]** に「**QuickStartCreateIP-rg**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、パブリック IP プレフィックスの作成方法を学習してください。
> [!div class="nextstepaction"]
> [Azure CLI を使用してパブリック IP プレフィックスを作成する](create-public-ip-prefix-cli.md)
