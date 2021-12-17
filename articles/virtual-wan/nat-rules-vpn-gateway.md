---
title: ゲートウェイの VPN NAT 規則を構成する
titleSuffix: Azure Virtual WAN
description: VWAN VPN ゲートウェイの NAT 規則を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6613952b5d7811c187123963a6e611602aabb5dc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113127692"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway"></a>仮想 WAN VPN ゲートウェイの NAT 規則を構成する

仮想 WAN VPN ゲートウェイを静的な 1 対 1 の NAT 規則を使用して構成できます。 NAT 規則には、IP アドレスの 1 対 1 の変換を設定するメカニズムが用意されています。 NAT を使用して、互換性のないまたは重複する IP アドレスを持つ 2 つの IP ネットワークを相互接続することができます。 代表的なシナリオは、重複する IP を持つブランチが Azure VNet リソースへのアクセスを必要とすることです。

この構成では、フロー テーブルを使用して、外部 (ホスト) IP アドレスから仮想ネットワーク内のエンドポイント (仮想マシン、コンピューター、コンテナーなど) に関連する内部 IP アドレスにトラフィックをルーティングします。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="アーキテクチャを示す図。":::
   
NAT を使用するには、VPN デバイスで Any-to-Any (ワイルドカード) のトラフィック セレクターを使用する必要があります。 ポリシーベース (ナロー) の トラフィック セレクターは、NAT 構成と組み合わせて使用することはできません。

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT 規則を構成する

VPN ゲートウェイ設定における NAT 規則を、いつでも構成および表示できます。

> [!NOTE]
> サイト間 NAT は、ポリシーベースのトラフィック セレクターが使用されるサイト間 VPN 接続ではサポートされていません。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="NAT 規則の編集方法を示すスクリーンショット。"lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. ご使用の仮想ハブに移動します。
1. **[VPN (サイト対サイト)]** を選択します。
1. **[NAT 規則 (編集)]** を選択します。
1. **[Edit NAT Rule]\(NAT 規則の編集\)** ページで、次の値を使用して NAT 規則の **追加、編集、削除** を行うことができます。

   * **名前:** NAT 規則の一意の名前。
   * **タイプ:** 静的。 静的な 1 対 1 の NAT では、内部アドレスと外部アドレスの間に 1 対 1 のリレーションシップが確立されます。
   * **モード:** IngressSnat または EgressSnat。  
      * IngressSnat モード (イングレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイに入るトラフィックに適用されます。 
      * EgressSnat モード (エグレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイを出ていくトラフィックに適用されます。 
   * **InternalMapping:** 外部 IP のセットにマップされる、内部ネットワーク上の送信元 IP のアドレス プレフィックス範囲。 言い換えると、NAT 前のアドレス プレフィックス範囲です。
   * **ExternalMapping:** 送信元 IP がマップされる、外部ネットワーク上の宛先 IP のアドレス プレフィックス範囲。 言い換えると、NAT 後のアドレス プレフィックス範囲です。
   * **リンク接続:** VPN サイトを Azure Virtual WAN ハブのサイト対サイト VPN ゲートウェイに仮想的に接続する接続リソース。
 
> [!NOTE]
> サイト間 VPN ゲートウェイで、BGP を介して変換された (**ExternalMapping**) アドレス プレフィックスをアドバタイズする場合は、 **[Enable BGP Translation]\(BGP 変換を有効にする\)** ボタンをクリックします。そうすることによって、オンプレミスはエグレス規則の NAT 後の範囲を自動的に学習し、Azure (Virtual WAN ハブ、接続された仮想ネットワーク、VPN、ExpressRoute ブランチ) はイングレス規則の NAT 後の範囲を自動的に学習します。 **[Enable BGP Translation]\(BGP 変換を有効にする\)** 設定は、Virtual WAN ハブのサイト間 VPN ゲートウェイの全 NAT 規則に適用されることに注意してください。 

## <a name="example-configurations"></a><a name="examples"></a>構成例

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>イングレス SNAT (BGP が有効になっている VPN サイト)

**イングレス SNAT 規則** は、Virtual WAN サイト間 VPN ゲートウェイを介して Azure に入るパケットに適用されます。 このシナリオでは、2 つのサイト間 VPN ブランチを Azure に接続します。 VPN サイト 1 はリンク A 経由で接続し、VPN サイト 2 はリンク B を介して接続します。各サイトは、10.30.0.0/24 という同じアドレス空間を持っています。

この例では、サイト 1 を 127.30.0.0.0/24 に NAT します。 Virtual WAN スポークの仮想ネットワークとブランチなどは、この NAT 後のアドレス空間を自動的に学習します。 

次の図は、予測される最終結果を示しています。

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="BGP が有効になっているサイトのイングレス モード NAT を示す図。":::

1. NAT 規則を指定します。

   サイト間 VPN ゲートウェイが、アドレス空間 （10.30.0.0/24 など） が重複している 2 つのブランチを確実に区別できるように、NAT 規則を指定します。 この例では、VPN サイト 1 のリンク A に注目します。

   次の NAT 規則を設定して、リンク A に関連付けることができます。これは静的な NAT 規則であるため、 **InternalMapping** と **ExternalMapping** のアドレス空間には同じ数の IP アドレスが含まれています。

   * **名前:** IngressRule01
   * **種類:** 静的
   * **モード:** IngressSnat
   * **InternalMapping::** 10.30.0.0/24
   * **ExternalMapping::** 172.30.0.0/24
   * **リンク接続:** リンク A

 2.  **BGP ルートの変換** を「有効」に切り替えます。

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="BGP 変換を有効にする方法を示すスクリーンショット。":::


3. サイト間 VPN ゲートウェイがオンプレミスの BGP ピアとピアリングできることを確認します。

      この例では、**イングレス NAT 規則** で 10.30.0.132 を 127.30.0.132 に変換する必要があります。 これを行うには、[VPN サイトの編集] をクリックして VPN サイト リンク A の BGP アドレスを構成し、変換された BGP ピア アドレス (127.30.0.132) を反映させます。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="BGP ピアリング IP を変更する方法を示すスクリーンショット。"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"></a>VPN サイトが BGP 経由で接続する場合の考慮事項
* 内部と外部の両方のマッピングのサブネット サイズは、静的な 1 対の 1 NAT の場合は同じである必要があります。
* **BGP 変換** が有効になっている場合、サイト間 VPN ゲートウェイは、**エグレス NAT 規則** の **Extermal Mapping** をオンプレミスに、**イングレス NAT 規則** の **External Mapping** を Azure (Virtual WAN ハブ、接続されているスポーク仮想ネットワーク、接続されている VPN/ExpresRoute) に自動的にアドバタイズします。 **BGP 変換** が無効になっている場合、変換されたルートはオンプレミスに自動的にはアドバタイズされません。 そのため、オンプレミスの BGP スピーカーは、その VPN サイト リンク接続に関連付けられている **イングレス NAT**  規則の NAT 後の (**ExternalMapping**) 範囲をアドバタイズするように構成する必要があります。 同様に、**エグレス NAT 規則** の NAT 後の (**ExternalMapping**) 範囲のルートをオンプレミス デバイスに適用する必要があります。
* オンプレミスの BGP ピア IP アドレスが **イングレス NAT 規則** の **Internal Mapping** に含まれて **いる場合**、サイト間 VPN ゲートウェイは、オンプレミスの BGP ピア IP アドレスを自動的に変換します。 その結果、VPN サイトの **リンク接続 BGP アドレス** には、NAT で変換されたアドレス (External Mapping の一部) が反映されている必要があります。 

    たとえば、オンプレミスの BGP IP アドレスが 10.30.0.133 で、10.30.0.0/24 を 127.30.0.0/24 に変換する **イングレス NAT 規則** がある場合、VPN サイトの **リンク接続 BGP アドレス** は、変換されたアドレス (127.30.0.133) になるように構成する必要があります。

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>イングレス SNAT (静的に構成されたルートを持つ VPN サイト)

**イングレス SNAT 規則** は、Virtual WAN サイト間 VPN ゲートウェイを介して Azure に入るパケットに適用されます。 このシナリオでは、2 つのサイト間 VPN ブランチを Azure に接続します。 VPN サイト 1 はリンク A 経由で接続し、VPN サイト 2 はリンク B を介して接続します。各サイトは、10.30.0.0/24 という同じアドレス空間を持っています。

この例では、VPN サイト 1 を 127.30.0.0.0/24 に NAT します。 ただし、VPN サイトは BGP 経由でサイト間 VPN ゲートウェイに接続されていないため、構成手順は BGP を有効にした例とは若干異なります。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="静的ルーティングを使用する VPN サイトのダイアグラム構成を示すスクリーンショット。":::


1. NAT 規則を指定します。

    サイト間 VPN ゲートウェイが、同一のアドレス空間 （10.30.0.0/24） を持つ 2 つのブランチを確実に区別できるように、NAT 規則を指定します。 この例では、VPN サイト 1 のリンク A に注目します。

    次の NAT 規則を設定して、VPN サイト 1 のリンク A に関連付けることができます。 これは静的な NAT 規則であるため、**InternalMapping** と **ExternalMapping** のアドレス空間には同じ数の IP アドレスが含まれています。

    * **名前**: IngressRule01
    * **種類**: 静的
    * **モード**: IngressSnat
    * **InternalMapping:** : 10.30.0.0/24
    * **ExternalMapping**: 172.30.0.0/24
    * **リンク接続**: リンク A

2. VPN サイト 1 の [プライベートアドレス空間] フィールドを編集して、サイト対サイト VPN ゲートウェイが NAT 後の範囲 (172.30.0.0/24) を学習するようにします

   * サイト間 VPN ゲートウェイを含む仮想ハブ リソースに移動します。 仮想ハブ ページの [接続] で、 [VPN (Site-to-Site)]\(VPN (サイト間)\) を選択します。

   * リンク A を経由して Virtual WAN ハブに接続されている VPN サイトを選択します。 [サイトの編集] を選択し、VPN サイトのプライベート アドレス空間として「172.30.0.0/24」と入力します。 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="VPN サイトのプライベート アドレス空間を編集する方法を示すスクリーンショット" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>VPN サイトが静的に構成されている (BGP 経由で接続されていない) 場合の考慮事項
* 内部と外部の両方のマッピングのサブネット サイズは、静的な 1 対の 1 NAT の場合は同じである必要があります。
* Azure portal で VPN サイトを編集して、**イングレス NAT 規則** の **ExternalMapping** にあるプレフィックスを [プライベート アドレス空間] フィールドに追加します。 
* **エグレス NAT 規則** を含む構成では、**エグレス NAT 規則** の **ExternalMapping** を使用するルート ポリシーまたは静的ルートをオンプレミス デバイスに適用する必要があります。

### <a name="packet-flow"></a>パケット フロー

前の例では、オンプレミスのデバイスがスポーク仮想ネットワークのリソースに接続しようとしています。 パケット フローは次のようになります。 NAT 変換は太字で示しています。

1. オンプレミスからのトラフィックが開始されます。
   * 送信元 IP アドレス: **10.30.0.4**
   * 宛先 IP アドレス: 10.200.0.4
1. トラフィックはサイト間ゲートウェイに入り、NAT 規則を使用して変換され、スポークに送信されます。
   * 送信元 IP アドレス: **172.30.0.4**
   * 宛先 IP アドレス: 10.200.0.4
1. スポークからの応答が開始されます。
   * 送信元 IP アドレス: 10.200.0.4
   * 宛先 IP アドレス: **172.30.0.4**
1. トラフィックはサイト間 VPN ゲートウェイに入り、変換は元に戻され、オンプレミスに送信されます。
   * 送信元 IP アドレス: 10.200.0.4
   * 宛先 IP アドレス: **10.30.0.4**

### <a name="verification-checks"></a>検証チェック

このセクションでは、構成が適切に設定されていることを確認するためのチェックを示します。 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable、規則、ルートを検証する

Virtual WAN 内のブランチは **DefaultRouteTable** に関連付けられています。これは、すべてのブランチ接続は DefaultRouteTable 内に設定されたルートを学習することを意味します。 DefaultRouteTable の有効なルートに、変換されたプレフィックスを含む NAT 規則があることがわかります。

前の例では、次のようになります。

* **プレフィックス:** 172.30.0.0/24  
* **ネクスト ホップの種類:** VPN_S2S_Gateway
* **ネクスト ホップ:** VPN_S2S_Gateway リソース

#### <a name="validate-address-prefixes"></a>アドレス プレフィックスを検証する

この例は、DefaultRouteTable に関連付けられている仮想ネットワーク内のリソースに適用されます。

Virtual WAN ハブに接続されているスポーク仮想ネットワーク内にある仮想マシンのネットワーク インターフェイス カード (NIC) の **有効なルート** には、**イングレス NAT 規則** に指定された **ExternalMapping** のアドレス プレフィックスも含まれている必要があります。 

オンプレミスのデバイスには、**エグレス NAT 規則** の **ExternalMapping** 内に含まれるプレフィックスのルートも含まれている必要があります。 

####  <a name="common-configuration-patterns"></a>一般的な構成パターン 

> [!NOTE]
> サイト間 NAT は、ポリシーベースのトラフィック セレクターが使用されるサイト間 VPN 接続ではサポートされていません。

次の表は、サイト間 VPN ゲートウェイでさまざまな種類の NAT 規則を構成するときに発生する一般的な構成パターンを示しています。  

| VPN サイトの種類 | イングレス NAT 規則 | エグレス NAT 規則
|--- |--- | ---|
|静的に構成されたルートを持つ VPN サイト |VPN サイトの [プライベート アドレス空間] を 編集して、NAT 規則の **ExternalMapping** を含めます| オンプレミス デバイスで NAT 規則の **ExternalMapping** のルートを適用します。|
|VPN サイト (BGP 変換が有効)|BGP ピアの **ExternalMapping** アドレスを VPN サイトのリンク接続の BGP アドレスに設定します。  | 特に考慮事項はありません。 |
| VPN サイト (BGP 変換が無効) | オンプレミスの BGP スピーカーによって NAT 規則の **ExternalMapping** のプレフィックスがアドバタイズされていることを確認します。 また、BGP ピアの ExternalMapping アドレスを VPN サイトのリンク接続の BGP アドレスに設定します。| オンプレミス デバイスで NAT 規則の **ExternalMapping** のルートを適用します。|

## <a name="next-steps"></a>次のステップ

サイト対サイト構成について詳しくは、 [仮想 WAN サイト対サイト接続の構成](virtual-wan-site-to-site-portal.md)に関するページをご覧ください。
