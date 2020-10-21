---
title: Azure VMware Solution への IPSec トンネルを作成する
description: Virtual WAN ハブを作成して、Azure VMware Solution への IPSec トンネルを確立する方法について説明します。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 74cc31abf432954008cbb20bf64825d199732dab
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951130"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware Solution への IPSec トンネルを作成する

この記事では、Microsoft Azure Virtual WAN ハブで終端する VPN (IPsec IKE v1 および IKE v2) サイト間トンネルを確立する手順について説明します。 パブリック IP アドレスがアタッチされた Azure Virtual WAN ハブと VPN ゲートウェイを作成します。 次に、Azure ExpressRoute ゲートウェイを作成し、Azure VMware Solution エンドポイントを確立します。 また、ポリシーベースの VPN オンプレミス設定を有効にする方法についても説明します。 

## <a name="topology"></a>トポロジ

![VPN サイト間トンネル アーキテクチャ。](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure Virtual ハブには、Azure VMware Solution ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイが含まれています。 これにより、オンプレミスの VPN デバイスは Azure VMware Solution エンドポイントに接続されます。

## <a name="before-you-begin"></a>開始する前に

サイト間 VPN トンネルを作成するには、オンプレミスの VPN デバイスで終端する公開 IP アドレスを作成する必要があります。

## <a name="create-a-virtual-wan-hub"></a>Virtual WAN のハブを作成する

1. Azure portal で、**仮想 WAN** を探します。 **[+追加]** を選択します。 [WAN の作成] ページが開きます。  

2. **[WAN の作成]** ページで必要なフィールドを入力し、 **[確認と作成]** を選択します。
   
   | フィールド | 値 |
   | --- | --- |
   | **サブスクリプション** | 値には、リソース グループが属しているサブスクリプションが事前に設定されています。 |
   | **リソース グループ** | 仮想 WAN はグローバル リソースであり、特定のリージョンに限定されません。  |
   | **リソース グループの場所** | Virtual WAN ハブを作成するには、リソース グループの場所を設定する必要があります。  |
   | **名前** |   |
   | **Type** | **[Standard]** を選択します。これにより、VPN ゲートウェイのトラフィック以外も許可されます。  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="WAN の作成":::。

3. Azure portal で、前の手順で作成した仮想 WAN を選択し、 **[仮想ハブの作成]** を選択し、必要なフィールドを入力して、 **[次へ: サイト間]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **リージョン** | 管理の観点から、リージョンの選択は必須です。  |
   | **名前** |    |
   | **ハブのプライベート アドレス空間** | `/24` (最小) を使用してサブネットを入力します。  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="WAN の作成":::

4. **[サイト対サイト]** タブで、 **[ゲートウェイ スケール ユニット]** ドロップダウンから合計スループットを設定して、サイト間ゲートウェイを定義します。 

   >[!TIP]
   >1 つのスケール ユニット = 500 Mbps。 スケール ユニットは冗長性のためにペアになっており、それぞれが 500 Mbps をサポートしています。
  
5. **[ExpressRoute]** タブで ExpressRoute ゲートウェイを作成します。 

   >[!TIP]
   >スケール ユニットの値は 2 Gbps です。 

    各ハブの作成には約 30 分かかります。 

## <a name="create-a-vpn-site"></a>VPN サイトを作成する 

1. Azure portal の **[最近のリソース]** で、前のセクションで作成した仮想 WAN を選択します。

2. 仮想ハブの **[概要]** で、 **[接続]**  >  **[VPN (Site-to-site)]\(VPN (サイト間)\)** を選択し、 **[VPN サイトの新規作成]** を選択します。


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="WAN の作成":::  
 
3. **[基本]** タブで、必要なフィールドを入力し、 **[次へ: リンク]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **リージョン** | 前のセクションで指定したものと同じリージョン。  |
   | **名前** |  |
   | **デバイス ベンダー** |  |
   | **Border Gateway Protocol** | **[有効]** に設定して、Azure VMware Solution とオンプレミス サーバーの両方がトンネルを介してルートのアドバタイズを実行するようにします。 無効にした場合、アドバタイズする必要のあるサブネットは手動で保守する必要があります。 サブネットが欠落している場合、HCX はサービス メッシュの形成に失敗します。 詳細については、「[BGP と Azure VPN Gateway について](../vpn-gateway/vpn-gateway-bgp-overview.md)」を参照してください。 |
   | **プライベート アドレス空間**  | オンプレミスの CIDR ブロックを入力します。  これは、トンネルを介してオンプレミスにバインドされているすべてのトラフィックをルーティングするために使用されます。  CIDR ブロックは、BGP を有効にしない場合にのみ必要です。 |
   | **接続先** |   |

4. **[リンク]** タブで、必須フィールドに入力し、 **[確認と作成]** を選択します。 リンクとプロバイダーの名前を指定すると、ハブの一部として最終的に作成される可能性のあるゲートウェイの数がいくつになっても区別できます。 BGP と自律システム番号 (ASN) は、組織内で一意である必要があります。
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>(省略可能) ポリシーベースの VPN サイト間トンネル用の VPN サイトの定義

このセクションは、ポリシーベースの VPN にのみ適用されます。 ポリシーベース (または静的なルートベース) の VPN 設定は、ほとんどの場合、オンプレミスの VPN デバイスの機能によって決まります。 オンプレミスと Azure VMware Solution のネットワークを指定する必要があります。 Azure Virtual WAN ハブを使用する Azure VMware Solution の場合、"*任意*" のネットワークを選択することはできません。 そうではなく、関連するすべてのオンプレミスおよび Azure VMware Solution Virtual WAN ハブの範囲を指定する必要があります。 このようなハブの範囲は、ポリシーベースの VPN トンネル オンプレミス エンドポイントの暗号化ドメインを指定するために使用されます。 Azure VMware Solution 側では、ポリシーベースのトラフィック セレクター インジケーターを有効にすることのみが必要です。 

1. Azure portal で、Virtual WAN ハブ サイトに移動します。 **[接続]** で、 **[VPN (Site to site)]\(VPN (サイト間)\)** を選択します。

2. VPN サイト名を選択して、右端の省略記号 (...) を選択します。次に **[edit VPN section to this hub]\(このハブへの VPN セクションの編集\)** を選択します。
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="WAN の作成" lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN サイトとハブ間の接続を編集し、 **[保存]** を選択します。
   - インターネット プロトコル セキュリティ (IPSec) で **[カスタム]** を選択します。
   - ポリシーベースのトラフィック セレクターを使用して **[有効にする]** を選択します
   - **[IKE フェーズ 1]** と **[IKE フェーズ 2 (ipsec)]** の詳細を指定します。 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="WAN の作成"::: 
 
    ポリシーベースの暗号化ドメインの一部であるトラフィック セレクターまたはサブネットは、次のようになります。
    
    - 仮想 WAN ハブ /24
    - Azure VMware Solution のプライベート クラウド /22
    - 接続されている Azure 仮想ネットワーク (存在する場合)

## <a name="connect-your-vpn-site-to-the-hub"></a>VPN サイトをハブに接続する

1. VPN サイト名の横にあるチェックボックスをオンにして (前述の **VPN (サイト間)** のスクリーンショットを参照してください)、 **[VPN サイトの接続]** を選択します。 **[事前共有キー]** フィールドに、オンプレミス エンドポイントに対して以前に定義したキーを入力します。 以前に定義したキーがない場合は、このフィールドを空白のままにすると、キーが自動的に生成されます。 
 
    ハブにファイアウォールを展開していて、それがそのトンネルを介した接続の次ホップである場合にのみ、 **[既定のルートを伝達する]** を有効にします。

    **[接続]** を選択します。 接続状態画面には、トンネル作成の状態が表示されます。

2. 仮想 WAN の概要に移動します。 VPN サイト ページを開き、VPN 構成ファイルをダウンロードして、オンプレミスのエンドポイントに適用します。  

3. 次に、Azure VMware Solution ExpressRoute に修正プログラムを適用して Virtual WAN ハブに展開します (この手順を実行するには、最初にプライベート クラウドを作成する必要があります)。

    Azure VMware Solution プライベート クラウドの **[接続]** セクションに移動します。 **[ExpressRoute]** タブで、 **[+ Request an authorization key]\(認可キーを要求する\)** を選択します。 名前を指定し、 **[作成]** を選択します (キーの作成には約 30 秒かかることがあります)。ExpressRoute ID と承認キーをコピーします。 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="WAN の作成":::

    > [!NOTE]
    > 承認キーはしばらくすると消えるため、表示されたらすぐにコピーしてください。

4. 次に、Virtual WAN ハブで Azure VMware Solution と VPN ゲートウェイをリンクします。 Azure portal で、以前に作成した仮想 WAN を開きます。 作成した Virtual WAN ハブを選択し、左側のペインで **[ExpressRoute]** を選択します。 **[+ 認可キーを利用する]** を選択します。

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="WAN の作成":::

    [承認キー] フィールドに承認キーを、 **[ピア回線の URI]** フィールドに ExpressRoute ID を貼り付けます。 必ず **[この ExpressRoute 回線をハブに自動的に関連付けます]** をオンにしてください。 **[追加]** を選択してリンクを確立します。 

5. 接続をテストするには、[NSX-T セグメントを作成](./tutorial-nsx-t-network-segment.md)し、ネットワーク上に VM をプロビジョニングします。 オンプレミスと Azure VMware Solution の両方のエンドポイントに ping を実行してテストします。