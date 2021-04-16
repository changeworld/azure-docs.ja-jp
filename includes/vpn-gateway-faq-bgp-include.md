---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953458"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP はすべての Azure VPN Gateway SKU でサポートされていますか。

BGP は、Basic SKU を除くすべての Azure VPN Gateway SKU でサポートされています。

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Azure Policy の VPN ゲートウェイでは BGP を使用できますか。

いいえ、BGP は、ルートベースの VPN ゲートウェイでのみサポートされています。

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>どの AS 番号 (自律システム番号) を使用できますか。

オンプレミスのネットワークと Azure 仮想ネットワークの両方に対して独自のパブリック AS 番号またはプライベート AS 番号を使用できます。 Azure または IANA によって予約されている範囲を使用することはできません。

次の ASN は、Azure または IANA によって予約されています。
* Azure によって予約済みの ASN:

  * パブリック ASN: 8074、8075、12076
  * プライベート ASN: 65515、65517、65518、65519、65520
* [IANA によって予約済み](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)の ASN:

  * 23456、64496 ～ 64511、65535 ～ 65551、および 429496729

これらの ASN は、Azure VPN ゲートウェイに接続する際にオンプレミスの VPN デバイスには指定できません。

### <a name="can-i-use-32-bit-4-byte-asns"></a>32 ビット (4 バイト) の AS 番号を使用できますか。

はい、VPN Gateway では 32 ビット (4 バイト) の AS 番号がサポートされるようになりました。 10 進数形式の AS 番号を使用してを構成するには、PowerShell、Azure CLI、または Azure SDK を使用します。

### <a name="what-private-asns-can-i-use"></a>どのプライベート AS 番号を使用できますか。

プライベート AS 番号の有効な範囲は次のとおりです。

* 64512から 65514 および 65521 から 65534

これらの AS 番号は、IANA または Azure で使用するために予約されていないため、Azure VPN ゲートウェイに割り当てるために使用できます。

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>VPN Gateway では、BGP ピア IP にどのようなアドレスが使用されますか。

VPN Gateway は、既定では *GatewaySubnet* 範囲から 1 つの IP アドレスをアクティブ/スタンバイ VPN ゲートウェイに割り当てるか、2 つの IP アドレスをアクティブ/アクティブ VPN ゲートウェイに割り当てます。 これらのアドレスは、VPN ゲートウェイの作成時に自動的に割り当てられます。 PowerShell を使用するか、Azure portal で特定することによって、実際に割り当てられた BGP IP アドレスを取得できます。 PowerShell で **Get-AzVirtualNetworkGateway** を使用し、**bgpPeeringAddress** プロパティを探します。 Azure portal の **[ゲートウェイの構成]** ページで、 **[BGP AS 番号の構成]** プロパティを確認します。

オンプレミスの VPN ルーターが BGP IP アドレスとして **APIPA** IP アドレス (169.254.x.x) を使用している場合は、Azure VPN ゲートウェイで、追加の **Azure APIPA BGP IP アドレス** を指定する必要があります。 Azure VPN Gateway では、ローカル ネットワーク ゲートウェイに指定されているオンプレミスの APIPA BGP ピアで使用する APIPA アドレス、または非 APIPA のオンプレミス BGP ピア用のプライベート IP アドレスが選択されます。 詳細については、「[BGP を構成する](../articles/vpn-gateway/bgp-howto.md)」を参照してください。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN デバイスの BGP ピア IP アドレスに関する要件はどんなものですか。

オンプレミスの BGP ピア アドレスを、ご使用の VPN デバイスまたは VPN ゲートウェイの VNet アドレス空間のパブリック IP アドレスと同じにすることはできません。 VPN デバイスでは BGP ピア IP に別の IP アドレスを使用してください。 デバイス上のループバック インターフェイスに割り当てられたアドレス (通常の IP アドレスまたは APIPA アドレス) を使用することができます。 デバイスで BGP に APIPA アドレスを使用する場合は、「[BGP を構成する](../articles/vpn-gateway/bgp-howto.md)」の説明に従って、Azure VPN ゲートウェイで APIPA BGP IP アドレスを指定する必要があります。 この場所を表している、対応するローカル ネットワーク ゲートウェイにこのアドレスを指定します。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP を使用する際、ローカル ネットワーク ゲートウェイのアドレス プレフィックスとして何を指定する必要がありますか。

> [!IMPORTANT]
>
>これは、以前に記載された要件からの変更です。 接続に BGP を使用する場合は、対応するローカル ネットワーク ゲートウェイ リソースの **[アドレス空間]** フィールドを空のままにしておきます。 Azure VPN Gateway では、IPsec トンネルを介したオンプレミスの BGP ピア IP へのホスト ルートが内部的に追加されます。 **[アドレス空間]** フィールドに /32 ルートを追加しないでください。 これは冗長であり、APIPA アドレスをオンプレミスの VPN デバイスの BGP IP として使用する場合、このフィールドに追加することはできません。 **[アドレス空間]** フィールドにその他のプレフィックスを追加すると、BGP 経由で学習したルートに加えて、Azure VPN ゲートウェイで静的ルートとして追加されます。
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>オンプレミスの VPN ネットワークと Azure 仮想ネットワークの両方に同じ AS 番号を使用できますか。

いいえ、BGP を使用して接続している場合は、オンプレミスのネットワークと Azure 仮想ネットワークに異なる AS 番号を割り当てる必要があります。 Azure VPN ゲートウェイには、クロスプレミス接続向けに BGP が有効になっているかどうかにかかわらず、AS 番号の既定値として 65515 が割り当てられています。 VPN ゲートウェイを作成する際に異なる AS 番号を割り当てて既定値をオーバーライドするか、ゲートウェイの作成後に AS 番号を変更することができます。 対応する Azure ローカル ネットワーク ゲートウェイにオンプレミスの AS 番号を割り当てる必要があります。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN ゲートウェイはどのようなアドレス プレフィックスをアドバタイズしますか。

ゲートウェイでは、オンプレミスの BGP デバイスに次のルートをアドバタイズします。

* 仮想ネットワークのアドレス プレフィックス。
* Azure VPN ゲートウェイに接続されている各ローカル ネットワーク ゲートウェイのアドレス プレフィックス。
* Azure VPN ゲートウェイに接続されている他の BGP ピアリング セッションから学習したルート。ただし、既定のルートまたは仮想ネットワーク プレフィックスと重複しているルートは除外されます。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>何個のプレフィックスを Azure VPN Gateway にアドバタイズできますか。

Azure VPN Gateway では最大 4,000 個のプレフィックスがサポートされます。 プレフィックスの数がこの制限を超えると、BGP セッションは切断されます。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Azure VPN ゲートウェイへの既定のルート (0.0.0.0/0) をアドバタイズすることはできますか。

はい。 これにより、すべての仮想ネットワークのエグレス トラフィックがオンプレミス サイトに強制的に送信されることに注意してください。 また、仮想ネットワーク VM が、インターネットからのパブリック通信 (RDP や SSH など) をインターネットから VM に直接受け入れなくなります。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>自分の仮想ネットワーク プレフィックスとまったく同じプレフィックスをアドバタイズすることはできますか。

いいえ、お使いの仮想ネットワーク アドレス プレフィックスのいずれかと同じプレフィックスのアドバタイズは、Azure によってブロックされるか、フィルター処理されます。 ただし、仮想ネットワークの内部に存在するアドレス空間のスーパーセットであるプレフィックスをアドバタイズすることができます。 

たとえば、仮想ネットワークでアドレス空間 10.0.0.0/16 を使用した場合、10.0.0.0/8 をアドバタイズすることができます。 しかし、10.0.0.0/16 や 10.0.0.0/24 をアドバタイズすることはできません。

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>仮想ネットワーク間の接続に BGP を使用できますか。

はい、クロスプレミス接続と仮想ネットワーク間接続の両方で BGP を使用できます。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Azure VPN ゲートウェイで BGP 接続を BGP 以外の接続と混在させることはできますか。

はい、同じ Azure VPN ゲートウェイに対して BGP 接続と非 BGP 接続の両方を混在させることができます。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN Gateway では、BGP トランジット ルーティングをサポートしていますか。

はい。BGP トランジット ルーティングはサポートされています。ただし、Azure VPN ゲートウェイは既定のルートを他の BGP ピアにアドバタイズしません。 複数の Azure VPN ゲートウェイでトランジット ルーティングを有効にするには、仮想ネットワーク間のすべての中間接続で BGP を有効にする必要があります。 詳細については、[BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) に関する記事を参照してください。

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN ゲートウェイとオンプレミス ネットワークの間で複数のトンネルを確立することはできますか。

はい、Azure VPN ゲートウェイとオンプレミス ネットワークの間に複数のサイト間 (S2S) VPN トンネルを確立することができます。 これらのトンネルはすべて Azure VPN ゲートウェイのトンネルの合計数にカウントされる点に注意してください。さらに、両方のトンネルの BGP を有効にする必要があります。

たとえば、Azure VPN ゲートウェイとオンプレミス ネットワークのいずれかの間に 2 つの冗長トンネルがある場合は、Azure VPN ゲートウェイの合計クォータから 2 つのトンネルが消費されることになります。

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>BGP で 2 つの Azure 仮想ネットワークの間に複数のトンネルを確立することはできますか。

はい。ただし、少なくとも 1 つの仮想ネットワーク ゲートウェイが、アクティブ/アクティブ構成になっている必要があります。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Azure ExpressRoute とサイト間 VPN の共存構成でサイト間 VPN に BGP を使用することはできますか。

はい。

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP ピアリング セッション向けにオンプレミスの VPN デバイスに何を追加する必要がありますか。

VPN デバイスに Azure BGP ピア IP アドレスのホスト ルートを追加します。 このルートは、IPsec サイト間 VPN トンネルを指しています。 たとえば、Azure VPN ピア IP が 10.12.255.30 の場合は、10.12.255.30 のホスト ルートを VPN デバイスに追加し、対応する IPsec トンネル インターフェイスの次ホップ インターフェイスを指定する必要があります。

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>仮想ネットワーク ゲートウェイは、BGP を使用したサイト間接続用の BFD をサポートしていますか。

いいえ。 双方向転送検出 (BFD) は、標準の BGP "keepalive" を使用するよりも、近隣のダウンタイムを迅速に検出するために BGP で使用できるプロトコルです。 BFD では、LAN 環境で動作するように設計された秒未満タイマーが使用されますが、パブリック インターネットやワイド エリア ネットワーク接続向けであはりません。

パブリック インターネット経由の接続では、特定のパケットが遅延したりドロップされたりすることは珍しくありません。そのため、これらの積極的なタイマーの導入は不安定さを増長する可能性があります。 この不安定さによって、ルートが BGP によって抑制される可能性があります。 別の方法として、既定の 60 秒の "keepalive" 間隔と 180 秒のホールド タイマーより低いタイマーを使用してオンプレミスのデバイスを構成することができます。 その結果、収束時間が短縮されます。

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Azure VPN ゲートウェイは、BGP ピアリング セッションまたは接続を開始しますか?

ゲートウェイは、VPN ゲートウェイのプライベート IP アドレスを使用して、ローカル ネットワーク ゲートウェイ リソースに指定されたオンプレミス BGP ピア IP アドレスに対する BGP ピアリング セッションを開始します。 オンプレミス BGP IP アドレスが APIPA 範囲内にあるか、通常のプライベート IP アドレスであるかは関係ありません。 オンプレミス VPN デバイスに BGP IP として APIPA アドレスが使用されている場合、接続を開始するための構成を BGP スピーカーに対して行う必要があります。