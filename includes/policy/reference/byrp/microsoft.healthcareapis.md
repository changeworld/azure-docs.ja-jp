---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d37b7f614f6c0f991dd975e048c9fda7a6f18d04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649940"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR では、保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |カスタマー マネージド キーを使用して、Azure API for FHIR に格納されるデータの保存時の暗号化を制御します (これが規制またはコンプライアンスの要件である場合)。 カスタマー マネージド キーを使用すると、サービス マネージド キーで実行される既定の暗号化レイヤーの上に 2 番目の暗号化レイヤーが追加されて、二重の暗号化が提供されることにもなります。 |audit、disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR には、承認されたプライベート エンドポイント接続が少なくとも 1 つ必要です。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/fhir-privatelink](../../../../articles/healthcare-apis/azure-api-for-fhir/configure-private-link.md) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |クロス オリジン リソース共有 (CORS) で API for FHIR へのアクセスをすべてのドメインに許可することは避けます。 API for FHIR を保護するには、すべてのドメインのアクセス権を削除し、接続が許可されるドメインを明示的に定義します。 |audit、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|[CORS で FHIR Service へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1c9040-c46a-4e81-9aea-c7850fbb3aa6) |クロス オリジン リソース共有 (CORS) で FHIR Service へのアクセスをすべてのドメインに許可することは避けます。 FHIR Service を保護するには、すべてのドメインのアクセス権を削除し、接続が許可されるドメインを明示的に定義します。 |audit、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Healthcare%20APIs/HealthcareAPIs_FHIR_Service_RestrictCORSAccess_Audit.json) |