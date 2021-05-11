---
title: Azure Active Directory による NIST AAL1 の実現
description: Azure Active Directory で NIST 認証保証レベル 1 (AAL 1) を実現するためのガイダンスです。
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
ms.openlocfilehash: 640c401beb42b07465f533fa14b5b7fd4b0200e5
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294076"
---
# <a name="achieving-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Azure Active Directory で NIST 認証保証レベル 1 (AAL 1) を実現する

アメリカ国立標準技術研究所 (NIST) は、ID ソリューションを導入する米国政府機関の技術要件の策定を行っています。 これらの要件を満たすことは、政府機関と協力している組織にも求められます。 この記事では、NIST 認証保証レベル 1 (AAL1) を実現する手順について説明します。 

AAL 1 の達成を試みる前に確認すべきリソース:
* [NIST の概要](nist-overview.md) - 異なる AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md) - 重要な用語と認証の種類です。
* [NIST 認証システムの種類](nist-authenticator-types.md) - 各認証の種類について理解します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - AAL の構成要素、Microsoft Azure Active Directory 認証方法がどのようにマップされるか、およびトラステッド プラットフォーム モジュール (TCM) について理解します。 

## <a name="permitted-authenticator-types"></a>許可される認証システムの種類

 AAL1 を実現するには、NIST の単一または多要素で[許可された認証システム](nist-authenticator-types.md)を使用できます。 次の表は、[AAL2](nist-authenticator-assurance-level-2.md) および [AAL3](nist-authenticator-assurance-level-2.md) でカバーされていないものを示したものです。

| Azure AD の認証方法| NIST 認証システムの種類 |
| - | - |
| Password |記憶シークレット |
| 電話 (SMS)|  帯域外 |
|  FIDO 2 セキュリティ キー <br>iOS 用の Microsoft Authenticator アプリ (パスワードレス)<br>ソフトウェア TPM と Windows Hello for Business <br>スマートカード (ADFS) |  多要素の暗号化ソフトウェア |

> [!TIP]
> ビジネス上の理由、業界標準、またはコンプライアンス要件によって AAL3 を満たすよう定められている場合を除き、少なくとも AAL 2 を満たすことをお勧めします。

## <a name="fips-140-validation"></a>FIPS 140 検証

### <a name="verifier-requirements"></a>検証の要件

Azure AD では、認証に関連するすべての暗号処理に、Windows FIPS 140 レベル 1 の全体の検証が行われた暗号   
モジュールが使用されています。 したがって、これは政府機関が必要とする FIPS 140 に準拠した検証ツールです。

## <a name="man-in-the-middle-mitm-resistance"></a>Man-in-the-middle (MitM) への耐性 

要求側と Azure AD 間のすべての通信は認証済みの保護されたチャネルを介して実行されるため、MitM 攻撃への耐性を有しています。 これは、AAL1、AAL2、および AAL3 の MitM への耐性要件を満たしています。

## <a name="next-steps"></a>次の手順 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を実現する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を実現する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を実現する](nist-authenticator-assurance-level-3.md) 