---
title: vWAN for Azure VMware Solution でサイト間 VPN を構成する
description: Azure VMware Solution への VPN (IPsec IKEv1 および IKEv2) サイト間トンネルを確立する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: f3ed10e2a67e40fe8f1124a88fd8e11b24b71986
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324999"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>vWAN for Azure VMware Solution でサイト間 VPN を構成する

この記事では、Microsoft Azure Virtual WAN ハブで終端する VPN (IPsec IKE v1 および IKE v2) サイト間トンネルを確立します。 ハブには、Azure VMware Solution ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイが含まれています。 これにより、オンプレミスの VPN デバイスは Azure VMware Solution エンドポイントに接続されます。

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN サイト間トンネル アーキテクチャを示す図。" border="false":::

## <a name="prerequisites"></a>前提条件
オンプレミスの VPN デバイスで終端する公開 IP アドレスが必要です。

## <a name="create-an-azure-virtual-wan"></a>Azure Virtual WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>仮想ハブを作成する

仮想ハブは、仮想 WAN によって作成および使用される仮想ネットワークです。 リージョン内の Virtual WAN ネットワークの核となります。  サイト間および ExpressRoute のゲートウェイを含めることができます。 

>[!TIP]
>[既存のハブにゲートウェイを作成する](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)こともできます。


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>VPN ゲートウェイの作成 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>サイト間 VPN を作成する

1. Azure portal で、先ほど作成した仮想 WAN を選択します。

2. 仮想ハブの **[概要]** で、 **[接続]**  >  **[VPN (サイト間)]**  >  **[VPN サイトの新規作成]** を選択します。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="[VPN (Site-to-site)]\(VPN (サイト間)\) と [VPN サイトの新規作成] が選択されている仮想ハブの [概要] ページのスクリーンショット。":::  
 
3. **[基本]** タブで、必須フィールドに入力します。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="[基本] タブが開いている [VPN サイトの作成] ページを示すスクリーンショット。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **[リージョン]** - 旧称は場所。 このサイト リソースを作成する場所です。
   
   * **[名前]** - オンプレミスのサイトの呼称。
   
   * **[デバイス ベンダー]** - VPN デバイス ベンダーの名前 (例: Citrix、Cisco、Barracuda)。 これにより、Azure チームがお客様の環境をよりよく理解し、将来の最適化の可能性を追加したり、トラブルシューティングに役立てることができます。

   * **[プライベート アドレス空間]** - オンプレミス サイトにある CIDR の IP アドレス空間です。 このアドレス空間宛てのトラフィックは、ローカル サイトにルーティングされます。 CIDR ブロックは、サイトで [BGP](../vpn-gateway/bgp-howto.md) が有効になっていない場合にのみ必要です。
    
   >[!NOTE]
   >サイトを作成した後にアドレス空間を編集した場合 (追加のアドレス空間を追加した場合など)、コンポーネントの再作成中での有効なルートの更新には、8 分から 10 分かかることがあります。


1. ブランチにある物理的なリンクに関する情報を追加するには、 **[リンク]** を選択します。 Virtual WAN パートナーの CPE デバイスがある場合は、システムから設定されたブランチ情報のアップロードの一部として、この情報が Azure と交換されるかどうかを確認します。

   リンクとプロバイダーの名前を指定すると、ハブの一部として最終的に作成される可能性のあるゲートウェイの数がいくつになっても区別できます。  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) と自律システム番号 (ASN) は、組織内で一意である必要があります。 BGP は、Azure VMware Solution とオンプレミス サーバーの両方がトンネルを介してルートをアドバタイズするようにします。 無効にした場合、アドバタイズする必要のあるサブネットは手動で保守する必要があります。 サブネットが欠落している場合、HCX はサービス メッシュの形成に失敗します。 
 
   >[!IMPORTANT]
   >既定では、Azure により、Azure VPN ゲートウェイの Azure BGP IP アドレスとして、GatewaySubnet プレフィックス範囲のプライベート IP アドレスが自動的に割り当てられます。 オンプレミスの VPN デバイスにより、BGP IP として APIPA アドレス (169.254.0.1 から 169.254.255.254) が使用される場合は、カスタム Azure APIPA BGP アドレスが必要です。 対応するローカル ネットワーク ゲートウェイ リソース (オンプレミス ネットワーク) に、BGP ピア IP として APIPA アドレスがある場合、Azure VPN Gateway により、カスタム APIPA アドレスが選択されます。 ローカル ネットワーク ゲートウェイによって (APIPA ではなく) 通常の IP アドレスが使用される場合は、Azure VPN Gateway によって GatewaySubnet 範囲のプライベート IP アドレスに戻されます。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="[リンク] タブが開いている [VPN サイトの作成] ページを示すスクリーンショット。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. **[確認と作成]** を選択します。 

1. 目的の仮想ハブに移動し、 **[ハブの関連付け]** の選択を解除して VPN サイトをハブに接続します。
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="[Connect to this hub]\(このハブに接続する\) を示すスクリーンショット。" lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>(任意) ポリシーベースの VPN サイト間トンネルを作成する

>[!IMPORTANT]
>これは省略可能な手順であり、ポリシーベースの VＰＮ にのみ適用されます。 

[ポリシーベースの VPN の設定](../virtual-wan/virtual-wan-custom-ipsec-portal.md)では、ハブの範囲を含め、オンプレミスおよび Azure VMware Solution のネットワークを指定する必要があります。  これらの範囲により、ポリシーベースの VPN トンネルのオンプレミス エンドポイントの暗号化ドメインが指定されます。  Azure VMware Solution 側では、ポリシーベースのトラフィック セレクター インジケーターを有効にすることのみが必要です。 

1. Azure portal で、Virtual WAN ハブ サイトに移動し、 **[接続]** で **[VPN (サイト間)]** を選択します。

2. カスタム IPsec ポリシーを設定する VPN サイトを選択します。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="顧客の IPsec ポリシーを設定するための既存の VPN サイトを示すスクリーンショット。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. VPN サイト名を選択して、右端の **詳細** (...)、 **[VPN 接続を編集する]** の順に選択します。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="既存の VPN サイトのコンテキスト メニューを示すスクリーンショット。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - インターネット プロトコル セキュリティ (IPSec) で **[カスタム]** を選択します。

   - ポリシーベースのトラフィック セレクターを使用して **[有効にする]** を選択します

   - **[IKE フェーズ 1]** と **[IKE フェーズ 2 (ipsec)]** の詳細を指定します。 

4. IPsec の設定を既定値からカスタムに変更して、IPsec ポリシーをカスタマイズします。 次に、 **[保存]** を選択します。

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="既存の VPN サイトを示すスクリーンショット。" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   ポリシーベースの暗号化ドメインの一部であるトラフィック セレクターまたはサブネットは、次のようになります。
    
   - Virtual WAN ハブ `/24`

   - Azure VMware Solution のプライベート クラウド `/22`

   - 接続されている Azure 仮想ネットワーク (存在する場合)

## <a name="connect-your-vpn-site-to-the-hub"></a>VPN サイトをハブに接続する

1. VPN サイト名を選択して、 **[VPN サイトの接続]** を選択します。 

1. **[事前共有キー]** フィールドに、オンプレミス エンドポイントに対して以前に定義したキーを入力します。 

   >[!TIP]
   >以前に定義したキーがない場合は、このフィールドは空白のままでかまいません。 キーが自動的に生成されます。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="[サイトの接続] ペインを示すスクリーンショット。仮想ハブの事前共有キーと関連設定を指定できます。"::: 

1. ハブにファイアウォールをデプロイしていて、それが次のホップである場合は、 **[既定のルートを伝達]** オプションを **[有効]** に設定します。 

   有効にすると、Virtual WAN ハブが接続に対して伝達を行うのは、ハブにファイアウォールをデプロイしたときにハブにより既定のルートが既に学習されている場合、または接続されている別のサイトでトンネリングが強制的に有効にされている場合のみです。 既定のルートの起点は Virtual WAN ハブ内にありません。  

1. **[接続]** を選択します。 数分後、サイトに接続と接続性の状態が表示されます。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="サイト間接続と接続性の状態を示すスクリーンショット。" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   **接続状態:** VPN サイトを Azure ハブの VPN ゲートウェイに接続する Azure リソースの状態です。 このコントロール プレーンの操作が正常に終了すると、Azure VPN ゲートウェイとオンプレミスの VPN デバイスは、接続を確立します。

   **接続性の状態**: ハブ内の Azure の VPN ゲートウェイと VPN サイト間の実際の接続性 (データ パス) の状態です。 次のいずれかの状態が表示されます。

    * **不明**: 通常、バックエンド システムが別の状態に移行しようとしている場合に表示されます。
    * **接続中**: Azure VPN ゲートウェイは、実際のオンプレミスの VPN サイトに接続しようとしています。
    * **接続済み**: Azure VPN ゲートウェイとオンプレミスの VPN サイトの間に接続が確立されています。
    * **切断済み**: 通常、何らかの理由 (オンプレミスまたは Azure 内) で切断されている場合に表示されます。



1. VPN 構成ファイルをダウンロードして、オンプレミスのエンドポイントに適用します。  
   
   1. [VPN (サイト間)] ページの上部にある **[VPN 構成のダウンロード]** を選択します。Azure により、リソース グループ 'microsoft-network-\[location\]' にストレージ アカウントが作成されます。この location は WAN の場所です。 VPN デバイスに構成を適用した後は、このストレージ アカウントを削除できます。

   1. 作成されたら、リンクを選択してダウンロードします。 

   1. オンプレミスの VPN デバイスに構成を適用します。

   構成ファイルの詳細については、「[VPN デバイス構成ファイルについて](../virtual-wan/virtual-wan-site-to-site-portal.md#about-the-vpn-device-configuration-file)」を参照してください。

1. Virtual WAN ハブで Azure VMware Solution ExpressRoute に修正プログラムを適用します。 

   >[!IMPORTANT]
   >まず、プライベート クラウドを作成してから、プラットフォームにパッチを適用する必要があります。 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. Virtual WAN ハブで Azure VMware Solution と VPN ゲートウェイをリンクします。 前の手順の認可キーと ExpressRoute ID (ピア回線の URI) を使用します。

   1. ExpressRoute ゲートウェイを選択し、 **[認可キーを利用する]** を選択します。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="プライベート クラウドの [ExpressRoute] ページで [認可キーを利用する] が選択されているスクリーンショット。":::

   1. 認可キーを **[認可キー]** フィールドに貼り付けます。

   1. ExpressRoute ID を **[ピア回線の URI]** フィールドに貼り付けます。 

   1. **[この ExpressRoute 回線をハブに自動的に関連付けます]** チェック ボックスを選択します。 

   1. **[追加]** を選択してリンクを確立します。 

1. 接続をテストするために、[NSX-T セグメントを作成](./tutorial-nsx-t-network-segment.md)してネットワーク上に VM をプロビジョニングします。 オンプレミスと Azure VMware Solution の両方のエンドポイントに ping を実行します。

   >[!NOTE]
   >前に作成した VNet の VM など、ExpressRoute 回路の後にあるクライアントからの接続は約 5 分待ってからテストしてください。
