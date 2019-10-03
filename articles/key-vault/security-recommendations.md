---
title: Azure Key Vault のセキュリティに関する推奨事項
description: Azure Key Vault のセキュリティに関する推奨事項。 このガイダンスを実装することにより、共有責任モデルに記載されたセキュリティの義務を果たすのに役立ちます
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/23/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 90965618b1d2cb126d56453d46a5bebc85319c0f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219646"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault のセキュリティに関する推奨事項

この記事には、Azure Key Vault のセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、共有責任モデルに記載されたセキュリティに関する義務を果たすのに役立ちます。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みについて詳しくは、「[クラウド コンピューティングについての共有責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)」をご覧ください。

この記事に記載されているいくつかの推奨事項は、Azure Security Center によって自動的に監視できます。 Azure Security Center は、Azure のリソースを保護するための防御の最前線です。 これにより Azure リソースのセキュリティの状態が定期的に分析され、潜在的なセキュリティ脆弱性が特定されます。 その後、それらに対処する方法の推奨事項を提供します。

- Azure Security Center の推奨事項の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項](../security-center/security-center-recommendations.md)」を参照してください。
- Azure Security Center の詳細については、「[Azure Security Center とは](../security-center/security-center-intro.md)」を参照してください。

## <a name="recommendations"></a>Recommendations

| Category | 推奨 | 説明 | Security Center |
|-|-|----|--|
| データ保護 |論理的な削除を有効にする | [論理的な削除](key-vault-ovw-soft-delete.md)を使用すると、削除されたコンテナーとコンテナー オブジェクトを復旧できます |  - |
| データ保護 | コンテナー データへのアクセスを制限する  | 最小限の特権の原則に従って、コンテナー データにアクセスできる組織のメンバーを制限します |  - |
| ID 管理とアクセス管理 | 共同作成者のアクセス権を持つユーザーの数を制限する | ユーザーがキー コンテナーの管理プレーンに対する共同作成者のアクセス許可を持っている場合は、そのユーザーは Key Vault のアクセス ポリシーを設定することで、データ プレーンへのアクセス権を自分自身に付与できます。 キー コンテナーへの共同作成者ロールのアクセス権を持つユーザーは、厳密に管理する必要があります。 アクセス権が許可される必要があるユーザーだけが、コンテナーにアクセスして管理できることを確実にします。 「[キー コンテナーへのアクセスをセキュリティで保護する](key-vault-secure-your-key-vault.md)」を読むことができます | - |
| 監視 | Key Vault で診断ログを有効にする必要がある | ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 | [はい](../security-center/security-center-identity-access.md) |
| 監視 | Azure Key Vault のログにアクセスできるユーザーを制限する | [Key Vault ログ](key-vault-logging.md)では、コンテナー、キー、シークレットの作成または削除など、コンテナーで実行されたアクティビティに関する情報を保存し、調査中に使用することができます |  - |
| ネットワーク |ネットワークの露出を制限する | ネットワークのアクセスは、コンテナーのアクセスを必要とするソリューションで使用される仮想ネットワークに制限される必要があります。 「[Azure Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)」の情報を確認してください | - |

## <a name="next-steps"></a>次の手順

アプリケーション プロバイダーに追加のセキュリティ要件があるかどうかを確認する。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](../security/fundamentals/abstract-develop-secure-apps.md)を参照してください。
