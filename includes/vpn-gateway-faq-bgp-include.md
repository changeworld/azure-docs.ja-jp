---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6054fe5f71f54794d4974a71cdfd61a7959534ff
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92082204"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP はすべての Azure VPN Gateway SKU でサポートされていますか。
BGP は、Basic SKU を除くすべての Azure VPN Gateway SKU でサポートされています。

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Azure のポリシーベースの VPN ゲートウェイでは BGP を使用できますか。
いいえ、BGP は、ルートベースの VPN ゲートウェイでのみサポートされています。

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>どの ASN (自律システム番号) を使用できますか。
オンプレミスのネットワークと Azure 仮想ネットワークの両方に対して独自のパブリック ASN またはプライベート ASN を使用できます。ただし、Azure または IANA に予約されている範囲を**除きます**。

> [!IMPORTANT]
>
> 次の ASN は、Azure または IANA によって予約されています。
> * Azure によって予約済みの ASN:
>    * パブリック ASN: 8074、8075、12076
>    * プライベート ASN: 65515、65517、65518、65519、65520
> * [IANA によって予約済み](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)の ASN
>    * 23456、64496 ～ 64511、65535 ～ 65551、および 429496729
>
> これらの ASN は、Azure VPN ゲートウェイに接続する際にオンプレミスの VPN デバイスには指定できません。
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>32 ビット (4 バイト) の ASN (自律システム番号) は使用できますか。
はい。Azure VPN Gateway では、32 ビット (4 バイト) の ASN がサポートされるようになりました。 10 進数形式で ASN を使用して構成するには、PowerShell/CLI/SDK を使用してください。

### <a name="what-private-asns-can-i-use"></a>どのようなプライベート ASN を使用できますか。
使用できるプライベート ASN の範囲は次のとおりです。

* 64512 ～ 65514、65521 ～ 65534

これらの ASN は、IANA または Azure で使用するために予約されていないため、Azure VPN Gateway に割り当てるために使用できます。

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN ゲートウェイでは、BGP ピア IP にどのようなアドレスが使用されますか。

* 既定では、Azure VPN ゲートウェイは、GatewaySubnet 範囲から 1 つの IP アドレスをアクティブ/スタンバイ VPN ゲートウェイに割り当てるか、2 つの IP アドレスをアクティブ/アクティブ VPN ゲートウェイに割り当てます。 これらのアドレスは、VPN ゲートウェイの作成時に自動的に割り当てられます。 PowerShell (Get-AzVirtualNetworkGateway、"bgpPeeringAddress" プロパティを探してください) を使用して、または Azure portal ([ゲートウェイの構成] ページの [BGP ASN の構成] プロパティの下) で、割り当てられた実際の BGP IP アドレスを取得できます。

* オンプレミスの VPN ルーターが BGP IP アドレスとして **APIPA** IP アドレス (169.254.x.x) を使用している場合は、Azure VPN ゲートウェイで、追加の **Azure APIPA BGP IP アドレス**を指定する必要があります。 Azure VPN ゲートウェイでは、ローカル ネットワーク ゲートウェイに指定されているオンプレミスの APIPA BGP ピアで使用する APIPA アドレス、または非 APIPA のオンプレミス BGP ピア用のプライベート IP アドレスが選択されます。 詳細については、「[BGP を構成する](../articles/vpn-gateway/bgp-howto.md)」を参照してください。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN デバイスの BGP ピア IP アドレスに関する要件はどんなものですか。
オンプレミスの BGP ピア アドレスを、ご使用の VPN デバイスまたは VPN Gateway の VNet アドレス空間のパブリック IP アドレスと同じにすることは**できません**。 VPN デバイスでは BGP ピア IP に別の IP アドレスを使用してください。 デバイス上のループバック インターフェイスに割り当てられたアドレス (通常の IP アドレスまたは APIPA アドレス) を使用することができます。 デバイスで BGP に APIPA アドレスを使用する場合は、「[BGP を構成する](../articles/vpn-gateway/bgp-howto.md)」の説明に従って、Azure VPN ゲートウェイで APIPA BGP IP アドレスを指定する必要があります。 この場所を表している、対応するローカル ネットワーク ゲートウェイにこのアドレスを指定します。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP を使用する際、ローカル ネットワーク ゲートウェイのアドレス プレフィックスとして何を指定する必要がありますか。

> [!IMPORTANT]
>
>これは、以前に記載された要件からの変更です。 接続に BGP を使用する場合は、対応するローカル ネットワーク ゲートウェイ リソースの **[アドレス空間]** フィールドを "***空***" のままにしておく必要があります。 Azure VPN ゲートウェイでは、IPsec トンネルを介したオンプレミスの BGP ピア IP へのホスト ルートが内部的に追加されます。 [アドレス空間] フィールドに /32 ルートを追加**しないでください**。 これは冗長であり、APIPA アドレスをオンプレミスの VPN デバイスの BGP IP として使用する場合、このフィールドに追加することはできません。 [アドレス空間] フィールドにその他のプレフィックスを追加すると、BGP 経由で学習したルートに加えて、Azure VPN ゲートウェイで**静的ルート**として追加されます。
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>オンプレミスの VPN ネットワークと Azure Vnet の両方に同じ ASN を使用できますか。
いいえ、BGP を使用して接続している場合は、オンプレミスのネットワークと Azure Vnet に異なる ASN を割り当てる必要があります。 Azure VPN Gateway には、クロスプレミス接続向けに BGP が有効になっているかどうかにかかわらず、ASN の既定値として 65515 が割り当てられています。 VPN ゲートウェイを作成する際に異なる ASN を割り当てて既定値をオーバーライドするか、ゲートウェイの作成後に ASN を変更することができます。 対応する Azure ローカル ネットワーク ゲートウェイにオンプレミスの ASN を割り当てる必要があります。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN ゲートウェイはどのようなアドレス プレフィックスをアドバタイズしますか。
Azure VPN ゲートウェイでは、オンプレミスの BGP デバイスに次のルートをアドバタイズします。

* VNet のアドレス プレフィックス
* Azure VPN ゲートウェイに接続されている各ローカル ネットワーク ゲートウェイのアドレス プレフィックス
* Azure VPN Gateway に接続されている他の BGP ピアリング セッションから学習したルート。ただし、**VNet プレフィックスと重複している既定のルートは除外**されます。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>何個のプレフィックスを Azure VPN ゲートウェイにアドバタイズできますか。
最大 4,000 プレフィックスがサポートされています。 プレフィックスの数がこの制限を超えると、BGP セッションは切断されます。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Azure VPN ゲートウェイへの既定のルート (0.0.0.0/0) をアドバタイズすることはできますか。
はい。

その場合、すべてのオンプレミス サイトへの VNet エグレス トラフィックが強制され、インターネットから VM への RDP や SSH など、インターネットからのパブリックな通信を VNet VM が直接受信できなくなることに注意してください。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>自分の仮想ネットワーク プレフィックスとまったく同じプレフィックスをアドバタイズすることはできますか。

いいえ、お使いの仮想ネットワーク アドレス プレフィックスのいずれかと同じプレフィックスのアドバタイズは、Azure プラットフォームによってブロックされるか、フィルター処理されます。 ただし、仮想ネットワークの内部に存在するアドレス空間のスーパーセットであるプレフィックスをアドバタイズすることができます。 

たとえば、仮想ネットワークでアドレス空間 10.0.0.0/16 を使用した場合、10.0.0.0/8 をアドバタイズすることができます。 しかし、10.0.0.0/16 や 10.0.0.0/24 をアドバタイズすることはできません。

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>VNet 間の接続で BGP を使用できますか。
はい、クロスプレミス接続と VNet 間接続の両方で BGP を使用できます。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Azure VPN ゲートウェイで BGP 接続を BGP 以外の接続と混在させることはできますか。
はい、同じ Azure VPN ゲートウェイに対して BGP 接続と非 BGP 接続の両方を混在させることができます。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN ゲートウェイでは、BGP トランジット ルーティングをサポートしていますか。
はい。BGP トランジット ルーティングはサポートされています。ただし、Azure VPN Gateway は既定のルートを他の BGP ピアにアドバタイズ**しません**。 複数の Azure VPN ゲートウェイでトランジット ルーティングを有効にするには、中間にあるすべての VNet 接続で BGP を有効にする必要があります。 詳細については、[BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) に関する記事を参照してください。

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN ゲートウェイとオンプレミス ネットワークの間で複数のトンネルを確立することはできますか。
はい、Azure VPN ゲートウェイとオンプレミス ネットワークの間に複数の S2S VPN トンネルを確立することができます。 これらのトンネルはすべて Azure VPN ゲートウェイのトンネルの合計数にカウントされる点に注意してください。さらに、両方のトンネルの BGP を有効にする必要があります。

たとえば、Azure VPN ゲートウェイとオンプレミス ネットワークのいずれかの間に 2 つの冗長トンネルがある場合は、Azure VPN ゲートウェイの合計クォータから 2 つのトンネルが消費されることになります。

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>BGP で 2 つの Azure Vnet の間に複数のトンネルを確立することはできますか。
はい。ただし、少なくとも 1 つの仮想ネットワーク ゲートウェイが、アクティブ/アクティブ構成になっている必要があります。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>ExpressRoute/サイト間 VPN 共存構成でサイト間 VPN に BGP を使用することはできますか。
はい。 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP ピアリング セッション向けにオンプレミスの VPN デバイスに何を追加する必要がありますか。
IPsec S2S VPN トンネルを示す Azure BGP ピア IP アドレスのホスト ルートを VPN デバイスに追加する必要があります。 たとえば、Azure VPN ピア IP が "10.12.255.30" の場合は、"10.12.255.30" のホスト ルートを VPN デバイスに追加し、対応する IPsec トンネル インターフェイスの次ホップ インターフェイスを指定する必要があります。

### <a name="does-the-virtual-network-gateway-support-bidirectional-forwarding-detection-bfd-for-site-to-site-connections-with-bgp"></a>Virtual Network ゲートウェイでは、BGP を使用したサイト間接続で双方向転送検出 (BFD) がサポートされていますか。
不正解です。 双方向転送検出 (BFD) は、標準の BGP keepalive を使用するよりも、近隣のダウンタイムを迅速に検出するために BGP で使用できるプロトコルです。 BFD では、LAN 環境で動作するように設計された秒未満タイマーが使用されますが、パブリック インターネットやワイド エリア ネットワーク接続向けであはりません。 パブリック インターネット経由の接続では、特定のパケットが遅延したりドロップされたりすることは珍しくありません。そのため、これらの積極的なタイマーの導入は不安定さを増長し、最終的にルートが BGP によって抑制される可能性があります。 別の方法として、既定の 60 秒の KeepAlive 間隔と 180 秒のホールド タイマーより低いタイマーを使用してオンプレミスのデバイスを構成し、収束時間を短縮することができます。
