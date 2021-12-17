---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d9a313353c7fef2954c4696e029483652162b582
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595713"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントを作成すると、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 プライベート エンドポイントを作成するだけでは、パブリック エンドポイントは無効になりません。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[プライベート エンドポイントを持つ Azure File Sync を構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |指定されたストレージ同期サービス リソースに対してプライベート エンドポイントがデプロイされます。 これにより、インターネット アクセス可能なパブリック エンドポイントを使用せずに、組織のネットワークのプライベート IP アドレス空間内からストレージ同期サービスのリソースをアドレス指定できます。 1 つ以上のプライベート エンドポイントが存在するだけでは、パブリック エンドポイントは無効になりません。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[変更 - 公衆ネットワーク アクセスを無効にするように Azure File Sync を構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Azure File Sync のインターネット アクセス可能なパブリック エンドポイントが組織のポリシーによって無効にされます。 ストレージ同期サービスには、引き続き、そのプライベート エンドポイントを介してアクセスすることができます。 |Modify、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Azure File Sync の公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |パブリック エンドポイントを無効にすると、ストレージ同期サービス リソースへのアクセスを、組織のネットワーク上の承認されたプライベート エンドポイント宛ての要求に制限できます。 パブリック エンドポイントへの要求を許可しても、そのこと自体が安全でないということはありませんが、規制、法律、組織のポリシーの要件を満たすために、それを無効にすることが必要になる場合があります。 ストレージ同期サービスのパブリック エンドポイントを無効にするには、リソースの incomingTrafficPolicy を AllowVirtualNetworksOnly に設定します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
