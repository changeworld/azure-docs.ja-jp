---
title: Azure Route Server に関してよく寄せられる質問
description: Azure Route Server についてよく寄せられる質問とその回答を紹介します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 47584994586e735647be4116fb49de610e5f97f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453271"
---
# <a name="azure-route-server-faq"></a>Azure Route Server に関する FAQ

## <a name="what-is-azure-route-server"></a>Azure Route Server とは何ですか。

Azure Route Server は、ネットワーク仮想アプライアンス (NVA) と仮想ネットワークの間のルーティングを簡単に管理できる、フル マネージド サービスです。

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server は単なる VM ですか。

いいえ。 Azure Route Server は、高可用性を使用して設計されたサービスです。 [Availability Zones](../availability-zones/az-overview.md) をサポートする Azure リージョンにデプロイされている場合は、ゾーンレベルの冗長性があります。

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>仮想ネットワークにはいくつのルート サーバーを作成できますか。

仮想ネットワーク内に作成できるルート サーバーは 1 つだけです。 これは、*RouteServerSubnet* という名前の専用サブネットにデプロイする必要があります。

### <a name="does-azure-route-server-support-virtual-network-peering"></a>Azure Route Server では仮想ネットワーク ピアリングがサポートされていますか。

はい。Azure Route Server をホストしている仮想ネットワークを別の仮想ネットワークにピアリングし、2 番目の仮想ネットワークでリモート ゲートウェイの使用を有効にした場合、Azure Route Server は、その仮想ネットワークのアドレス空間を学習し、ピアリングされたすべての NVA に送信します。 また、NVA から、ピアリングされた仮想ネットワーク内の VM のルーティング テーブルへのルートがプログラミングされます。 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure Route Server ではどのようなルーティング プロトコルがサポートされていますか。

Azure Route Server では、Border Gateway Protocol (BGP) のみがサポートされています。 仮想ネットワーク内の専用サブネットに Azure Route Server をデプロイする必要があるため、NVA ではマルチホップの外部 BGP をサポートする必要があります。 選択する [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) は、NVA で BGP を構成するときに Azure Route Server によって使用されるものと異なる必要があります。

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server では NVA と VM の間でデータ トラフィックがルーティングされますか。

いいえ。 Azure Route Server では、NVA との間でのみ BGP ルートが交換されます。 データ トラフィックは、NVA と宛先 VM の間で直接送受信されます。

### <a name="does-azure-route-server-store-customer-data"></a>Azure Route Server では、顧客データは保存されますか。
いいえ。 Azure Route Server が行うのは、BGP ルートを NVA と交換し、それらを仮想ネットワークに伝達することのみです。

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>Azure Route Server にパブリック IP アドレスが必要なのはなぜですか。

Azure Router Server では、Route Server の構成を管理するバックエンド サービスへの接続を確保する必要があるため、パブリック IP アドレスが必要になります。 

### <a name="does-azure-route-server-support-ipv6"></a>Azure Route Server では IPv6 がサポートされていますか。

いいえ。 IPv6 サポートは今後追加される予定です。 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Azure Route Server が複数の NVA から同じルートを受け取る場合、どのように処理されますか。

ルートの AS パスの長さが同じ場合、Azure Route Server は、仮想ネットワーク内の VM へのルートの複数のコピーを、それぞれ異なるネクスト ホップで作成します。 VM によってこのルートの宛先にトラフィックが送信されると、VM ホストによって等コスト マルチパス (ECMP) ルーティングが行われます。 ただし、1つの NVA が、他の NVA よりも短い AS パスの長さでルートを送信した場合、Azure Route Server は、この NVA に対してネクスト ホップが設定されているルートのみを、仮想ネットワーク内の VM にプログラムします。

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Azure Route Server は、受信したルートの BGP コミュニティを保持しますか。

はい。Azure Route Server は、BGP コミュニティを使用してルートを伝達します。

### <a name="what-is-the-bgp-timer-setting-of-azure-route-server"></a>Azure Route Server の BGP タイマーはどのように設定されていますか。
キープアライブ タイマーは 60 秒、停止タイマーは 180 秒に設定されています。

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>どの自律システム番号 (ASN) を使用できますか。

ネットワーク仮想アプライアンスで、独自のパブリック ASN またはプライベート ASN を使用できます。 Azure または IANA によって予約されている範囲を使用することはできません。
次の ASN は、Azure または IANA によって予約されています。

* Azure によって予約済みの ASN:
    * パブリック ASN: 8074、8075、12076
    * プライベート ASN: 65515、65517、65518、65519、65520
* [IANA によって予約済み](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)の ASN:
    * 23456、64496-64511、65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>32 ビット (4 バイト) の ASN を使用できますか。

いいえ。Azure Route Server でサポートされる ASN は、16 ビット (2 バイト) のみです。

### <a name="can-i-associate-a-user-defined-route-udr-to-the-routeserversubnet"></a>ユーザー定義ルート (UDR) を RouteServerSubnet に関連付けることはできますか。

いいえ。Azure Route Server は、RouteServerSubnet 上での UDR の構成をサポートしていません。

### <a name="can-i-associate-a-network-security-group-nsg-to-the-routeserversubnet"></a>ネットワーク セキュリティ グループ (NSG) を RouteServerSubnet に関連付けることはできますか。

いいえ。Azure Route Server は、RouteServerSubnet への NSG の関連付けをサポートしていません。

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>2 つのピアリングされた仮想ネットワーク内の 2 つのルート サーバーをピアリングし、ルート サーバーに接続されている NVA が互いに通信可能となるようにできますか? 

***トポロジ: NVA1 -> RouteServer1 -> (VNet ピアリング経由) -> RouteServer2 -> NVA2***

いいえ。Azure Route Server では、データ トラフィックは転送されません。 NVA を介したトランジット接続を有効にするには、NVA 間の直接接続 (IPsec トンネルなど) を設定し、動的なルート伝達にルート サーバーを使用します。 

### <a name="can-i-use-azure-route-server-to-direct-traffic-between-subnets-in-the-same-virtual-network-to-flow-inter-subnet-traffic-through-the-nva"></a>Azure Route Server を使用して、同じ仮想ネットワーク内のサブネット間でトラフィックを送信して、サブネット間のトラフィックを NVA 経由でフローさせることはできますか。

いいえ。 仮想ネットワーク、仮想ネットワークのピアリング、または仮想ネットワーク サービス エンドポイントに関連したトラフィックに使用されるシステム ルートは、BGP のルートの方が具体的であったとしても、優先されるルートとなります。 Azure Route Server は BGP を使用してルートをアドバタイズしますが、現時点では、これは仕様ではサポートされていません。 引き続き、UDR を使用してルートを強制的に上書きする必要があります。また、BGP を利用してこれらのルートを迅速にフェールオーバーすることはできません。 フェールオーバーの場合には、サードパーティのソリューションを使用して、API 経由で UDR を更新するか、HA ポート モードで Azure Load Balancer を使用してトラフィックを送信する必要があります。

Azure Route Server を使用して、異なる仮想ネットワーク内のサブネット間のトラフィックを、NVA を使用してフローするように誘導できます。 使用できる設計は、"スポーク" 仮想ネットワークごとに 1 つのサブネットであり、すべての仮想ネットワークが "ハブ" 仮想ネットワークとピアリングされていますが、これは非常に制限されており、仮想ネットワーク対サブネットへの Azure の最大制限についても考慮する必要があります。

## <a name="route-server-limits"></a><a name = "limitations"></a>Route Server の制限

Azure Route Server には、(デプロイごとに) 次の制限があります。

| リソース | 制限 |
|----------|-------|
| サポートされている BGP ピアの数 | 8 |
| 各 BGP ピアで Azure Route Server にアドバタイズできるルートの数 | 1000 |
| Azure Route Server で ExpressRoute または VPN ゲートウェイにアドバタイズできるルートの数 | 200 |
| Azure Route Server で対応できる、仮想ネットワークの最大 VM 数 (ピアリングされた仮想ネットワークの数を含む) | 2000 |

Azure Route Server でサポートできる VM の数は、ハード リミットではありません。 これは、Azure リージョン内で Route Server インフラストラクチャがどのようにデプロイされているかによって異なります。

NVA によって制限より多くのルートが公開された場合、BGP セッションは削除されます。 ゲートウェイと Azure Route Server の間で BGP セッションが削除された場合、オンプレミス ネットワークから Azure への接続は失われます。 詳しくは、「[Azure 仮想マシンのルーティングに関する問題を診断する](../virtual-network/diagnose-network-routing-problem.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

[Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認します。
