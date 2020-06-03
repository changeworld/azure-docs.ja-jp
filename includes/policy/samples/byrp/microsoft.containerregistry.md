---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f10f0d5cbd76597dd08c0beb172a51195d83d266
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235677"
---
|名前 |説明 |効果 |Version |GitHub |
|---|---|---|---|---|
|[コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |カスタマー マネージド キー (CMK) を使用して暗号化が有効になっていないコンテナー レジストリを監査します。 CMK 暗号化の詳細については、[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) を参照してください。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |ネットワーク (IP または VNET) ルールが構成されておらず、既定ですべてのネットワーク アクセスを許可するコンテナー レジストリを監査します。 少なくとも 1 つの IP ルールやファイアウォール ルールまたは構成済みの仮想ネットワークを含むコンテナー レジストリは、準拠していると見なされます。 コンテナー レジストリのネットワーク ルールについては、[https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) を参照してください。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |1 つ以上の承認されたプライベート エンドポイント接続がないコンテナー レジストリを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) を参照してください。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
