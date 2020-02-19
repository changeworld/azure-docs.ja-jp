---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169903"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[許可される場所](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |このポリシーでは、リソースをデプロイするときに組織が指定できる場所を制限できます。 geo コンプライアンス要件を強制するために使用されます。 リソース グループ Microsoft.AzureActiveDirectory/b2cDirectories や、「グローバル」リージョンを使用するリソースを除外します。 |deny |1.0.0 |
|[リソース グループが許可される場所](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |このポリシーでは、組織がリソース グループを作成できる場所を制限できます。 geo コンプライアンス要件を強制するために使用されます。 |deny |1.0.0 |
|[許可されるリソースの種類](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |このポリシーでは、組織でデプロイできるリソースの種類を指定できるようになります。 このポリシーの影響を受けるのは、'tags' と 'location' をサポートするリソースの種類のみです。 すべてのリソースを制限するには、このポリシーを複製し、'mode' を 'All' に変更してください。 |deny |1.0.0 |
|[リソースの場所がリソース グループの場所と一致することの監査](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |リソースの場所がそのリソース グループの場所と一致することを監査します |監査 |1.0.0 |
|[カスタム RBAC 規則の使用監査](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |1.0.0 |
|[カスタム サブスクリプションの所有者ロールが作成されていないこと](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |このポリシーでは、カスタム サブスクリプションの所有者ロールが存在しないことを確認します。 |Audit、Disabled |1.0.0 |
|[許可されないリソースの種類](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |このポリシーでは、組織でデプロイできないリソースの種類を指定できるようになります。 |拒否 |1.0.0 |
