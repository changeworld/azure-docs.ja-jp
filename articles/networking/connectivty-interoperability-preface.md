---
title: 'ExpressRoute、サイト間 VPN、VNet ピアリングの相互運用性 - テスト セットアップ: Azure バックエンド接続機能の相互運用性 | Microsoft Docs'
description: このページでは、ExpressRoute、サイト間 VPN、VNet ピアリングの機能の相互運用性を分析するためのテスト セットアップについて取り上げます。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947112"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>ExpressRoute、サイト間 VPN、VNet ピアリングの相互運用性 - テスト セットアップ
この記事では、コントロールプレーンとデータプレーンの両方のレベルで各種機能の相互運用性を分析する際に使用できるテスト セットアップを考えてみましょう。 テスト セットアップについて検討する前に、Azure の各種ネットワーク機能の意味を簡単に見てみましょう。

ExpressRoute: ExpressRoute プライベート ピアリングを使用すると、ご利用の Azure VNet デプロイにオンプレミス ネットワークのプライベート IP 空間を直接接続することができます。  ExpressRoute を使用することで、より帯域幅の広いプライベート接続を実現できます。 ExpressRoute のエコシステムには、ExpressRoute 接続と SLA を提供する多くのパートナーが存在します。 ExpressRoute とその構成方法の詳細については、[ExpressRoute の概要][ExpressRoute]に関するページを参照してください。

サイト間 VPN: オンプレミス ネットワークをインターネットまたは ExpressRoute 経由で Azure に安全に接続するために、サイト間 (S2S) VPN オプションが利用可能です。 Azure に接続するために S2S VPN を構成する方法については、[VPN Gateway の構成][VPN]に関するページを参照してください。

VNET ピアリング: VNET ピアリングは、仮想ネットワーク (VNET) 間の接続を確立する際に利用できます。 VNET ピアリングの詳細については、[VNET ピアリングに関するチュートリアル][VNet]を参照してください。

##<a name="test-setup"></a>テストの設定

以下の図は、テスト セットアップを示しています。

[![1]][1]

テスト セットアップの中心となるのは、Azure Region 1 の Hub Vnet です。 Hub Vnet は、次の各種ネットワークに接続されています。

1.  VNET ピアリングで Spoke Vnet に。 Spoke Vnet は、Hub Vnet の両方のゲートウェイにリモート アクセスできます。
2.  サイト間 VPN 経由で Branch Vnet に。 この接続では eBGP を使用してルートを交換します。
3.  プライマリ パスとして ExpressRoute プライベート ピアリングを使用し、バックアップ パスとしてサイト間 VPN 接続を経由してオンプレミス ネットワークの Location 1 に。 以降、このドキュメントでは、この ExpressRoute 回線を ExpressRoute1 と呼びます。 ExpressRoute 回線には、冗長接続による高可用性が既定で確保されています。 ExpressRoute1 では、セカンダリ MSEE と面しているセカンダリ CE ルーターのサブインターフェイスが無効になっています。 上の図では、これが二重線の矢印上に赤線で示されています。
4.  別の ExpressRoute プライベート ピアリングでオンプレミス ネットワークの Location 2 に。 以降、このドキュメントでは、この 2 つ目の ExpressRoute 回線を ExpressRoute2 と呼びます。
5.  また、Hub Vnet とオンプレミスの Location 1 は、どちらも ExpressRoute1 によって Azure Region 2 のリモート Vnet に接続されています。

## <a name="further-reading"></a>参考資料

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続を組み合わせて使用する

#### <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN 

ExpressRoute Microsoft ピアリング上にサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet との間で、機密性、アンチリプレイ、信頼性、整合性を確保しつつ、プライベートにデータを交換することができます。 ExpressRoute Microsoft ピアリング上にトンネル モードでサイト間 IPsec VPN を構成する方法の詳細については、[ExpressRoute Microsoft ピアリング上のサイト間 VPN][S2S-Over-ExR] に関するページを参照してください。 

Microsoft ピアリング上に S2S VPN を構成することに伴う重要な制限はスループットです。 IPsec トンネル上では、VPN GW の容量により、スループットが制限されます。 VPN GW のスループットは、ExpressRoute のスループットよりも低くなります。 そのようなシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用すると、ExpressRoute の帯域幅使用率を最適化するのに役立ちます。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute のセキュリティで保護されたフェールオーバー パスとしてのサイト間 VPN
ExpressRoute は、高可用性を確保するための冗長回線ペアとして提供されます。 異なる Azure リージョンで geo 冗長 ExpressRoute 接続を構成することができます。 また、ここで紹介したテスト セットアップのように、特定の Azure リージョン内で ExpressRoute 接続のフェールオーバー パスが必要な場合は、サイト間 VPN を使用することができます。 ExpressRoute と S2S VPN の両方に同じプレフィックスがアドバタイズされた場合、Azure では ExpressRoute が S2S VPN よりも優先されます。 ExpressRoute と S2S VPN との間で非対称なルーティングを回避するために、オンプレミス ネットワーク構成でも、ExpressRoute を S2S VPN 接続よりも優先するように設定する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法の詳細については、[ExpressRoute とサイト間の共存][ExR-S2S-CoEx]に関するページを参照してください。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>バックエンド接続をスポーク VNet とブランチ ロケーションに拡張する

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNet ピアリングを使用したスポーク VNet 接続

ハブアンドスポークの VNet アーキテクチャは、広く利用されています。 ハブは、Azure 内の仮想ネットワーク (VNet) であり、スポーク VNet とオンプレミス ネットワークをつなぐ接続の中央拠点として機能します。 スポークは、ハブとピアリングする VNet であり、ワークロードを分離する目的で使用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャの詳細については、[ハブアンドスポーク アーキテクチャ][Hub-n-Spoke]に関するページを参照してください。

スポーク VNet は、同じリージョン内の VNet ピアリングを通じてハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用し、リモート ネットワークと通信を行うことができます。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>サイト間 VPN を使用したブランチ VNet 接続

(異なるリージョンに存在する) ブランチ VNet とオンプレミス ネットワークをハブ VNet 経由で相互通信させたい場合、VPN を使用したサイト間 VPN 接続が Azure のネイティブ ソリューションとなります。 ハブにおけるルーティングには NVA を使用する選択肢もあります。

VPN ゲートウェイの構成については、[VPN ゲートウェイの構成][VPN]に関するページを参照してください。 高可用性の NVA のデプロイについては、[高可用性 NVA のデプロイ][Deploy-NVA]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

テスト トポロジの構成の詳細については、[構成の詳細][Configuration]に関するページを参照してください。

テスト セットアップのコントロール プレーン分析と、このトポロジの各種 VNet/VLAN の全体像については、[コントロール プレーン分析][Control-Analysis]に関するページを参照してください。

テスト セットアップのデータ プレーン分析および Azure のネットワーク監視機能の概観については、[データ プレーン分析][Data-Analysis]に関するページを参照してください。

ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数や、ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数など、ExpressRoute のスケーリングに関する各種制限については、「[ExpressRoute の FAQ][ExR-FAQ]」を参照してください。



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "テスト トポロジ"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




