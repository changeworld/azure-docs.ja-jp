---
title: Azure VPN Gateway で NAT を構成する
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway で NAT を構成する方法を学習します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/24/2021
ms.author: yushwang
ms.openlocfilehash: 8b9d3d298986aa4aae6ead4e9733c9b0994b1f91
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214067"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways-preview"></a>Azure VPN Gateway で NAT を構成する方法 (プレビュー)

この記事は、Azure portal を使用して Azure VPN Gateway で NAT (ネットワークアドレス変換) を構成するのに役立ちます。

## <a name="about-nat"></a><a name="about"></a>NAT について

NAT は、IP パケット内の IP アドレスを別の IP アドレスに変換するメカニズムを定義するものです。 これは、一般的に、IP アドレス範囲が重複するネットワークを接続するために使用されます。 ネットワークに接続するゲートウェイ デバイスの NAT ルールまたはポリシーは、ネットワーク上のアドレス変換のアドレス マッピングを指定します。

Azure VPN Gateway での NAT サポートの詳細については、「[Azure VPN Gateways における NAT について](nat-overview.md)」を参照してください。

> [!IMPORTANT] 
> Azure NAT for VPN Gateway は、現在、プレビュー段階です。 
> * この記事の「 **[プレビューの制限事項](#limits)** 」セクションを必ずお読みください。
> * Azure VPN Gateway NAT では、静的な 1:1 の NAT ルールのみがサポートされます。 動的な NAT ルールはサポートされません。
> * NAT がサポートされる SKU は、VpnGw2 から 5、VpnGw2AZ から 5AZ です。

## <a name="getting-started"></a>はじめに

この記事の各パートに従うと、ネットワーク接続で NAT を構成するための基本的な構成要素を形成できます。 3 つのパートをすべて完了すると、図 1 のようなトポロジが構築されます。

### <a name="diagram-1"></a><a name="diagram"></a>図 1

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="図 1 のスクリーンショット。" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>前提条件

* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* 「[プレビューの制限事項](#limits)」をご確認ください。

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>パート 1: VNet およびゲートウェイを作成する

このセクションでは、[図 1](#diagram) に示されているリソースに対応する仮想ネットワーク、VPN ゲートウェイ、ローカル ネットワーク ゲートウェイのリソースを作成します。

これらのリソースを作成するには、[サイト間に関するチュートリアル](tutorial-site-to-site-portal.md)にある手順を使用します。 その記事の次のセクションを完了します。ただし、接続は作成しないでください。

* [VNet](tutorial-site-to-site-portal.md#CreatVNet)
* [VPN Gateway](tutorial-site-to-site-portal.md#VNetGateway)
* [ローカル ネットワーク ゲートウェイ](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [VPN デバイスの構成](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  次の記事の手順を使用する場合は、これらの記事で **接続** リソースを作成しないでください。 VNet、ブランチ 1、ブランチ 2 の間で IP アドレス空間が同じであるため、操作は失敗します。 次のセクションの手順を使用して、NAT ルールを作成した後、その NAT ルールを使用して接続を作成します。
>


次のスクリーンショットは、作成するリソースの例を示しています。

* **VNet**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="VNet アドレス空間を示すスクリーンショット。" lightbox="./media/nat-howto/vnet.png":::
* **VPN Gateway**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="ゲートウェイを示すスクリーンショット。" lightbox="./media/nat-howto/vpn-gateway.png":::
* **ブランチ 1 ローカル ネットワーク ゲートウェイ**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="ブランチ 1 ローカル ネットワーク ゲートウェイを示すスクリーンショット。" lightbox="./media/nat-howto/branch-1.png" :::

* **ブランチ 2 ローカル ネットワーク ゲートウェイ**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="ブランチ 2 ローカル ネットワーク ゲートウェイを示すスクリーンショット。" lightbox="./media/nat-howto/branch-2.png":::

   > [!IMPORTANT] 
   > プレビュー期間中、ローカル ネットワーク ゲートウェイのアドレス空間が VNet アドレス空間と同じであるかそれよりも小さい場合、**BGP** を使用し、ローカル ネットワーク ゲートウェイの [アドレス空間] フィールドを **空白** のままにします。 プレビュー期間中、このシナリオでは静的ルーティング (非 BGP) はサポートされていません。
   >

## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>パート 2: NAT ルールを作成する

接続を作成する前に、VPN ゲートウェイで NAT ルールを作成して保存する必要があります。 次の表に、必要な NAT ルールを示します。 トポロジについては、[図 1](#diagram) を参照してください。

**NAT ルールの表**

| 名前     | 型   | モード        | 内部    | 外部     | 接続          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| VNet     | スタティック | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | 両方の接続    | 
| Branch_1 | スタティック | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | ブランチ 1 接続 |
| Branch_2 | スタティック | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | ブランチ 2 接続 |

次の手順を使用して、VPN ゲートウェイ上のすべての NAT ルールを作成します。

1. Azure portal で、 **[Virtual Network ゲートウェイ]** リソース ページに移動して、 **[NAT ルール (プレビュー)]** を選択します。
1. 上記の「**NAT ルールの表**」を使用して、値を入力します。

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="NAT ルールを示すスクリーンショット。" lightbox="./media/nat-howto/nat-rules.png":::
1. **[保存]** をクリックして、NAT ルールを VPN ゲートウェイ リソースに保存します。 この操作は、完了するまで最大 10 分かかることがあります。

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>パート 3: 接続を作成し、NAT ルールをリンクする

このセクションでは、接続を作成し、NAT ルールを接続に関連付けて、[図 1](#diagram) のサンプル トポロジを実装します。

### <a name="1-create-connections"></a>1. 接続を作成する

[サイト間接続の作成](tutorial-site-to-site-portal.md)に関する記事にある手順に従って、次に示すように 2 つの接続を作成します。

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="[接続] ページを示すスクリーンショット。" lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. NAT ルールを接続に関連付ける

この手順では、NAT ルールを各接続リソースに関連付けます。

1. Azure portal で接続リソースに移動し、 **[構成]** を選択します。

1. [イングレス NAT ルール] で、先ほど作成した NAT ルールを選択します。

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="構成済みの NAT ルールを示すスクリーンショット。" lightbox="./media/nat-howto/config-nat.png":::

1. **[保存]** をクリックして、構成を接続リソースに適用します。

1. 手順を繰り返して、他の接続リソースに NAT ルールを適用します。

1. BGP を使用する場合は、[NAT ルール] ページで **[Enable BGP Route Translation]\(BGP ルート変換を有効にする\)** を選択し、 **[保存]** をクリックします。 表には、各 NAT ルールにリンクされた接続が表示されていることにご注意ください。

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="BGP を有効にする方法を示すスクリーンショット。" lightbox="./media/nat-howto/nat-rules-linked.png":::

これらの手順を完了すると、[図 1](#diagram) に示すトポロジと一致するセットアップが作成されます。

## <a name="preview-limitations"></a><a name ="limits"></a>プレビューの制限事項

> [!IMPORTANT] 
> NAT 機能のプレビュー期間中、いくつかの制約があります。 これらの一部は、GA 前に解消される予定です。
>

* Azure VPN Gateway NAT では、静的な 1:1 の NAT ルールのみがサポートされます。 動的な NAT ルールはサポートされません。
* NAT がサポートされる SKU は、VpnGw2 から 5、および VpnGw2AZ から 5AZ です。
* NAT は、IPsec/IKE クロスプレミス接続でのみサポートされます。 VNet 間接続や P2S 接続はサポートされません。
* 接続の作成プロセスで NAT ルールを接続リソースに関連付けることはできません。 最初に接続リソースを作成し、[接続の構成] ページで NAT ルールを関連付けます。
* プレビュー期間中、ローカル ネットワーク ゲートウェイのアドレス空間が VNet アドレス空間と同じであるかそれよりも小さい場合、**BGP** を使用し、ローカル ネットワーク ゲートウェイの [アドレス空間] フィールドを **空白** のままにします。 ローカル ネットワーク ゲートウェイと VNet の間でアドレス競合が発生するため、静的ルーティング (非 BGP) はサポートされて **いません**。
* [図 1](#diagram) に示すように、異なるローカル ネットワーク ゲートウェイ (オンプレミス ネットワークまたはブランチ) のアドレス空間は、重複しないプレフィックスにマップするために *IngressSNAT* ルールと同じにすることができます。
* NAT ルールは、 *[Use Policy Based Traffic Selectors]\(ポリシー ベースのトラフィック セレクターを使用する\)* が有効になっている接続ではサポートされません。

## <a name="next-steps"></a>次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/windows/quick-create-portal.md) に関するページを参照してください。
