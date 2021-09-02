---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 649578b574f6d00964e9489fcbd25e1848ab3323
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103381"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management サービスで仮想ネットワークをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |API Management のサポートされている SKU を使用して、仮想ネットワークにサービスをデプロイすると、高度な API Management ネットワークとセキュリティ機能のロックが解除されるため、ネットワーク セキュリティの構成をより細かく制御できます。 詳細については、[https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management サービスには仮想ネットワークが使用されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network のデプロイにより、セキュリティが強化され、分離が行われ、インターネット ルーティングできないネットワークに API Management サービスを配置して、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用してオンプレミス ネットワークに接続できます。これにより、ネットワークやオンプレミス内のバックエンド サービスにアクセスできるようになります。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |