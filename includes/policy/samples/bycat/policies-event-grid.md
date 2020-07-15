---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4ca82cbc03471de5e7f488b65d7b35ec4685e5e0
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313806"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid domains should use private links (Azure Event Grid ドメインではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid ドメインを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid topics should use private links (Azure Event Grid トピックではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid トピックを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
