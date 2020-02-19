---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 1d5e916d9f8256c002f6810d1fc7aedebeba6481
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170010"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |Redis Cache に対して SSL 接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |1.0.0 |
