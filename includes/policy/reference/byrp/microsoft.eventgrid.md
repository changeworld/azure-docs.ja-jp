---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7abd2823cb7489fea1f3cabed1f30e384ff28fed
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90021968"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid domains should use private links (Azure Event Grid ドメインではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid ドメインを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid topics should use private links (Azure Event Grid トピックではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid トピックを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
