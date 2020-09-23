---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9cb570572fb055da40e9b0a47797593410983ccd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982222"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |カスタマー マネージド キー (CMK) を使用した暗号化が有効になっていないコンテナー レジストリを監査します。 Azure によって、サービス マネージド キーが使用され、保存されているレジストリ コンテンツが自動的に暗号化されます。 Azure Key Vault 内で作成して管理するキーを使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 CMK 暗号化の詳細については、[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |ネットワーク ルールまたはファイアウォール (IP) ルールが構成されていないため、既定ではすべてのネットワーク アクセスを許可しているコンテナー レジストリを監査します。 ネットワーク アクセスを制限すると、コンテナー レジストリを潜在的な脅威から保護することができます。 1 つ以上の IP ルールやファイアウォール ルール、または構成済みの仮想ネットワークを含むコンテナー レジストリは、準拠していると見なされます。 コンテナー レジストリのネットワーク ルールの詳細については、[https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) および [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |1 つ以上の承認されたプライベート エンドポイント接続がないコンテナー レジストリを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でのプライベート エンドポイント接続があるリソースに安全にアクセスできます。 その後、パブリック アクセスを無効にし、レジストリへの接続に確実にプライベート リンクのみを使用できるようにします。 詳細については、[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
