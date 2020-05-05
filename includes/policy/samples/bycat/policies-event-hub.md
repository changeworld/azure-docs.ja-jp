---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7adc0ee7cbb9453a25703b07a6224ccc0293a90d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82191028"
---
|名前 |説明 |効果 |Version |GitHub |
|---|---|---|---|---|
|[イベント ハブの名前空間から RootManageSharedAccessKey 以外のすべての承認規則を削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |イベント ハブ クライアントでは、名前空間内のすべてのキューおよびトピックへのアクセスを提供する名前空間レベルのアクセス ポリシーを使用してはいけません。 最小限の特権セキュリティ モデルに合わせるために、キューとトピックについてはエンティティ レベルでアクセス ポリシーを作成し、特定のエンティティのみへのアクセスを提供する必要があります |Audit、Deny、Disabled |1.0.1 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |
|[イベント ハブ インスタンスの承認規則を定義する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |最小特権のアクセスを付与する承認規則がイベント ハブ エンティティに存在することを監査します |AuditIfNotExists、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |
|[イベント ハブの診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |診断ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |2.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
