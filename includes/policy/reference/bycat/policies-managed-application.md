---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 21178fb4e03c0a7b9b81a2f54492c183a7fa8dd8
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810135"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[マネージド アプリケーションのアプリケーション定義では、ユーザー指定のストレージ アカウントを使用する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |これが規制またはコンプライアンスの要件である場合、独自のストレージ アカウントを使用してアプリケーション定義データを制御してください。 管理アプリケーション定義は、作成時に指定したストレージ アカウント内に保存することを選択できます。これにより、場所とアクセスを完全に管理し、規制コンプライアンス要件を満たすことができます。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[マネージド アプリケーションの関連付けのデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |選択したリソースの種類を、指定したマネージド アプリケーションに関連付ける関連付けリソースをデプロイします。  このポリシーのデプロイでは、入れ子になったリソースの種類はサポートされていません。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
