---
title: Azure Relay のネットワーク セキュリティ
description: この記事では、プライベート エンドポイントからのアクセスを構成する方法について説明します
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984234"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay のネットワーク セキュリティ 
この記事では、Azure Relay で次のセキュリティ機能を使用する方法について説明します。 

- IP ファイアウォール規則 (プレビュー)
- プライベート エンドポイント (プレビュー)

> [!NOTE]
> Azure Relay では、ネットワーク サービス エンドポイントはサポートされていません。 


## <a name="ip-firewall"></a>IP ファイアウォール 
既定では、要求に有効な認証と承認がある限り、Relay 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Relay へのアクセスを特定の既知のサイトからのみに制限したいシナリオで役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure ExpressRoute](/azure/expressroute/expressroute-faqs#supported-services) で Relay を使用する場合、オンプレミス インフラストラクチャ IP アドレスからのトラフィックのみ許可する**ファイアウォール規則**を作成できます。 

IP ファイアウォール規則は、Relay 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Relay 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

詳細については、[Relay の名前空間の IP ファイアウォールを構成する方法](ip-firewall-virtual-networks.md)に関する記事を参照してください

## <a name="private-endpoints"></a>プライベート エンドポイント

Azure **Private Link サービス**を使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (Azure Relay、Azure Service Bus、Azure Event Hubs、Azure Storage、Azure Cosmos DB など) や、Azure でホストされている顧客またはパートナーのサービスにアクセスできます。 詳細については、「[Azure Private Link とは (プレビュー)](../private-link/private-link-overview.md)」を参照してください。

**プライベート エンドポイント**はネットワーク インターフェイスであり、それを使用することで、仮想ネットワークで実行されているワークロードから、**プライベート リンク リソース** (Relay 名前空間など) を持つサービスに対してプライベートで安全に接続することができます。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute、VPN 接続、またはパブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由するので、パブリック インターネットから漏えいすることがなくなります。 特定の Azure Relay 名前空間への接続を許可することにより、アクセスをきめ細かく制御できます。

> [!NOTE]
> 現在、この機能は**プレビュー段階**にあります。 

詳細については、[プライベート エンドポイントを構成する方法](private-link-service.md)に関するページを参照してください


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [IP ファイアウォールを構成する方法](ip-firewall-virtual-networks.md)
- [プライベート エンドポイントを構成する方法](private-link-service.md)