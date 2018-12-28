---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886205"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [デプロイあたりの Web/worker ロール数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [インスタンスの入力エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) 数 |25 |25 |
| [入力エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) 数 |25 |25 |
| [内部エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) 数 |25 |25 |
| デプロイあたりの[ホストされるサービスの証明書数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>Web/worker ロールの Cloud Service には、それぞれ運用環境用とステージング環境用の 2 つのデプロイを作成することができます。 この制限は、ロールあたりのインスタンス数 (スケール) ではなく、別個のロール (構成) の数を指してることに注意してください。

