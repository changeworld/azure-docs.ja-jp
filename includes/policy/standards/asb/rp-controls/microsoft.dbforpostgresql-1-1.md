---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3bc47befc71881ac2e53a0d2999fd881b97a9d3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284324"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |プライベート エンドポイント接続は、Azure Database for PostgreSQL へのプライベート接続を有効にすることで、通信のセキュリティを強化します。 既知のネットワークからのトラフィックへのアクセスを有効にし、Azure 内の IP アドレスも含めて他のすべての IP アドレスからのアクセスを防ぐために、プライベート エンドポイント接続を構成します。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
