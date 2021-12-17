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
ms.openlocfilehash: 7005028eb61c53788e7283b9a0af5878b3e3961b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214797"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Azure Private Link を使用したプライベート ネットワーク アクセス (プレビュー) 

[Azure Private Link](../private-link/private-link-overview.md)  は、 [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) 内のプライベート エンドポイント経由で Azure リソース ( [Azure Event Hubs](../event-hubs/event-hubs-about.md)、 [Azure Storage](../storage/common/storage-introduction.md)、 [Azure Cosmos DB](../cosmos-db/introduction.md) など) や Azure でホストされている顧客とパートナーのサービスにアクセスできるようにするサービスです。 

同様に、Time Series Insights インスタンス用のプライベート エンドポイントを使用すると、仮想ネットワーク内に配置されたクライアントは Private Link 経由でこのインスタンスに安全にアクセスできます。 

## <a name="about-the-private-endpoints"></a>プライベート エンドポイントについて

プライベート エンドポイントでは、Azure VNet アドレス空間の IP アドレスが使用されます。 プライベート ネットワーク上のクライアントと Time Series Insights インスタンスの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上の Private Link を経由するため、パブリック インターネットに曝されることがなくなります。 このシステムを視覚化すると、次のようになります。 

[![Time Series プライベート リンクの DNS](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

顧客はまた、パブリック アクセスのための TSI 環境へのアクセスをブロックして、VNET からしかアクセスできないようにすることもできます。 Time Series Insights インスタンス用のプライベート エンドポイントを構成すると、Time Series Insights インスタンスをセキュリティで保護して、パブリックに曝されないようにしたり、VNet からのデータ流出を回避したりできるようになります。 

[![Time Series プライベート リンクのネットワーク](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

プライベート エンドポイントが有効になり、パブリック アクセスが制限された後、顧客が TSI 環境にアクセスするには、TSI エクスプローラーに別のアドレスを使用する必要があります。 そのアドレスは、Azure portal の [概要] セクションで見つけることができます。 

## <a name="next-steps"></a>次のステップ

* 詳細については、[TSI 環境用にプライベート エンドポイントを構成する方法](./how-to-private-links.md)に関するページを参照してください。