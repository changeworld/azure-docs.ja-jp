---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a9e461aab44e523bade31c8a679c258f8706ab1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513538"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[すべてのインターネット トラフィックはデプロイされた Azure Firewall を介してルーティングする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |一部のサブネットが次世代のファイアウォールで保護されていないことを Azure Security Center で確認しました。 Azure Firewall またはサポートされている次世代のファイアウォールを使用してアクセスを制限することにより、潜在的な脅威からサブネットを保護してください |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[仮想ネットワークは、指定された仮想ネットワーク ゲートウェイを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |このポリシーは、指定された仮想ネットワーク ゲートウェイを既定のルートが指していない場合に、仮想ネットワークを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
