---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: e0c002114aafa81528b7092d0eed8f38e009a759
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810275"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management サービスで仮想ネットワークをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |API Management のサポートされている SKU を使用して、仮想ネットワークにサービスをデプロイすると、高度な API Management ネットワークとセキュリティ機能のロックが解除されるため、ネットワーク セキュリティの構成をより細かく制御できます。 詳細については、[https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management サービスではパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf73bd95-24da-4a4f-96b9-4e8b94b402bd) |API Management サービスのセキュリティを向上させるために、エンドポイントがパブリック インターネットに公開されていないことを確認します。 一部のパブリック エンドポイントは、管理 API への直接アクセス、Git を使用した構成の管理、セルフホステッド ゲートウェイ構成などのユーザー シナリオをサポートするために、API Management サービスによって公開されます。 これらの機能をいずれも使用していない場合は、対応するエンドポイントを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_AuditIfNotExist.json) |
|[API Management サービスには仮想ネットワークが使用されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network のデプロイにより、セキュリティが強化され、分離が行われ、インターネット ルーティングできないネットワークに API Management サービスを配置して、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用してオンプレミス ネットワークに接続できます。これにより、ネットワークやオンプレミス内のバックエンド サービスにアクセスできるようになります。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[パブリック ネットワーク アクセスを無効にするように API Management サービスを構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ca8c8ac-3a6e-493d-99ba-c5fa35347ff2) |API Management サービスのセキュリティを向上させるために、パブリック エンドポイントを無効にします。 一部のパブリック エンドポイントは、管理 API への直接アクセス、Git を使用した構成の管理、セルフホステッド ゲートウェイ構成などのユーザー シナリオをサポートするために、API Management サービスによって公開されます。 これらの機能をいずれも使用していない場合は、対応するエンドポイントを無効にする必要があります。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_DeployIfNotExist.json) |