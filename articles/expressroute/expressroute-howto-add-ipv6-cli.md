---
title: 'Azure ExpressRoute: Azure CLI を使用して IPv6 サポートを追加する'
description: Azure CLI を使用して Azure デプロイに接続するための IPv6 サポートを追加する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 9341d61229ed5b28eed838271fd0108c1a45d8b5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228978"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli"></a>Azure CLI を使用してプライベート ピアリングに対する IPv6 サポートを追加する

この記事では、Azure CLI を使用して Azure 内のリソースに ExpressRoute 経由で接続するための IPv6 サポートを追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。
* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 回線に IPv6 プライベート ピアリングを追加する

1. [ExpressRoute 回線を作成](howto-circuit-cli.md)するか、既存の回線を使用します。 以下を実行して、回線の詳細を表示します。

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. 以下を実行して、回線のプライベート ピアリング構成を表示します。

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. 既存の IPv4 プライベート ピアリング構成に IPv6 プライベート ピアリングを追加します。 プライマリ リンクとセカンダリ リンク用に所有している /126 IPv6 サブネットのペアを指定します。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>既存の仮想ネットワークへの接続を更新する

IPv6 プライベート ピアリングを使用する Azure リソースの既存環境がある場合は、下の手順に従います。

1. IPv6 アドレス空間を、ご自身の ExpressRoute 回線が接続されている仮想ネットワークに追加します。

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. ゲートウェイ サブネットに IPv6 アドレス空間を追加します。 ゲートウェイ IPv6 サブネットは /64 以上である必要があります。

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. 既存のゾーン冗長ゲートウェイがある場合は、次を実行して IPv6 接続を有効にします (変更が反映されるまでに最大 1 時間かかることがあります)。 それ以外の場合は、SKU を使用して[仮想ネットワーク ゲートウェイを作成します](expressroute-howto-add-gateway-resource-manager.md)。 FastPath を使用する場合は、UltraPerformance または ErGw3AZ を使用します (これは、ExpressRoute Direct を使用する回線でのみ利用できます)。

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> 既存のゲートウェイがゾーン冗長ではなく (つまり、Standard、High Performance、Ultra Performance SKU のいずれかで)、Basic SKU のパブリック IP アドレスが使用されている場合、任意の SKU と、Standard、Static パブリック IP アドレスを使用してゲートウェイを削除して[再作成](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)する必要があります。

## <a name="create-a-connection-to-a-new-virtual-network"></a>新しい仮想ネットワークへの接続を作成する

IPv6 プライベート ピアリングを使用して新しい Azure リソース セットに接続する場合は、下の手順に従います。

1. IPv4 と IPv6 の両方のアドレス空間を使用して、デュアルスタック仮想ネットワークを作成します。 詳細については、「[仮想ネットワークの作成](../virtual-network/quick-create-cli.md)」をご覧ください。

2. [デュアルスタック ゲートウェイ サブネットを作成します](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

3. SKU を使用して[仮想ネットワーク ゲートウェイを作成します](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。 FastPath を使用する場合は、UltraPerformance または ErGw3AZ を使用します (これは、ExpressRoute Direct を使用する回線でのみ利用できます)。

4. [仮想ネットワークを ExpressRoute 回線にリンクします](howto-linkvnet-cli.md)。

## <a name="limitations"></a>制限事項
IPv6 サポートは、グローバル Azure リージョン内のデプロイへの接続に使用できますが、次のユース ケースはサポートされていません。

* ゾーン冗長ではない "*既存の*" ExpressRoute ゲートウェイへの接続。 標準の静的 IP アドレスを使用して "*新しく*" 作成された任意の SKU の ExpressRoute ゲートウェイは (ゾーン冗長かどうかを問わず)、デュアルスタック ExpressRoute 接続に使用できることに注意してください
* ExpressRoute 回線間の Global Reach 接続
* ExpressRoute と仮想 WAN の使用
* ExpressRoute Direct 以外の回線の FastPath
* ピアリングの場所 (ドバイ) に回線がある FastPath
* VPN Gateway との共存

## <a name="next-steps"></a>次のステップ

ExpressRoute に関する問題のトラブルシューティングを行うには、次の記事を参照してください。

* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
