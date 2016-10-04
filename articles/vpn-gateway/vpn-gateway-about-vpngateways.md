<properties 
   pageTitle="VPN Gateway について| Microsoft Azure"
   description="Azure Virtual Networks の VPN Gateway 接続について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# VPN Gateway について


仮想ネットワーク ゲートウェイは、Azure 仮想ネットワークとオンプレミスの場所の間のほか、Azure 内の仮想ネットワーク間 (VNet 間) のネットワーク トラフィックの送信にも使用されます。VPN ゲートウェイを構成するときに、仮想ネットワーク ゲートウェイ、および仮想ネットワーク ゲートウェイの接続を作成して構成する必要があります。

Resource Manager デプロイメント モデルでは、仮想ネットワーク ゲートウェイ リソースを作成する際に、設定をいくつか指定します。必要な設定の 1 つは "-GatewayType" です。Vpn と ExpressRoute という 2 種類の仮想ネットワーク ゲートウェイがあります。

専用プライベート接続でネットワーク トラフィックを送信する場合、ゲートウェイの種類 "ExpressRoute" を使用します。これは、ExpressRoute ゲートウェイとも呼ばれます。パブリック接続でネットワーク トラフィックを暗号化して送信する場合、ゲートウェイの種類 "Vpn" を使用します。これは、VPN ゲートウェイと呼ばれます。サイト間接続、ポイント対サイト接続、VNet 間接続のすべてで、VPN ゲートウェイが使用されます。

各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは、ゲートウェイの種類ごとに 1 つに限られています。たとえば、-GatewayType が ExpressRoute の仮想ネットワーク ゲートウェイと -GatewayType が Vpn の仮想ネットワーク ゲートウェイをそれぞれ 1 つ配置できます。この記事では主に、VPN ゲートウェイについて説明します。ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

## 価格

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]


## ゲートウェイの SKU

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## VPN ゲートウェイの構成

VPN ゲートウェイを構成する手順は、仮想ネットワークの作成に使用したデプロイメント モデルによって異なります。たとえば、クラシック デプロイメント モデルを使用して VNet を作成した場合は、クラシック デプロイメント モデルに対応したガイドラインと手順を使用して VPN ゲートウェイ設定を作成し、構成します。デプロイメント モデルの詳細については、[Resource Manager デプロイメント モデルとクラシック デプロイメント モデルについて](../resource-manager-deployment-model.md)のページを参照してください。

VPN ゲートウェイ接続は、特定の設定で構成された複数のリソースに依存します。ほとんどのリソースは個別に構成できますが、場合によってはある一定の順序で構成を行う必要があります。Azure Portal などの構成ツールをどれか 1 つ使用して、リソースの作成と構成を開始できます。その後、追加のリソースを構成したり、適用できる場合に既存のリソースを変更したりするために、PowerShell などの別のツールに切り替えることができます。現時点では、すべてのリソースとリソースの設定を Azure Portal で構成することはできません。各接続トポロジの記事の手順では、特定の構成ツールが必要な場合が指定されています。VPN Gateway の個々のリソースと設定については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。

以下の各セクションには、次の情報をまとめた表があります。

- 利用可能なデプロイメント モデル
- 利用可能な構成ツール
- 記事に直接移動するリンク (利用可能な場合)

図と説明を参考にして、要件を満たす接続トポロジを選択できます。図は主要なベースライン トポロジを示していますが、図をガイドラインとして使用して、より複雑な構成を構築することもできます。

## サイト間とマルチサイト

### サイト間

サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。この種類の接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。S2S 接続は、クロスプレミスおよびハイブリッド構成に使用できます。

![S2S connection](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### マルチサイト

VNet と複数のオンプレミス ネットワークの間に、VPN ゲートウェイ接続を作成して構成することができます。複数の接続を使用する場合は、VPN の種類 RouteBased (従来の VNet での動的ゲートウェイ) を使用する必要があります。VNet に配置できる VPN ゲートウェイは 1 つのみであるため、ゲートウェイを経由するすべての接続は、使用可能な帯域幅を共有します。これは一般に "マルチサイト" 接続と呼ばれます。
 

![Multi-Site connection](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### サイト間接続とマルチサイト接続で使用できるデプロイメント モデルとデプロイ方法

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 間

仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。マルチサイト接続構成と VNet 間通信を組み合わせることもできます。そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

以下のような VNet を接続できます。

- 同じリージョンまたは異なるリージョンにある
- 同じサブスクリプションまたは異なるサブスクリプションにある
- 同じデプロイメント モデルまたは異なるデプロイメント モデルである


![VNet to VNet connection](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")

#### デプロイメント モデル間の接続

Azure には現在、クラシックと Resource Manager という 2 つのデプロイメント モデルがあります。これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。VNet 間に接続を作成し、一方の VNet 内のリソースがもう一方の VNet 内のリソースと直接通信できるようにすることが可能です。

#### VNET ピアリング

仮想ネットワークが特定の要件を満たしていれば、接続の作成に VNET ピアリングを使用することができます。VNET ピアリングは、仮想ネットワーク ゲートウェイを使用しません。詳細については、「[VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。


### VNet 間接続で使用できるデプロイメント モデルとデプロイ方法

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## ポイント対サイト

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。P2S は、SSTP (Secure Socket トンネリング プロトコル) 経由の VPN 接続です。P2S 接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。VPN 接続を確立するには、クライアント コンピューターから接続を開始します。これは、自宅や会議室など、リモートの場所から VNet に接続する場合や、VNet に接続する必要があるクライアントの数が少ない場合などに便利です。P2S 接続と S2S 接続は、両者の構成要件がすべて両立する場合に、同じ VPN ゲートウェイを使って組み合わせて使用できます。


![Point-to-site connection](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### ポイント対サイト接続で使用できるデプロイメント モデルとデプロイ方法

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

ExpressRoute 接続では、仮想ネットワーク ゲートウェイは "Vpn" ではなく "ExpressRoute" というゲートウェイの種類で構成されます。ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。


## サイト間と ExpressRoute の共存接続

ExpressRoute は、パブリックなインターネットを経由せずに WAN から Azure などの Microsoft サービスに直接アクセスする、専用の接続です。サイト間 VPN トラフィックは、暗号化が施されたうえでパブリック インターネット経由で送受信されます。同じ仮想ネットワークに対してサイト間 VPN と ExpressRoute 接続が構成可能な場合、いくつかの利点があります。

ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。ここでは同一の仮想ネットワークに 2 つの仮想ネットワーク ゲートウェイが必要であることに注意してください (1 つは -GatewayType が Vpn のゲートウェイで、もう 1 つは -GatewayType が ExpressRoute のゲートウェイ)。


![Coexist connection](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### S2S 接続と ExpressRoute 接続で使用できるデプロイメント モデルとデプロイ方法

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 次のステップ

VPN ゲートウェイの構成を計画します。[VPN ゲートウェイの計画と設計](vpn-gateway-plan-design.md)に関するページのほか、「[Connecting your on-premises network to Azure (Azure へのオンプレミス ネットワークの接続)](../guidance/guidance-connecting-your-on-premises-network-to-azure.md)」を参照してください。








 

<!---HONumber=AcomDC_0928_2016-->