---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170290"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[許可されるストレージ アカウントの SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |このポリシーを使用して、対象の組織でデプロイできるストレージ アカウント SKU のセットを指定できます。 |拒否 |1.0.0 |
|[ストレージ アカウントに対する制限のないネットワーク アクセスの監査](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |ストレージ アカウントのファイアウォール設定で無制限のネットワーク アクセスを監査します。 代わりに、許可されているネットワークからのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます |Audit、Disabled |1.0.0 |
|[ストレージ アカウントで Advanced Threat Protection をデプロイします](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |このポリシーを使用して、ストレージ アカウントで Advanced Threat Protection を有効にすることができます。 |DeployIfNotExists、Disabled |1.0.0 |
|[ストレージ アカウントの geo 冗長ストレージを有効にする必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |このポリシーは、geo 冗長ストレージが有効になっていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |1.0.0 |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |お使いのストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |1.0.0 |
|[ストレージ アカウントは、信頼された Microsoft サービスからのアクセスを許可する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |ストレージ アカウントとやり取りする一部の Microsoft サービスは、ネットワーク ルールでアクセスを許可できないネットワークから実行されます。 この種のサービスを意図したとおりに動作させるには、一連の信頼できる Microsoft サービスがネットワーク ルールをバイパスするのを許可します。 そうすると、これらのサービスはストレージ アカウントにアクセスするために強力な認証を使用します。 |Audit、Deny、Disabled |1.0.0 |
|[ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |新しい Azure Resource Manager をお使いのストレージ アカウントに使用して、セキュリティの拡張機能を提供します。たとえば、アクセス制御の強化 (RBAC)、監査の改善、Azure Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレット取得のための Key Vault へのアクセス、Azure AD ベースの認証、セキュリティ管理を容易にするタグとリソース グループのサポートがあります |Audit、Deny、Disabled |1.0.0 |
