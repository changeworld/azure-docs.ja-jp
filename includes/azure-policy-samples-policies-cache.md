---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 31bf6382558565198f0f133c5df721b4a227cc5f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77370714"
---
|名前 |説明 |効果 |Version |source |
|---|---|---|---|
|[Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Redis Cache に対して SSL 接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
