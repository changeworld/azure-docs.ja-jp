---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 865bbb093c9a02c0989a54d08bf1d956ffb3a3ef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429436"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Batch アカウントではデータの暗号化にカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |カスタマー マネージド キーを使用して、Batch アカウントのデータの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK) をご覧ください。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_CustomerManagedKey_Audit.json) |
|[Batch アカウントでメトリック アラート ルールを構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |必須メトリックを有効にするための Batch アカウントにおけるメトリック アラート ルールの構成を監査します |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
|[Batch アカウントでは公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c5a0ae-5e48-4738-b093-65e23a060488) |Batch アカウントで公衆ネットワーク アクセスを無効にすると、プライベート エンドポイントからのみ Batch アカウントにアクセスできるようになるため、セキュリティが向上します。 公衆ネットワーク アクセスを無効にする方法の詳細については、[https://docs.microsoft.com/azure/batch/private-connectivity](https://docs.microsoft.com/azure/batch/private-connectivity) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_DisablePublicNetworkAccess_Audit.json) |
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
