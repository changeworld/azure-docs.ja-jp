---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 654176074b99af060ed35297369b8f7eecb7e283
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453998"
---
## <a name="simultaneous-use-of-private-endpoints-and-virtual-network-service-endpoints"></a>プライベート エンドポイントと Virtual Network サービス エンドポイントを同時に使用する

[プライベート エンドポイント](../speech-services-private-link.md)と [Virtual Network サービス エンドポイント](../speech-service-vnet-service-endpoint.md)を使用して、同じ Speech リソースに同時にアクセスすることができます。 この同時使用を有効にするには、Azure portal の Speech リソースのネットワーク設定で **[Selected Networks and Private Endpoints]\(選択したネットワークとプライベート エンドポイント\)** オプションを使用する必要があります。 このシナリオでは、他のオプションはサポートされていません。
