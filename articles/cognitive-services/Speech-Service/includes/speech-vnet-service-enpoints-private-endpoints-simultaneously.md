---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 8a1253e7ac88d2fe8b557c48dd846b48de59fba3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059224"
---
## <a name="simultaneous-use-of-private-endpoints-and-vnet-service-endpoints"></a>プライベート エンドポイントと VNet サービス エンドポイントを同時に使用する

[プライベート エンドポイント](../speech-services-private-link.md) と [VNet サービス エンドポイント](../speech-service-vnet-service-endpoint.md) を使用して、同じ音声リソースに同時にアクセスすることができます。 ただし、プライベート エンドポイントと VNet サービス エンドポイントを同時に有効にするには、Azure portal の音声リソースのネットワーク設定で **[選択されたネットワークとプライベート エンドポイント]** オプションを使用する必要があります。 このシナリオでは、他のすべてのオプションはサポートされていません。
