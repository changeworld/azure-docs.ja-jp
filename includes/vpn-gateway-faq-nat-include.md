---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676938"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>NAT はすべての Azure VPN Gateway SKU でサポートされていますか?

NAT は、VpnGw2 から VpnGw5 および VpnGw2AZ から VpnGw5AZ でサポートされます。

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>VNet 間接続や P2S 接続で NAT は使用できますか?

いいえ。NAT は、**IPsec** クロスプレミス接続でのみサポートされます。

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>VPN ゲートウェイで使用できる NAT 規則はいくつですか?

VPN ゲートウェイには、最大 100 個の NAT 規則 (イングレス規則とエグレス規則の合計) を作成できます。

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>NAT は、VPN ゲートウェイのすべての接続に適用されるのですか?

NAT は、NAT 規則が設定されている接続に適用されます。 接続に NAT 規則が設定されていない場合、その接続では NAT は無効となります。 同じ VPN ゲートウェイに、NAT のある接続と NAT のない接続を混在させることができます。

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>Azure VPN ゲートウェイでは、どのような種類の NAT がサポートされていますか?

1 対 1 の静的 NAT のみです。 動的 NAT や NAT64 はサポートされていません。

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>アクティブ/アクティブの VPN ゲートウェイで NAT は機能しますか?

はい。 NAT は、アクティブ/アクティブとアクティブ/スタンバイの両方の VPN ゲートウェイで機能します。

### <a name="does-nat-work-with-bgp-connections"></a>NAT は BGP 接続で機能しますか?

はい。NAT で BGP を使用できます。 重要な考慮事項をいくつか以下に示します。

* 学習済みのルートとアドバタイズされたルートが、接続に関連付けられている NAT 規則に基づいて NAT 後のアドレス プレフィックス (外部マッピング) に確実に変換されるよう、NAT 規則の構成ページで **[Enable BGP Route Translation]\(BGP ルートの変換を有効にする\)** を選択します。 オンプレミスの BGP ルーターから、IngressSNAT 規則に定義されているとおりにプレフィックスがアドバタイズされるようにする必要があります。

* オンプレミス VPN ルーターが BGP スピーカーとピアの IP として APIPA (169.254.x.x) を使用している場合、ローカル ネットワーク ゲートウェイの **[BGP ピアの IP アドレス]** フィールドで直接 APIPA アドレスを使用します。 APIPA ではない通常のアドレスがオンプレミス VPN ルーターで使用されていて、それが VNet アドレス空間や他のオンプレミス ネットワーク空間と競合する場合は、IngressSNAT 規則で BGP ピア IP を重複しない一意のアドレスに変換し、ローカル ネットワーク ゲートウェイの **[BGP ピアの IP アドレス]** フィールドには NAT 後のアドレスを設定するようにしてください。

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>SNAT 規則に対応する DNAT 規則を作成する必要はありますか?

いいえ。 1 つの SNAT 規則で、特定のネットワークの **両方向** の変換が定義されます。

* IngressSNAT 規則では、オンプレミス ネットワークから Azure VPN ゲートウェイに **入ってくる** 送信元 IP アドレスの変換が定義されます。 また、同じオンプレミス ネットワークに向かって VNet から出ていく送信先 IP アドレスの変換も処理されます。

* EgressSNAT 規則では、オンプレミス ネットワークに向かって Azure VPN ゲートウェイから出ていく VNet の送信元 IP アドレスの変換が定義されます。 また、その接続を介して VNet に入ってくるパケットの送信先 IP アドレスの変換も EgressSNAT 規則によって処理されます。

* どちらの場合も、**DNAT 規則は必要ありません**。

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>VNet またはローカル ネットワーク ゲートウェイのアドレス空間に複数のプレフィックスがある場合は、どうすればよいでしょうか。 それらすべてに NAT を適用できますか? それとも、サブセットだけでしょうか?

NAT に必要なプレフィックスごとに NAT 規則を 1 つ作成する必要があります。各 NAT 規則に含めることができる NAT のアドレス プレフィックスは 1 つだけです。 たとえば、ローカル ネットワーク ゲートウェイのアドレス空間が 10.0.1.0/24 と 10.0.2.0/25 から成る場合、次のように 2 つの規則を作成できます。

* IngressSNAT 規則 1: 10.0.1.0/24 を 100.0.1.0/24 にマップ
* IngressSNAT 規則 2: 10.0.2.0/25 を 100.0.2.0/25 にマップ

2 つの規則で、対応するアドレス プレフィックスのプレフィックス長が一致している必要があります。 同じことは、VNet アドレス空間の EgressSNAT 規則にも言えます。

> [!IMPORTANT]
> 上記の接続に 1 つしか規則をリンクさせなかった場合、もう一方のアドレス空間は変換 **されません**。

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>複数の EgressSNAT 規則を使用して、VNet アドレス空間を異なるオンプレミス ネットワークの異なるプレフィックスに変換することはできますか?

はい。同じ VNet アドレス空間に複数の EgressSNAT 規則を作成し、その EgressSNAT 規則を異なる接続に適用することができます。 EgressSNAT 規則がない接続の場合、

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>同じ IngressSNAT 規則を異なる接続に使用できますか?

はい。冗長性を確保するために同じオンプレミス ネットワークに対する接続が複数ある場合に一般的に使用されています。 複数の接続が別々のオンプレミス ネットワーク用である場合は、同じイングレス規則を使用することはできません。

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>NAT 接続にはイングレスとエグレスの両方の規則が必要なのですか?

オンプレミス ネットワークのアドレス空間が VNet のアドレス空間と重複するときは、同じ接続にイングレス規則とエグレス規則の両方が必要です。 VNet のアドレス空間が、接続されているすべてのネットワークで一意である場合、それらの接続に EgressSNAT 規則は必要ありません。 イングレス規則を使用すると、オンプレミス ネットワーク間でのアドレスの重複を回避できます。