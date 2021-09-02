---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453989"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>プライベート エンドポイントと仮想ネットワーク サービス エンドポイント

Azure では、[プライベート Azure バックボーン ネットワーク](https://azure.microsoft.com/global-infrastructure/global-network/)経由でトンネリングするトラフィックに対して、プライベート エンドポイントと仮想ネットワーク サービス エンドポイントを提供します。 これらの種類のエンドポイントは、目的と、基になるテクノロジが似ています。 しかし、2 つのテクノロジには違いがあります。 ネットワークを設計する前に、両方の長所と短所について学習することをお勧めします。

使用するテクノロジを決定する際には、以下の点を考慮する必要があります。
- どちらのテクノロジでも、仮想ネットワークと Speech リソース間のトラフィックはパブリック インターネットを経由しません。
- プライベート エンドポイントには、音声リソースに専用のプライベート IP アドレスがあります。 この IP アドレスには、特定の仮想ネットワークとサブネット内でのみアクセスできます。 ネットワーク インフラストラクチャ内でこの IP アドレスへのアクセスを完全に制御できます。
- 仮想ネットワーク サービス エンドポイントでは、Speech リソース専用のプライベート IP アドレスは提供されません。 代わりに、Speech リソースに送信されたすべてのパケットはカプセル化され、Azure バックボーン ネットワーク経由で直接配信されます。
- どちらのテクノロジでも、オンプレミスのシナリオをサポートしています。 既定では、仮想ネットワーク サービス エンドポイントを使用する場合、仮想ネットワークでセキュリティ保護された Azure サービス リソースには、オンプレミス ネットワークからはアクセスできません。 しかし、[その動作を変更](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises)できます。
- 仮想ネットワーク サービス エンドポイントは、トラフィックの発信元の仮想ネットワークに基づいて Speech リソースへのアクセスを制限するために、よく使用されます。
- Cognitive Services の場合、仮想ネットワーク サービス エンドポイントを有効にすると、すべての Cognitive Services リソースのトラフィックがプライベート バックボーン ネットワークを経由します。 これには、明示的なネットワーク アクセス構成が必要です。 (詳細については、「[仮想ネットワークと Speech リソースのネットワーク設定を構成する](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings)」のセクションを参照してください。) プライベート エンドポイントにはこの制限はないので、ネットワーク構成をより柔軟に行うことができます。 同じ仮想ネットワークの同じサブネットを使用し、1 つのリソースにはプライベート バックボーンを介してアクセスし、もう 1 つにはパブリック インターネットを介してアクセスすることができます。
- プライベート エンドポイントには[追加コスト](https://azure.microsoft.com/pricing/details/private-link)が発生します。 仮想ネットワーク サービス エンドポイントは、無料です。
- プライベート エンドポイントには、[追加の DNS 構成](../speech-services-private-link.md#turn-on-private-endpoints)が必要です。
- 1 つの Speech リソースが、プライベート エンドポイントと仮想ネットワーク サービス エンドポイントの両方で同時に動作する場合があります。

運用環境の設計を決定する前に、両方の種類のエンドポイントを試してみることをお勧めします。 

詳細については、次のリソースを参照してください。

- [Azure Private Link とプライベート エンドポイントのドキュメント](../../../private-link/private-link-overview.md)
- [仮想ネットワーク サービス エンドポイントのドキュメント](../../../virtual-network/virtual-network-service-endpoints-overview.md)
