---
title: Azure Route Server に関してよく寄せられる質問
description: Azure Route Server についてよく寄せられる質問とその回答を紹介します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/22/2021
ms.author: duau
ms.openlocfilehash: 77b9cd7590b381a29fc0dc19b2a80b72afceca0a
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930301"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (プレビュー) に関する FAQ

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="what-is-azure-route-server"></a>Azure Route Server とは何ですか。

Azure Route Server は、ネットワーク仮想アプライアンス (NVA) と仮想ネットワークの間のルーティングを簡単に管理できる、フル マネージド サービスです。

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server は単なる VM ですか。

いいえ。 Azure Route Server は、高可用性を使用して設計されたサービスです。 [Availability Zones](../availability-zones/az-overview.md) をサポートする Azure リージョンにデプロイされている場合は、ゾーンレベルの冗長性があります。

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>仮想ネットワークにはいくつのルート サーバーを作成できますか。

VNet 内に作成できるルート サーバーは 1 つだけです。 これは、*RouteServerSubnet* という名前の指定されたサブネットにデプロイする必要があります。

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure Route Server では VNet ピアリングがサポートされていますか。

はい。 Azure Route Server をホスティングしている VNet を別の VNet にピアリングし、後者の VNet でリモート ゲートウェイの使用を有効にした場合、Azure Route Server はその VNet のアドレス空間を学習し、ピアリングされたすべての NVA に送信します。 また、ピアリングされた VNet 内の VM のルーティング テーブルに、NVA からのルートがプログラミングされます。 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure Route Server ではどのようなルーティング プロトコルがサポートされていますか。

Azure Route Server では、Border Gateway Protocol (BGP) のみがサポートされています。 仮想ネットワーク内の専用サブネットに Azure Route Server をデプロイする必要があるため、NVA ではマルチホップの外部 BGP をサポートする必要があります。 選択する [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) は、NVA で BGP を構成するときに Azure Route Server によって使用されるものと異なる必要があります。

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server では NVA と VM の間でデータ トラフィックがルーティングされますか。

いいえ。 Azure Route Server では、NVA との間でのみ BGP ルートが交換されます。 データ トラフィックは、NVA と宛先 VM の間で直接送受信されます。

### <a name="does-azure-route-server-store-customer-data"></a>Azure Route Server では、顧客データは保存されますか。
いいえ。 Azure Route Server が行うのは、BGP ルートを NVA と交換し、それらを仮想ネットワークに伝達することのみです。

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Azure Route Server が複数の NVA から同じルートを受け取る場合、どのように処理されますか。

ルートの AS パスの長さが同じ場合、Azure Route Server は、仮想ネットワーク内の VM へのルートの複数のコピーを、それぞれ異なるネクスト ホップで作成します。 VM によってこのルートの宛先にトラフィックが送信されると、VM ホストによって等コスト マルチパス (ECMP) ルーティングが行われます。 ただし、1つの NVA が、他の NVA よりも短い AS パスの長さでルートを送信した場合、Azure Route Server は、この NVA に対してネクスト ホップが設定されているルートのみを、仮想ネットワーク内の VM にプログラムします。

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Azure Route Server は、受信したルートの BGP コミュニティを保持しますか。

はい。Azure Route Server は、BGP コミュニティを使用してルートを伝達します。

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

## <a name="route-server-limits"></a><a name = "limitations"></a>Route Server の制限

Azure Route Server には、(デプロイごとに) 次の制限があります。

| リソース | 制限 |
|----------|-------|
| サポートされている BGP ピアの数 | 8 |
| 各 BGP ピアで Azure Route Server にアドバタイズできるルートの数 | 200 |
| Azure Route Server で ExpressRoute または VPN ゲートウェイにアドバタイズできるルートの数 | 200 |
| Azure Route Server で対応できる、仮想ネットワークの最大 VM 数 (ピアリングしている VNet の数を含む) | 6000 |

NVA によって制限より多くのルートが公開された場合、BGP セッションは削除されます。 ゲートウェイと Azure Route Server にこれが発生した場合は、オンプレミス ネットワークから Azure への接続が失われます。 詳しくは、「[Azure 仮想マシンのルーティングに関する問題を診断する](../virtual-network/diagnose-network-routing-problem.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

[Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認します。
