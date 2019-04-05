---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553983"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [デプロイあたりの Web または worker ロール数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| デプロイあたりの[インスタンスの入力エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)数 |25 |25 |
| デプロイあたりの[入力エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)数 |25 |25 |
| デプロイあたりの[内部エンドポイント](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)数 |25 |25 |
| デプロイあたりの[ホストされるサービスの証明書数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>Web または worker ロールの Azure Cloud Service には、それぞれ運用環境用とステージング環境用の 2 つのデプロイを作成することができます。 この制限は、個別のロールの数、つまり、構成を示しています。 この制限は、ロールあたりのインスタンスの数、つまり、スケーリングを示しています。

