---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddeb30e0c0c03629a0b08a127e62836c768bc030
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022595"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Configuration はカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |カスタマー マネージド キーは、暗号化キーを管理できるようにすることで、データ保護を強化します。 これは、多くの場合、コンプライアンス要件を満たすために必要となります。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[App Configuration はプライベート リンクを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |プライベート エンドポイント接続を使用すると、仮想ネットワーク上のクライアントは、プライベート リンク経由で Azure App Configuration に安全にアクセスできます。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
