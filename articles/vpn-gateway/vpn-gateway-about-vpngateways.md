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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# VPN Gateway について


VPN Gateway は、仮想ネットワークとオンプレミスの場所との間でネットワーク トラフィックの送信に使用されるリソースのコレクションです。ゲートウェイは、サイト対サイト、ポイント対サイト、ExpressRoute の各接続で使用されます。また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (VNet 間)。

接続を作成するには、VNet に仮想ネットワーク ゲートウェイを追加し、追加の VPN Gateway リソースとその設定を構成します。各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは、ゲートウェイの種類ごとに 1 つに限られています。たとえば、-GatewayType が Vpn の仮想ネットワーク ゲートウェイと -GatewayType が ExpressRoute の仮想ネットワーク ゲートウェイをそれぞれ 1 つ配置できます。

ゲートウェイの要件については、「[ゲートウェイの要件](vpn-gateway-about-vpn-gateway-settings.md#requirements)」を参照してください。合計スループットの見積もりについては、「[About VPN Gateway Settings (VPN Gateway の設定について)](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput)」を参照してください。価格については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway)」を参照してください。サブスクリプションとサービスの制限については、[ネットワークの制限](../articles/azure-subscription-service-limits.md#networking-limits)に関するページを参照してください。

VPN ゲートウェイを構成する手順は、仮想ネットワークの作成に使用したデプロイメント モデルによって異なります。たとえば、クラシック デプロイメント モデルを使用して VNet を作成した場合は、クラシック デプロイメント モデルに対応したガイドラインと手順を使用して VPN ゲートウェイ設定を作成し、構成します。詳細については、[Resource Manager およびクラシック デプロイメント モデルの概要](../resource-manager-deployment-model.md)に関するページを参照してください。

以下の各セクションには、構成に関する次の情報をまとめた表があります。

- 利用可能なデプロイメント モデル
- 利用可能な構成ツール
- 記事に直接移動するリンク (利用可能な場合)


図と説明を参考にして、要件を満たす構成トポロジを選択できます。図は主要なベースライン トポロジを示していますが、図をガイドラインとして使用して、より複雑な構成を構築することもできます。各構成は、選択した VPN ゲートウェイ設定に依存します。

### VPN Gateway の設定の構成

VPN Gateway はリソースのコレクションであるため、一部のリソースをあるツールで構成してから、別のツールに切り替えて他のリソースの設定を構成できます。現時点では、すべての VPN Gateway リソースの設定を Azure Portal で構成することはできません。各構成の記事の手順では、特定のツールが必要かどうかが指定されています。クラシック デプロイメント モデルで作業している場合、現時点では、クラシック ポータルで操作するか、PowerShell を使用することができます。使用可能な個々の設定については、「[About VPN Gateway settings (VPN ゲートウェイ設定について)](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。



## サイト間とマルチサイト

### サイト間

サイト間 (S2S) 接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。この種類の接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。S2S 接続は、クロスプレミスおよびハイブリッド構成に使用できます。

![S2S connection](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### マルチサイト

VNet と複数のオンプレミス ネットワークの間に、VPN 接続を作成して構成することができます。複数の接続を使用する場合は、ルート ベースの VPN の種類 (従来の VNet での動的ゲートウェイ) を使用する必要があります。VNet には 1 つの仮想ネットワーク ゲートウェイしか配置できないため、ゲートウェイを経由するすべての接続は、使用可能な帯域幅を共有します。この種類の構成は、一般に "マルチサイト" 接続と呼ばれます。
 

![Multi-Site connection](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### デプロイメント モデルおよび方法

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 間

仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。どちらの接続タイプでも、Azure VPN ゲートウェイを使用し、IPsec/IKE を使った安全なトンネルが確保されます。マルチサイト構成と VNet 間通信を組み合わせることもできます。そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

以下のような VNet を接続できます。

- 同じリージョンまたは異なるリージョンにある
- 同じサブスクリプションまたは異なるサブスクリプションにある
- 同じデプロイメント モデルまたは異なるデプロイメント モデルである



![VNet to VNet connection](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")



### デプロイメント モデル間の接続

Azure には現在、クラシックと Resource Manager という 2 つのデプロイメント モデルがあります。これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。VNet 間に接続を作成し、一方の VNet 内のリソースがもう一方の VNet 内のリソースと直接通信できるようにすることが可能です。


### デプロイメント モデルおよび方法

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

### VNET ピアリング

仮想ネットワークの構成が特定の要件を満たしていれば、接続の作成に VNET ピアリングを使用することができます。VNET ピアリングは、仮想ネットワーク ゲートウェイを使用しません。[VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)は、現在、プレビュー段階です。



## ポイント対サイト

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。P2S は、SSTP (Secure Socket トンネリング プロトコル) 経由の VPN 接続です。P2S 接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。VPN 接続を確立するには、クライアント コンピューターから接続を開始します。これは、自宅や会議室など、リモートの場所から VNet に接続する場合や、VNet に接続する必要があるクライアントの数が少ない場合などに便利です。


![Point-to-site connection](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### デプロイメント モデルおよび方法

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。


## サイト間と ExpressRoute の共存接続

ExpressRoute は、パブリックなインターネットを経由せずに WAN から Azure などの Microsoft サービスに直接アクセスする、専用の接続です。サイト間 VPN トラフィックは、暗号化が施されたうえでパブリック インターネット経由で送受信されます。同じ仮想ネットワークに対してサイト間 VPN と ExpressRoute 接続が構成可能な場合、いくつかの利点があります。

ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。この構成では、同一の仮想ネットワークに 2 つの仮想ネットワーク ゲートウェイが必要です (1 つは -GatewayType が Vpn のゲートウェイで、もう 1 つは -GatewayType が ExpressRoute のゲートウェイ)。


![Coexist connection](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### デプロイメント モデルおよび方法

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 次のステップ

VPN Gateway の詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。

オンプレミスの場所を VNet に接続します。[サイト間接続の作成](vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関するページを参照してください。





 

<!---HONumber=AcomDC_0831_2016-->