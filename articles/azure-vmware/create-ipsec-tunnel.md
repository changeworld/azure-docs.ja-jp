---
title: Azure VMware Solution への IPSec トンネルを作成する
description: Azure VMware Solution への VPN (IPsec IKEv1 および IKEv2) サイト間トンネルを確立する方法について説明します。
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027043"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware Solution への IPSec トンネルを作成する

この記事では、Microsoft Azure Virtual WAN ハブで終端する VPN (IPsec IKE v1 および IKE v2) サイト間トンネルを確立する手順について説明します。 ハブには、Azure VMware Solution ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイが含まれています。 これにより、オンプレミスの VPN デバイスは Azure VMware Solution エンドポイントに接続されます。

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN サイト間トンネル アーキテクチャを示す図。" border="false":::

この記事では、次のことについて説明します。
- Azure Virtual WAN ハブとパブリック IP アドレスがアタッチされた VPN ゲートウェイを作成する。 
- Azure ExpressRoute ゲートウェイを作成し、Azure VMware Solution エンドポイントを確立する。 
- ポリシーベースの VPN のオンプレミス設定を有効にする。 

## <a name="prerequisites"></a>前提条件
オンプレミスの VPN デバイスで終端する公開 IP アドレスが必要です。

## <a name="step-1-create-an-azure-virtual-wan"></a>手順 1. Azure Virtual WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>手順 2. Virtual WAN のハブとゲートウェイを作成する

>[!TIP]
>[既存のハブにゲートウェイを作成する](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)こともできます。

1. 前の手順で作成した Virtual WAN を選択します。

1. **[仮想ハブを作成する]** を選択し、必須フィールドに入力し、 **[次へ: サイト対サイト]** を選択します。 

   `/24` (最小) を使用してサブネットを入力します。

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="[仮想ハブを作成する] ページを示すスクリーンショット。":::

4. **[サイト対サイト]** タブを選択し、 **[ゲートウェイ スケール ユニット]** ドロップダウンから合計スループットを設定して、サイト間ゲートウェイを定義します。 

   >[!TIP]
   >スケール ユニットは冗長性のためにペアになっており、それぞれが 500 Mbps をサポートしています (1 スケール ユニット = 500 Mbps)。 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="サイト間の詳細を示すスクリーンショット。":::

5. **[ExpressRoute]** タブを選択し ExpressRoute ゲートウェイを作成します。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="ExpressRoute 設定のスクリーンショット。":::

   >[!TIP]
   >スケール ユニットの値は 2 Gbps です。 

    各ハブの作成には約 30 分かかります。 

## <a name="step-3-create-a-site-to-site-vpn"></a>手順 3. サイト間 VPN を作成する

1. Azure portal で、先ほど作成した仮想 WAN を選択します。

2. 仮想ハブの **[概要]** で、 **[接続]**  >  **[VPN (サイト間)]**  >  **[VPN サイトの新規作成]** を選択します。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="[VPN (Site-to-site)]\(VPN (サイト間)\) と [VPN サイトの新規作成] が選択されている仮想ハブの [概要] ページのスクリーンショット。":::  
 
3. **[基本]** タブで、必須フィールドに入力します。 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="新しい VPN サイトの [基本] タブのスクリーンショット。":::  

   1. **[Border Gateway Protocol]** を **[有効]** に設定します。  有効にすると、Azure VMware Solution とオンプレミス サーバーの両方がトンネルを介してルートをアドバタイズするようになります。 無効にした場合、アドバタイズする必要のあるサブネットは手動で保守する必要があります。 サブネットが欠落している場合、HCX はサービス メッシュの形成に失敗します。 詳細については、「[BGP と Azure VPN Gateway について](../vpn-gateway/vpn-gateway-bgp-overview.md)」を参照してください。
   
   1. **[プライベート アドレス空間]** には、オンプレミスの CIDR ブロックを入力します。 これは、トンネルを介してオンプレミスにバインドされているすべてのトラフィックをルーティングするために使用されます。 CIDR ブロックは、BGP を有効にしない場合にのみ必要です。

1. **[次へ: リンク]** を選択し、必須フィールドに入力します。 リンクとプロバイダーの名前を指定すると、ハブの一部として最終的に作成される可能性のあるゲートウェイの数がいくつになっても区別できます。 BGP と自律システム番号 (ASN) は、組織内で一意である必要があります。

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="リンクの詳細を示すスクリーンショット。":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。 

1. 目的の仮想ハブに移動し、 **[ハブの関連付け]** の選択を解除して VPN サイトをハブに接続します。
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="[サイトの接続] ペインを示すスクリーンショット。仮想ハブの事前共有キーと関連設定を指定できます。":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>手順 4. (任意) ポリシーベースの VPN サイト間トンネルを作成する

>[!IMPORTANT]
>これは省略可能な手順であり、ポリシーベースの VＰＮ にのみ適用されます。 

ポリシーベースの VPN セットアップでは、ハブの範囲を含め、オンプレミスおよび Azure VMware Solution のネットワークを指定する必要があります。  これらのハブの範囲により、ポリシーベースの VPN トンネルのオンプレミス エンドポイントの暗号化ドメインが指定されます。  Azure VMware Solution 側では、ポリシーベースのトラフィック セレクター インジケーターを有効にすることのみが必要です。 

1. Azure portal で、お使いの仮想 WAN ハブ サイトに移動します。 **[接続]** の **[VPN (Site to site)]\(VPN (サイト間)\)** を選択します。

2. VPN サイト名、右端の省略記号 (...)、 **[このハブへの VPN 接続の編集]** の順に選択します。
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="[このハブへの VPN 接続の編集] にアクセスするために選択された省略記号を示している Azure の仮想 WAN ハブ サイト用のページのスクリーンショット。" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN サイトとハブ間の接続を編集し、 **[保存]** を選択します。
   - インターネット プロトコル セキュリティ (IPSec) で **[カスタム]** を選択します。
   - ポリシーベースのトラフィック セレクターを使用して **[有効にする]** を選択します
   - **[IKE フェーズ 1]** と **[IKE フェーズ 2 (ipsec)]** の詳細を指定します。 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="[VPN 接続を編集する] ページのスクリーンショット。"::: 
 
   ポリシーベースの暗号化ドメインの一部であるトラフィック セレクターまたはサブネットは、次のようになります。
    
   - Virtual WAN ハブ `/24`
   - Azure VMware Solution のプライベート クラウド `/22`
   - 接続されている Azure 仮想ネットワーク (存在する場合)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>手順 5. VPN サイトをハブに接続する

1. VPN サイト名を選択して、 **[VPN サイトの接続]** を選択します。 

1. **[事前共有キー]** フィールドに、オンプレミス エンドポイントに対して以前に定義したキーを入力します。 

   >[!TIP]
   >以前に定義したキーがない場合は、このフィールドは空白のままでかまいません。 キーが自動的に生成されます。 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="[サイトの接続] ペインを示すスクリーンショット。仮想ハブの事前共有キーと関連設定を指定できます。"::: 

1. ハブにファイアウォールをデプロイしていて、それが次のホップである場合は、 **[既定のルートを伝達]** オプションを **[有効]** に設定します。 

   有効にすると、Virtual WAN ハブが接続に対して伝達を行うのは、ハブにファイアウォールをデプロイしたときにハブにより既定のルートが既に学習されている場合、または接続されている別のサイトでトンネリングが強制的に有効にされている場合のみです。 既定のルートの起点は Virtual WAN ハブ内にありません。  

1. **[接続]** を選択します。 数分後、サイトに接続と接続性の状態が表示されます。

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="サイト間接続と接続性の状態を示すスクリーンショット。" lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. オンプレミスのエンドポイントの [VPN 構成ファイルをダウンロード](../virtual-wan/virtual-wan-site-to-site-portal.md#device)します。  

3. Virtual WAN ハブで Azure VMware Solution ExpressRoute に修正プログラムを適用します。 

   >[!IMPORTANT]
   >まず、プライベート クラウドを作成してから、プラットフォームにパッチを適用する必要があります。 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Virtual WAN ハブで Azure VMware Solution と VPN ゲートウェイをリンクします。 前の手順の認可キーと ExpressRoute ID (ピア回線の URI) を使用します。

   1. ExpressRoute ゲートウェイを選択し、 **[認可キーを利用する]** を選択します。

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="プライベート クラウドの [ExpressRoute] ページで [認可キーを利用する] が選択されているスクリーンショット。":::

   1. 認可キーを **[認可キー]** フィールドに貼り付けます。
   1. ExpressRoute ID を **[ピア回線の URI]** フィールドに貼り付けます。 
   1. **[この ExpressRoute 回線をハブに自動的に関連付けます]** チェック ボックスを選択します。 
   1. **[追加]** を選択してリンクを確立します。 

5. 接続をテストするために、[NSX-T セグメントを作成](./tutorial-nsx-t-network-segment.md)してネットワーク上に VM をプロビジョニングします。 オンプレミスと Azure VMware Solution の両方のエンドポイントに ping を実行します。
