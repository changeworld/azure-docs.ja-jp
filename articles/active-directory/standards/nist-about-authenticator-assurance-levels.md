---
title: Azure Active Directory による NIST Authenticator Assurance Level
description: Azure Active Directory に適用される Authenticator Assurance Level の概要
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34cf1b096963c5b83c359c6583573c709655bc1c
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517728"
---
# <a name="about-authenticator-assurance-levels"></a>Authenticator Assurance Level について

米国国立標準技術研究所 (NIST) は、ID ソリューションをデプロイする米国政府機関の技術要件を策定します。 [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) では、デジタル認証の実装についての技術的ガイドラインを定義しています。 これは、Authenticator Assurance Level (AAL) のフレームワークを使用して行われます。 AAL はデジタル ID の認証の強度を特徴付けしたものです。 そのガイダンスでは、失効を含む Authenticator のライフサイクルの管理も対象としています。 

標準には、11 個の要件カテゴリの AAL 要件が含まれています。

* 許可される Authenticator の種類

* Federal Information Processing Standards 140 (FIPS 140) 検証レベル (FIPS 140 要件は、[FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) 以降のリビジョンによって満たされます)

* 再認証

* セキュリティ コントロール

* 中間者攻撃 (MitM) への耐性

* 検証ツール偽装への耐性 (フィッシングへの耐性)

* 検証ツール侵害への耐性

* リプレイへの耐性

* 認証意図

* アイテム保持ポリシー

* プライバシー管理

## <a name="applying-nist-aals-in-your-environment"></a>環境に NIST AAL を適用する

> [!TIP]
> ビジネス上の理由、業界標準、またはコンプライアンス要件によって AAL3 を満たすことが規定されている場合を除き、少なくとも AAL 2 を満たすことが推奨されます。

一般に、AAL1 はパスワードのみのソリューションを受け入れるため、推奨されておらず、パスワードは、最も侵害されやすい認証の形式です。 「[パスワードは関係ない](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)」を参照してください。 

NIST では、AAL3 まで検証ツールの偽装 (資格情報のフィッシングとも呼ばれる) への耐性を必要としませんが、すべてのレベルでこの脅威に対処することが強く推奨されます。 Azure AD に参加しているか、ハイブリッド Azure AD に参加しているデバイスを必要とするなど、検証ツールの偽装への耐性を備えている Authenticator を選択できます。 Office 365 を使用している場合は、Office 365 Advanced Threat Protection、特に[フィッシング対策ポリシー](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies)を使用して、対処できます。

組織に適切な NIST AAL を評価する際に、組織全体が NIST 標準を満たす必要があるかどうか、または分離できる特定のユーザーとリソースのグループがあるかどうか、および特定のユーザーとリソースのグループのみに適用する NIST AAL 構成を検討できます。 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>セキュリティ コントロール、プライバシー管理、レコード保持ポリシー

Azure と Azure Government は、FedRAMP 認定の最高位である、[NIST SP 800-53 High Impact Level](https://nvd.nist.gov/800-53/Rev4/impact/high) の Provisional Authority to Operate (P-ATO) を合同認定委員会から獲得し、これにより、高度に機密性の高いデータを処理するために Azure と Azure Government の使用が認可されました。

これらの Azure および Azure Government の認定は、AAL1、AAL2、AAL3 のセキュリティ コントロール、プライバシー管理、および記録保持ポリシーの要件を満たしています。

Azure と Azure Government の FedRAMP 監査には、対象サービスのインフラストラクチャ、開発、運用、管理、およびサポートを網羅する情報セキュリティ管理システムが含まれます。 P-ATO が与えられても、クラウド サービス プロバイダーは、まだ協力しているすべての政府機関からの認可 (ATO) が必要です。 Azure、政府機関、またはそれらと協力する組織は、それぞれ独自のセキュリティ認可プロセスで Azure P-ATO を使用し、FedRAMP の要件を同様に満たす機関 ATO を発行するための基礎としてそれを利用できます。

Azure では、他のどのクラウド プロバイダーよりも多くの FedRAMP High Impact レベルのサービスをサポートし続けます。 また、Azure パブリック クラウドの FedRAMP High は多くの米国政府のお客様のニーズを満たしますが、より厳格な要件を持つ機関では、職員の高度なスクリーニングなどの追加の保護が提供される Azure Government に引き続き依存することになります。 Microsoft では、FedRAMP High 境界に対して、Azure Government で現在利用可能なすべての Azure パブリック サービスと、本年度に計画されているサービスを一覧表示しています。

さらに、Microsoft は、明確に規定されたレコード保持ポリシーによって、[顧客データの保護と管理](https://www.microsoft.com/trust-center/privacy/data-management)に完全にコミットしています。 世界のほとんどすべての国にお客様を抱えるグローバル企業として、Microsoft では、お客様を支援するための堅牢なコンプライアンス ポートフォリオを揃えています。 当社のコンプライアンス オファリングの完全な一覧については、「[Microsoft のコンプライアンス オファリング](https://docs.microsoft.com/compliance/regulatory/offering-home)」を参照してください。 

## <a name="next-steps"></a>次の手順 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を実現する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md) 