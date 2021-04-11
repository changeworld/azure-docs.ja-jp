---
title: 'Azure ExpressRoute: Azure portal を使用して IPv6 サポートを追加する'
description: Azure portal を使用して Azure デプロイに接続するための IPv6 サポートを追加する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561663"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Azure portal を使用してプライベート ピアリングに対する IPv6 サポートを追加する (プレビュー)

この記事では、Azure portal を使用して Azure 内のリソースに ExpressRoute 経由で接続するための IPv6 サポートを追加する方法について説明します。 

> [!Note]
> この機能は現在、[Availability Zones がある Azure リージョン](../availability-zones/az-region.md#azure-regions-with-availability-zones)でのプレビューに使用できます。 したがって、ExpressRoute 回線は任意のピアリングの場所を使用して作成できますが、接続先である IPv6 ベースのデプロイは、Availability Zones があるリージョン内に存在している必要があります。

## <a name="register-for-public-preview"></a>パブリック プレビューに登録する
IPv6 サポートを追加する前に、まずサブスクリプションを登録する必要があります。 登録するには、Azure PowerShell から次のコマンドを実行します。

1.  Azure にサインインし、サブスクリプションを選択します。 これらのコマンドは、ExpressRoute 回線が含まれているサブスクリプションと、Azure デプロイが含まれているサブスクリプションに対して実行する必要があります (それらが異なる場合)。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 次のコマンドを使用して、サブスクリプションをパブリック プレビューに登録します。
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

リクエストは、2 から 3 営業日以内に ExpressRoute チームによって承認されます。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから [Azure portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 回線に IPv6 プライベート ピアリングを追加する

1. [ExpressRoute 回線を作成する](expressroute-howto-circuit-portal-resource-manager.md)か、変更する既存の回線に移動します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute 回線の一覧のスクリーンショット。":::

1. **[Azure プライベート]** のピアリング構成を選択します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Express Route の概要ページのスクリーンショット。":::

1. **[サブネット]** に対して [両方] を選択して、既存の IPv4 プライベート ピアリング構成に IPv6 プライベート ピアリングを追加するか、[IPv6] を選択して、新しい回線で IPv6 プライベート ピアリングのみを有効にします。 プライマリ リンクとセカンダリ リンク用に所有している /126 IPv6 サブネットのペアを指定します。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。 すべてのパラメーターを指定したら、ピアリング構成を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="プライベート ピアリング ページでの Ipv6 の追加のスクリーンショット。":::

1. 構成が正常に受け入れられると、次の例のようなものが表示されます。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="プライベート ピアリングに構成された Ipv6 のスクリーンショット。":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>既存の仮想ネットワークへの接続を更新する

IPv6 プライベート ピアリングを使用する Availability Zones があるリージョン内に Azure リソースの既存環境がある場合は、下の手順に従います。

1. ExpressRoute 回線が接続されている仮想ネットワークに移動します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="仮想ネットワークの一覧のスクリーンショット。":::

1. **[アドレス空間]** タブに移動し、仮想ネットワークに IPv6 アドレス空間を追加します。 アドレス空間を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="仮想ネットワークへの Ipv6 アドレス空間の追加のスクリーンショット。":::

1. **[サブネット]** タブに移動し、 **[GatewaySubnet]** を選択します。 **[Add IPv6 address space]\(IPv6 アドレス空間を追加する\)** チェックボックスをオンにし、サブネットの IPv6 アドレス空間を指定します。 ゲートウェイ IPv6 サブネットは /64 以上である必要があります。 すべてのパラメーターを指定したら、構成を **保存** します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="サブネットへの Ipv6 アドレス空間の追加のスクリーンショット。":::

1. 既存のゾーン冗長ゲートウェイがある場合は、ExpressRoute ゲートウェイに移動し、リソースを更新して IPv6 接続を有効にします。 それ以外の場合は、ゾーン冗長 SKU (ErGw1AZ、ErGw2AZ、ErGw3AZ) を使用して[仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-portal-resource-manager.md)します。 FastPath の使用を計画している場合は、ErGw3AZ を使用します。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="ゲートウェイの更新のスクリーンショット。":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>新しい仮想ネットワークへの接続を作成する

IPv6 プライベート ピアリングを使用して Availability Zones があるリージョン内の新しい Azure リソース セットに接続する場合は、下の手順に従います。

1. IPv4 と IPv6 の両方のアドレス空間を使用して、デュアルスタック仮想ネットワークを作成します。 詳細については、「[仮想ネットワークの作成](../virtual-network/quick-create-portal.md#create-a-virtual-network)」をご覧ください。

1. [デュアルスタック ゲートウェイ サブネットを作成します](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。

1. ゾーン冗長 SKU (ErGw1AZ、ErGw2AZ、ErGw3AZ) を使用して[仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)します。 FastPath を使用する場合は、ErGw3AZ を使用します (このオプションは ExpressRoute Direct を使用する回線でのみ利用できます)。

1. [仮想ネットワークを ExpressRoute 回線にリンクします](expressroute-howto-linkvnet-portal-resource-manager.md)。

## <a name="limitations"></a>制限事項
IPv6 サポートは、Availability Zones があるリージョン内のデプロイへの接続に使用できますが、次のユース ケースはサポートされていません。

* Azure 内のデプロイへの接続に AZ ExpressRoute ゲートウェイ SKU 以外を使用
* AZ リージョン内以外のデプロイへの接続
* ExpressRoute 回線間の Global Reach 接続
* ExpressRoute と仮想 WAN の使用
* ExpressRoute Direct 以外の回線の FastPath
* ピアリングの場所 (ドバイ) に回線がある FastPath
* VPN Gateway との共存

## <a name="next-steps"></a>次のステップ

ExpressRoute に関する問題のトラブルシューティングを行うには、次の記事を参照してください。

* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)