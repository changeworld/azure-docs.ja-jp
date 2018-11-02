---
title: 'ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - 構成の詳細: Azure バックエンド接続機能の相互運用性 | Microsoft Docs'
description: このページでは、ExpressRoute、サイト間 VPN、VNET ピアリングの機能の相互運用性の分析に使用するテスト構成の構成を詳しく説明します｡
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947097"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>ExpressRoute、サイト間 VPN、VNET ピアリングの相互運用性 - テスト構成の詳細

この記事では､テスト構成の構成を一通り見てみます｡ テスト構成については､[テスト構成][Setup]を参照してください｡ 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNET ピアリングを使用したスポーク VNET 接続

次の Azure portal のスクリーンショットは､スポーク VNET の VNET ピアリングの詳細を示しています｡ 2 つの仮想ネットワークの間に VNET ピアリングを構成する手順については､[VNET ピアリング][VNet-Config]を参照してください｡ Spoke VNET が Hub VNET を使用するようにしたい場合は､*リモート ゲートウェイを使用 (Use remote gateways)* を選択する必要があります｡

[![1]][1]

次の Azure portal のスクリーンショットは､ハブ VNET の VNET ピアリングの詳細を示しています｡ Hub VNET で Spoke VNET がゲートウェイを使用するようにしたい場合は､*リモート ゲートウェイを使用 (Use remote gateways)* を選択する必要があります｡

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>サイト間 VPN を使用した分岐 VNET 接続

ハブと Branch VNET との間のサイト間 VPN 接続は､VPN ゲートウェイを利用して構成されます｡ 既定では､VPN および ExpressRoute ゲートウェイは､プライベート ASN 値の 65515 を使って構成します｡ VPN ゲートウェイにより､ユーザーは ASN 値を構成できます｡ 次の Azure portal のスクリーンショットに示すようなテスト構成では､分岐 VNet の VPN ゲートウェイの ASN 値を65516 に変更することでハブと Branch VNET 間で eBGP ルーティングを行えるようにしています｡


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>ExpressRoute とサイト間 VPN を使用した場所 1 のオンプレミス接続

###<a name="expressroute1-configuration-details"></a>ExpressRoute1 構成の詳細

次のポータル スクリーンショットは､場所 1 の オンプレミス CE ルーター方向の Azure Region 1 ExpressRoute 回線の構成を示しています｡

[![4]][4]

次のポータル スクリーンショットは､ExpressRoute1 回線と Hub VNET 間の接続の構成を示しています｡

[![5]][5]

次は､ExpressRoute のプライベート ピアリング接続に関係するプライマリー CE ルーター (このテスト構成では CE ルーターとして Cisco ASR1000 ルーターを使用) 構成の一覧です｡ サイト間 VPN と ExpressRoute 回線が並列に構成されて､オンプレミスのネットワークを Azure に接続するようになっている場合､既定では Azure は ExpressRoute 回線を優先します｡ ExpressRoute とサイト間 VPN の両方を経由する受信ルートで非対称ルーティングを回避するには､オンプレミスのネットワークもまたサイト間 VPN よりも ExpressRoute を優先する必要があります｡ これは､BGP ローカル優先属性を使った次の構成で行うことができます｡ 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>サイト間 VPN 構成の詳細

次は､サイト間 VPN 接続に関係するプライマリー CE ルーターの構成の一覧です｡

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>ExpressRoute を使用した場所 2 のオンプレミス接続

オンプレミスで場所 2 に近接した場所にある 2 つ目 ExpressRoute 回線によって､オンプレミスの場所 2 がハブ VNET に接続されています｡ 次のポータル スクリーンショットは 2 つ目の ExpressRoute の構成を示しています｡

[![6]][6]

次のポータル スクリーンショットは､2 つ目の ExpressRoute 回線と Hub VNET 間の接続の構成を示しています｡

[![7]][7]

ExpressRoute1 は､別の Azure リージョンにあるリモート VNET にオンプレミスの Hub VNET と場所 1 の両方を接続しています｡

[![8]][8]

## <a name="further-reading"></a>参考資料

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続を組み合わせて使用する

#### <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリング上にサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet との間で、機密性とアンチリプレイ、信頼性、整合性を確保しながらプライベートにデータを交換することができます。 ExpressRoute Microsoft ピアリング上にトンネル モードでサイト間 IPsec VPN を構成する方法についての詳細は、[ExpressRoute Microsoft ピアリング上にサイト間 VPNを構成する方法][S2S-Over-ExR]に関するページを参照してください。 

Microsoft ピアリング上に S2S VPN を構成することに伴う重要な制限はスループットです。 IPsec トンネル上では、VPN GW の容量によりスループットが制限されます。 VPN GW のスループットは、ExpressRoute のスループットよりも低下します｡ このようなシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用することで、ExpressRoute の帯域幅使用率を最適化できます｡

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute のセキュアなフェールオーバー パスとしてのサイト間 VPN
ExpressRoute は、高可用性を確保するための冗長回線ペアとして提供されます。 複数の Azure リージョンに geo 冗長の ExpressRoute 接続を構成することができます。 また、ここで紹介したテスト構成のように、特定の Azure リージョン内で ExpressRoute 接続のフェールオーバー パスを設けたければ、サイト間 VPN を使用することができます。 ExpressRoute と S2S VPN の両方に同じプレフィックスがアドバタイズされた場合、ExpressRoute は S2S VPN よりも優先されます。 ExpressRoute と S2S VPN との間で非対称なルーティングを回避するために、オンプレミス ネットワーク構成でも、ExpressRoute を S2S VPN 接続よりも優先するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法についての詳細は、[ExpressRoute とサイト間の共存接続][ExR-S2S-CoEx]に関するページを参照してください。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>スポーク VNET と分岐先へのバックエンド接続の延長

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNET ピアリングを使用したスポーク VNET 接続

Hub-and-spoke VNET アーキテクチャは広く利用されています｡ Azure ではこのハブは仮想ネットワーク (VNet) であり､オンプレミスネットワークに接続しているスポーク VNET 間の接続の中心点の働きをします｡ スポークはハブとピアリングする VNet であり、ワークロードの分離に利用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャについての詳細は､｢[Hub-and-Spoke アーキテクチャ][Hub-n-Spoke]｣を参照してください｡

スポーク VNet は、同じリージョン内の VNet ピアリングを通じてハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用し、リモート ネットワークと通信を行うことができます。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>サイト間 VPN を使用した分岐 VNET 接続

分岐 VNet (異なるリージョンに存在) とオンプレミス ネットワークとをハブ VNet 経由で互いに通信させたい場合、Azure のネイティブ ソリューションは VPN を使用したサイト間 VPN 接続になります。 ハブにおけるルーティングには NVA を使用する選択肢もあります。

VPN ゲートウェイの構成については､[VPN ゲートウェイの構成][VPN]を参照してください｡ 高可用性の NVA のデプロイについては､[高可用性 NVA のデプロイ][Deploy-NVA]を参照してください。

## <a name="next-steps"></a>次の手順

テスト構成のコントロール プレーン分析と､このトポロジのさまざまな VNET/VLAN のビューについては､[コントロール プレーン分析][Control-Analysis]を参照してください｡

テスト構成のデータ プレーン分析および Azure ネットワーク監視機能のビューについては、[データ プレーン分析][Data-Analysis]を参照してください。

ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数や、ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数など、ExpressRoute のスケーリングに関するその他制限については、[ExpressRoute FAQ][ExR-FAQ] を参照してください。


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "スポーク VNet の VNet ピアリング"
[2]: ./media/backend-interoperability/HubVNet-peering.png "ハブ VNet の VNet ピアリング"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "分岐 VNET の VPN GW 構成"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 の構成"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute1 からハブ VNet ExR GW への接続の構成"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 の構成"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute2 からハブ VNet ExR GW への接続の構成"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "ExpressRoute2 からリモート VNet ExR GW への接続の構成"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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




