---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: db8ae4bba6717cb1c0f27befd0897b60c2760ed2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059209"
---
## <a name="private-endpoints-and-vnet-service-endpoints"></a>プライベート エンドポイントと VNet サービス エンドポイント

Azure には、[Azure のプライベート バックボーン ネットワーク](https://azure.microsoft.com/global-infrastructure/global-network/)を使用したトラフィック トンネリング用のプライベート エンドポイントと VNet サービス エンドポイントがあります。 これらのエンドポイントの種類は、目的と基づいているテクノロジが類似しています。 ただし、2 つのテクノロジには違いがあります。ネットワークを設計する前に、それぞれの長所と短所についてさらに学習することをお勧めします。

選択を行う際には、いくつかの点を考慮する必要があります。
- どちらのテクノロジでも、VNet と音声リソースの間のトラフィックはパブリック インターネットを経由 *しません*。
- プライベート エンドポイントには、音声リソースに専用のプライベート IP アドレスがあります。 この IP アドレスには、特定の VNet とサブネット内でのみアクセスできます。 ネットワーク インフラストラクチャ内でこの IP アドレスへのアクセスを完全に制御できます。
- VNet サービス エンドポイントでは、音声リソースに専用のプライベート IP アドレスは *なく*、音声リソースに送信されたパケットをカプセル化し、Azure のバックボーン ネットワークを経由して直接配信します。
- どちらのテクノロジでも、オンプレミスのシナリオをサポートしています。 既定では、VNet サービス エンドポイントを使用する場合、仮想ネットワークからのアクセスに限定された Azure サービス リソースには、オンプレミス ネットワークからアクセスできませんが、これは[設定できます](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises)。
- VNet サービス ポイントは、トラフィックの発信元の VNet に基づいて音声リソースへのアクセスを制限する場合によく使用されます。
- Cognitive Services の場合、VNet サービス エンドポイントを有効にすると、**すべての** Cognitive Services リソースのトラフィックがプライベート バックボーン ネットワークを経由します。 これには、明示的なネットワーク アクセス構成が必要です (詳細については、[こちら](../speech-service-vnet-service-endpoint.md#configure-vnets-and-the-speech-resource-networking-settings)を参照してください)。 プライベート エンドポイントにはこの制限はありません。また、ネットワーク構成も柔軟になります。1 つのリソースにはプライベート バックボーンを経由してアクセスし、別のリソースに同じ VNet の同じサブネットを使用してパブリック インターネットを経由したてアクセスすることができます。
- プライベート エンドポイントには[追加コスト](https://azure.microsoft.com/pricing/details/private-link)が発生します。 VNet サービス エンドポイントは無料です。
- プライベート エンドポイントには、[追加の DNS 構成](../speech-services-private-link.md#turn-on-private-endpoints)が必要です。
- 1 つの音声リソースが、プライベート エンドポイントと VNet サービス エンドポイントの両方で同時に動作する場合があります。

運用環境の設計を決定する前に、両方の種類のエンドポイントを試してみることをお勧めします。 

- [Private Link とプライベート エンドポイントのドキュメント](../../../private-link/private-link-overview.md)
- [VNet サービス エンドポイントのドキュメント](../../../virtual-network/virtual-network-service-endpoints-overview.md)
