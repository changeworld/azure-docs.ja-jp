<properties 
   pageTitle="VPN Gateway 接続トポロジ | Microsoft Azure"
   description="VPN Gateway 接続トポロジのほか、使用可能な構成ツールとデプロイ モデルを示します。"
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
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Azure VPN Gateway 接続

この記事は、VPN ゲートウェイ接続のベースライン トポロジを示します。グラフィックスと説明を参考に、要件に一致する構成トポロジを選択できます。この記事では主要なベースライン トポロジについて説明していますが、図をガイドラインとして使用することで、より複雑なトポロジを構築することもできます。

各トポロジには、そのトポロジを使用できるデプロイ モデルと、各トポロジの構成に使用できるデプロイ ツールが列挙された表が含まれています。また、利用できる記事がある場合は、リンクを設定してあります。新しい記事とデプロイ ツールが利用可能になったら、表をこまめに更新します。

VPN ゲートウェイの作成に使用する手順は、仮想ネットワークの作成に使用したデプロイ モデルによって異なります。たとえば、クラシック デプロイ モデルを使用して VNet を作成した場合は、クラシック デプロイ モデルに対応したガイドラインと手順を使用して VPN ゲートウェイを作成し、構成します。クラシック デプロイ モデルの仮想ネットワークに Resource Manager の VPN ゲートウェイを作成することはできません。デプロイ モデルの詳細については、「[Resource Manager とクラシック デプロイを理解する](../resource-manager-deployment-model.md)」を参照してください。

## サイト間とマルチサイト

サイト間接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。この種類の接続には、VPN デバイスまたはオンプレミスの Windows Server RRAS が必要です。サイト間接続は、クロスプレミスおよびハイブリッド構成に使用できます。


**S2S の図**

![S2S connection](./media/vpn-gateway-topology/site2site.png "site-to-site")

ルート ベースの Azure VPN を使用している場合は、オンプレミスのネットワークへの S2S VPN 接続を複数作成して構成できます。この種類の構成は、一般に "マルチサイト" 接続と呼ばれます。
 

**マルチサイトの図**

![Multi-Site connection](./media/vpn-gateway-topology/multisite.png "multi-site")


**使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 間

仮想ネットワークどうし (VNet 間) の接続は、仮想ネットワークをオンプレミス サイトの場所に接続することとよく似ています。どちらの接続タイプでも、Azure VPN ゲートウェイを使用し、IPsec/IKE を使った安全なトンネルが確保されます。接続する VNet は、リージョンやサブスクリプションが異なっていてもかまいません。マルチサイト構成と VNet 間通信を組み合わせることもできます。そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

Azure には現在、Azure サービス管理 (クラシックと呼ばれます) と Azure Resource Manager という 2 つのデプロイ モデルがあります。これまで Azure を使用してきた方であれば、クラシック VNet 上で実行される Azure VM やインスタンス ロールをご利用だと思われますが、新しい VM やロール インスタンスは、ARM で作成された VNet 上で実行される可能性があります。デプロイ モデルが異なる場合でも、仮想ネットワーク間の接続を作成できます。


**VNet2VNet の図**

![VNet to VNet connection](./media/vpn-gateway-topology/vnet2vnet.png "vnet-to-vnet")


**使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## サイト間と ExpressRoute の共存接続

ExpressRoute は、パブリックなインターネットを経由せず、WAN から直接、Azure などの Microsoft サービスに接続する専用の接続です。サイト間 VPN トラフィックは、暗号化が施されたうえでパブリック インターネット経由で送受信されます。同じ仮想ネットワークへのサイト間 VPN と ExpressRoute 接続が構成可能な場合、いくつかの利点があります。ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。


**共存接続の図**

![Coexist connection](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## ポイント対サイト

ポイント対サイト構成では、クライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を個別に作成することができます。VPN 接続を確立するには、クライアント コンピューターからの接続を開始します。これは、自宅や会議室など、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合などに便利です。

ポイント対サイト接続は、SSTP (Secure Socket トンネリング プロトコル) を介した VPN 接続です。ポイント対サイト接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。

**P2S の図**

![Point-to-site connection](./media/vpn-gateway-topology/point2site.png "point-to-site")

**使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## 次のステップ

「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md)」と「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」の項目を参照し、VPN ゲートウェイについての理解を深めてから、接続の計画と設計に進むことをお勧めします。





 

<!---HONumber=AcomDC_0720_2016-->