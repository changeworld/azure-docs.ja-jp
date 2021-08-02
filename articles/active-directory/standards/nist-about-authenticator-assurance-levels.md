---
title: Azure Active Directory による NIST 認証システムの保証レベル
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
ms.openlocfilehash: 72462bf14fb8c287335e0497cbaa00102521b310
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889852"
---
# <a name="about-authenticator-assurance-levels"></a>認証システムの保証レベルについて

米国国立標準技術研究所 (NIST) は、ID ソリューションを実装する米国政府機関の技術要件の策定を行っています。 [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) では、デジタル認証の実装についての技術的ガイドラインを定義しています。 これは、認証システムの保証レベル (AAL) のフレームワークを使用して行われます。 AAL はデジタル ID の認証の強度を特徴付けしたものです。 そのガイダンスでは、失効を含む認証システムのライフサイクルの管理も対象としています。 

標準には、これらの要件カテゴリの AAL 要件が含まれています。

* 許可される Authenticator の種類

* Federal Information Processing Standards 140 (FIPS 140) 検証レベル (FIPS 140 要件は、[FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) 以降のリビジョンによって満たされます)

* 再認証

* セキュリティ コントロール

* 中間者攻撃 (MitM) への耐性

* 検証ツール偽装への耐性 (フィッシングへの耐性)

* 検証ツール侵害への耐性

* リプレイへの耐性

* 認証意図

* 記録アイテム保有ポリシー

* プライバシー管理

## <a name="apply-nist-aals-in-your-environment"></a>環境に NIST AAL を適用する

> [!TIP]
> 少なくとも AAL2 を満たすようにすることをお勧めします。 ビジネス上の理由、業界標準、またはコンプライアンス要件により必要な場合は、AAL3 を満たすようにします。

一般に、AAL1 はパスワードのみのソリューションを受け入れるため、推奨されておらず、パスワードは、最も侵害されやすい認証の形式です。 詳細については、[あなたの Pa$$word は関係ありません](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)というブログ記事を参照してください。 

NIST では、AAL3 までの検証ツールの偽装 (資格情報のフィッシングとも呼ばれる) への耐性を必要としませんが、すべてのレベルでこの脅威に対処することが強く推奨されます。 デバイスが Azure Active Directory (Azure AD) またはハイブリッド Azure AD に参加していることを必要とするなど、検証ツールの偽装への耐性を備えている認証システムを選択できます。 Office 365 を使用している場合は、Office 365 Advanced Threat Protection、特に[フィッシング対策ポリシー](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies)を使用できます。

組織に適した NIST AAL を評価する際には、組織全体が NIST の標準を満たす必要があるかどうかを検討してください。 特定のユーザーのグループとリソースを分離できる場合は、NIST AAL の構成を特定のユーザーのグループとリソースのみに適用できることがあります。 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>セキュリティ コントロール、プライバシー管理、レコード保持ポリシー

Azure と Azure Government は、[NIST SP 800-53 High Impact レベル](https://nvd.nist.gov/800-53/Rev4/impact/high)の P-ATO (Provisional Authority to Operate) を合同認定委員会から獲得しています。 このレベルは、FedRAMP 認定の最高基準を表しており、Azure と Azure Government を使用して機密性の高いデータを処理することを認可しています。

これらの Azure および Azure Government の認定は、AAL1、AAL2、AAL3 のセキュリティ コントロール、プライバシー管理、記録アイテム保有ポリシーの要件を満たしています。

Azure と Azure Government の FedRAMP 監査には、対象サービスのインフラストラクチャ、開発、運用、管理、およびサポートを網羅する情報セキュリティ管理システムが含まれます。 P-ATO が与えられても、クラウド サービス プロバイダーは、提携しているすべての政府機関からの認可 (ATO) が必要となります。 Azure、政府機関、またはそれらと協力する組織は、独自のセキュリティ認可プロセスで Azure P-ATO を使用できます。 機関または組織は、FedRAMP の要件も満たす機関の ATO を発行するための基礎としてそれを利用できます。

Azure では、他のどのクラウド プロバイダーよりも多くの FedRAMP High Impact レベルのサービスをサポートし続けます。 また、Azure パブリック クラウドの FedRAMP High は多くの米国政府のお客様のニーズを満たしますが、より厳格な要件を持つ機関は Azure Government に依存します。 Azure Government では、職員の高度なスクリーニングなどの追加の保護機能が提供されます。 Microsoft では、FedRAMP High 境界に対して、Azure Government で現在利用可能なすべての Azure パブリック サービスと、本年度に計画されているサービスを一覧表示しています。

さらに、Microsoft は、明確に規定されたレコード保持ポリシーによって、[顧客データの保護と管理](https://www.microsoft.com/trust-center/privacy/data-management)に完全にコミットしています。 世界のほとんどすべての国にお客様を抱えるグローバル企業として、Microsoft では、お客様を支援するための堅牢なコンプライアンス ポートフォリオを揃えています。 コンプライアンス オファリングの完全な一覧については、「[Microsoft のコンプライアンス オファリング](/compliance/regulatory/offering-home)」を参照してください。 

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md)