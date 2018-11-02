---
title: 'ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - コントロール プレーン分析: Azure バックエンド接続機能の相互運用性 | Microsoft Docs'
description: このページでは、ExpressRoute、サイト間 VPN、VNET ピアリングの機能の相互運用性を分析するために構築されたテスト構成のコントロール プレーン分析について説明します｡
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947122"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - コントロール プレーン分析

この記事では､テスト構成のコントロール プレーン分析を一通り見てみます｡ テスト構成については､[テスト構成][Setup]を参照してください｡ テスト構成の構成詳細については､｢[テスト構成の構成][Configuration]｣を参照してください｡

基本的に､コントロール プレーン分析では､1 つのトポロジのネットワーク間で交換されるルートを調べます｡ コントロール プレーン分析は､ネットワークによるトポロジの見え方の違いを理解するのに役立ちます｡

##<a name="hub-and-spoke-vnet-perspective"></a>ハブ VNET とスポーク VNET の見え方

次の図はハブ VNet とスポーク VNet (青色で強調表示) から見たネットワークを表しています｡ この図はまた､ネットワークによって異なる ASN (Autonomous System Number) と異なるネットワーク間で交換されるルートも示しています｡ 

[![1]][1]

VNet の ExpressRoute ゲートウェイの ASN が Microsoft エンタープライズ エッジ ルーター (MSEE) と異なることに注意してください。 ExpressRoute ゲートウェイではプライベート ASN (65515) が使用されているのに対し､MSEE ではパブリック ASN (12076) が使用されています｡ ExpressRoute ピアリングを構成すると､MSEE がピアであるためにピアのASN として 12076 を使用します｡ Azure 側では､MSEE は ExpressRoute GW と eBGP ピアリングを確立します｡ 各 ExpressRoute ピアリング用に MSEE が確立するデュアル eBGP ピアリングは､コントロール プレーン レベルではトランスペアレントです｡ このため､ExpressRoute ルート テーブルを表示すると､VNET の プレフィックスにその VNet の ExpressRoute GW の ASN が表示されます｡ 次は ExpressRoute ルート テーブル例のスクリーンショットです｡ 

[![5]][5]

Azure 内では ASN はピアリングの視点からのみ意味があります｡ 既定では､ExpressRoute GW と VPN GW のどちらの ASN も65515 です｡

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>ExpressRoute 1 を経由するオンプレミスの場所 1 と リモート VNet の見え方

次図に示すように､オンプレミスの場所 1 とリモート VNET は共に ExpressRoute 1 経由でハブ VNET に接続されており､このため､トポロジの見え方は同じです｡

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>サイト間 VPN を経由するオンプレミスの場所 1 と分岐 VNET の見え方

次図に示すように､オンプレミスの場所 1 と分岐 VNET は共に サイト間 VPN 接続経由でハブ VNET の VPN GW に接続されており､このため､トポロジの見え方は同じです｡

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>オンプレミスの場所 2 の見え方

オンプレミスの場所 2 は、ExpressRoute 2 のプライベート ピアリング経由でハブ VNET に接続されています。 

[![4]][4]

## <a name="further-reading"></a>参考資料

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続を組み合わせて使用する

####  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリング上にサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet との間で、機密性とアンチリプレイ、信頼性、整合性を確保しながらプライベートにデータを交換することができます。 ExpressRoute Microsoft ピアリング上にトンネル モードでサイト間 IPsec VPN を構成する方法についての詳細は、[ExpressRoute Microsoft ピアリング上にサイト間 VPNを構成する方法][S2S-Over-ExR]に関するページを参照してください。 

Microsoft ピアリング上に S2S VPN を構成することに伴う重要な制限はスループットです。 IPsec トンネル上では、VPN GW の容量によりスループットが制限されます。 VPN GW のスループットは、ExpressRoute のスループットよりも低下します｡ このようなシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用することで、ExpressRoute の帯域幅使用率を最適化できます｡

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute のセキュアなフェールオーバー パスとしてのサイト間 VPN
ExpressRoute は、高可用性を確保するための冗長回線ペアとして提供されます。 複数の Azure リージョンに geo 冗長の ExpressRoute 接続を構成することができます。 また、ここで紹介したテスト構成のように、特定の Azure リージョン内で ExpressRoute 接続のフェールオーバー パスを設けたければ、サイト間 VPN を使用することができます。 ExpressRoute と S2S VPN の両方に同じプレフィックスがアドバタイズされた場合、ExpressRoute は S2S VPN よりも優先されます。 ExpressRoute と S2S VPN との間で非対称なルーティングを回避するために、オンプレミス ネットワーク構成でも、ExpressRoute を S2S VPN 接続よりも優先するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法についての詳細は、｢[ExpressRoute とサイト間の共存接続][ExR-S2S-CoEx]｣を参照してください。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>スポーク VNET と分岐先へのバックエンド接続の延長

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNET ピアリングを使用したスポーク VNET 接続

Hub-and-spoke VNET アーキテクチャは広く利用されています｡ Azure ではこのハブは仮想ネットワーク (VNet) であり､オンプレミスネットワークに接続しているスポーク VNET 間の接続の中心点の働きをします｡ スポークはハブとピアリングする VNet であり、ワークロードの分離に利用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャについての詳細は、[ハブ & スポーク アーキテクチャ][Hub-n-Spoke]を参照してください。

スポーク VNet は、同じリージョン内の VNet ピアリングを通じてハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用し、リモート ネットワークと通信を行うことができます。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>サイト間 VPN を使用した分岐 VNet 接続

分岐 VNet (異なるリージョンに存在) とオンプレミス ネットワークとをハブ VNet 経由で互いに通信させたい場合、Azure のネイティブ ソリューションは VPN を使用したサイト間 VPN 接続になります。 ハブにおけるルーティングには NVA を使用する選択肢もあります。

VPN ゲートウェイの構成については､[VPN ゲートウェイの構成][VPN]を参照してください｡ 高可用性の NVA のデプロイについては､[高可用性 NVA のデプロイ][Deploy-NVA]を参照してください。

## <a name="next-steps"></a>次の手順

テスト構成のデータ プレーン分析および Azure ネットワーク監視機能のビューについては、[データ プレーン分析][Data-Analysis]を参照してください。

ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数や、ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数など、ExpressRoute のスケーリングに関するその他制限については、[ExpressRoute FAQ][ExR-FAQ] を参照してください。


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "ハブ & スポーク VNET からのトポロジの見え方"
[2]: ./media/backend-interoperability/Loc1ExRView.png "ExpressRoute 1 を経由する場所 1 とリモート VNET からのトポロジの見え方"
[3]: ./media/backend-interoperability/Loc1VPNView.png "S2S VPN を経由する場所 1 と分岐 VNet からのトポロジの見え方"
[4]: ./media/backend-interoperability/Loc2View.png "場所 2 からのトポロジの見え方"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 の RouteTable"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




