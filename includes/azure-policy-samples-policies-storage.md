---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: a4c9aae28175e31fdcded22595275e4b32c333e9
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262430"
---
|名前 |説明 |効果 |Version |GitHub |
|---|---|---|---|---|
|[許可されるストレージ アカウントの SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7433c107-6db4-4ad1-b57a-a76dce0154a1) |このポリシーを使用して、対象の組織でデプロイできるストレージ アカウント SKU のセットを指定できます。 |拒否 |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json)
|[ストレージ アカウントに対する制限のないネットワーク アクセスの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントのファイアウォール設定で無制限のネットワーク アクセスを監査します。 代わりに、許可されているネットワークからのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます |Audit、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json)
|[ストレージ アカウントで Advanced Threat Protection をデプロイします](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361c2074-3595-4e5d-8cab-4f21dffc835c) |このポリシーを使用して、ストレージ アカウントで Advanced Threat Protection を有効にすることができます。 |DeployIfNotExists、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json)
|[ストレージ アカウントの geo 冗長ストレージを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |このポリシーは、geo 冗長ストレージが有効になっていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json)
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |お使いのストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json)
|[ストレージ アカウントは、信頼された Microsoft サービスからのアクセスを許可する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |ストレージ アカウントとやり取りする一部の Microsoft サービスは、ネットワーク ルールでアクセスを許可できないネットワークから実行されます。 この種のサービスを意図したとおりに動作させるには、一連の信頼できる Microsoft サービスがネットワーク ルールをバイパスするのを許可します。 そうすると、これらのサービスはストレージ アカウントにアクセスするために強力な認証を使用します。 |Audit、Deny、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json)
|[ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |新しい Azure Resource Manager をお使いのストレージ アカウントに使用して、セキュリティの拡張機能を提供します。たとえば、アクセス制御の強化 (RBAC)、監査の改善、Azure Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレット取得のための Key Vault へのアクセス、Azure AD ベースの認証、セキュリティ管理を容易にするタグとリソース グループのサポートがあります |Audit、Deny、Disabled |1.0.0 |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json)
