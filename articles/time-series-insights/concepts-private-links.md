---
title: プライベート リンク - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 でのプライベート リンクとパブリック アクセス制限の概要。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207775"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Azure Private Link を使用したプライベート ネットワーク アクセス (プレビュー) 

[Azure Private Link](/azure/private-link/private-link-overview)  は、 [Azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) 内のプライベート エンドポイント経由で Azure リソース ( [Azure Event Hubs](/azure/event-hubs/event-hubs-about)、 [Azure Storage](/azure/storage/common/storage-introduction)、 [Azure Cosmos DB](/azure/cosmos-db/introduction) など) や Azure でホストされている顧客とパートナーのサービスにアクセスできるようにするサービスです。 

同様に、Time Series Insights インスタンス用のプライベート エンドポイントを使用すると、仮想ネットワーク内に配置されたクライアントは Private Link 経由でこのインスタンスに安全にアクセスできます。 

## <a name="about-the-private-endpoints"></a>プライベート エンドポイントについて

プライベート エンドポイントでは、Azure VNet アドレス空間の IP アドレスが使用されます。 プライベート ネットワーク上のクライアントと Time Series Insights インスタンスの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上の Private Link を経由するため、パブリック インターネットに曝されることがなくなります。 このシステムを視覚化すると、次のようになります。 

[![Time Series プライベート リンクの DNS](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

顧客はまた、パブリック アクセスのための TSI 環境へのアクセスをブロックして、VNET からしかアクセスできないようにすることもできます。 Time Series Insights インスタンス用のプライベート エンドポイントを構成すると、Time Series Insights インスタンスをセキュリティで保護して、パブリックに曝されないようにしたり、VNet からのデータ流出を回避したりできるようになります。 

[![Time Series プライベート リンクのネットワーク](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

プライベート エンドポイントが有効になり、パブリック アクセスが制限された後、顧客が TSI 環境にアクセスするには、TSI エクスプローラーに別のアドレスを使用する必要があります。 そのアドレスは、Azure portal の [概要] セクションで見つけることができます。 

## <a name="next-steps"></a>次のステップ

* 詳細については、[TSI 環境用にプライベート エンドポイントを構成する方法](./how-to-private-links.md)に関するページを参照してください。 