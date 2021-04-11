---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 34455ba61095bfa65c9c82d0829bdc54b2b897e4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091131"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[マネージド アプリケーションのアプリケーション定義では、ユーザー指定のストレージ アカウントを使用する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |これが規制またはコンプライアンスの要件である場合、独自のストレージ アカウントを使用してアプリケーション定義データを制御してください。 管理アプリケーション定義は、作成時に指定したストレージ アカウント内に保存することを選択できます。これにより、場所とアクセスを完全に管理し、規制コンプライアンス要件を満たすことができます。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[マネージド アプリケーションの関連付けのデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |選択したリソースの種類を、指定したマネージド アプリケーションに関連付ける関連付けリソースをデプロイします。  このポリシーのデプロイでは、入れ子になったリソースの種類はサポートされていません。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
