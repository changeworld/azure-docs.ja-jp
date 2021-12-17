---
title: Azure Active Directory で NIST AAL1 を達成する
description: Azure Active Directory を使用して NIST Authenticator Assurance Level 1 (AAL1) を達成するためのガイダンスです。
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
ms.openlocfilehash: d38dfd79eefe8b7b1bfb8119a62b139b654f2fe5
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540473"
---
# <a name="achieve-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Azure Active Directory を使用して NIST Authenticator Assurance Level 1 を達成する

米国国立標準技術研究所 (NIST) は、ID ソリューションを実装する米国政府機関の技術要件を策定します。 これらの要件を満たすことは、政府機関と協力している組織にも求められます。 

Authenticator Assurance Level 1 (AAL1) の達成を試みる前に、次のリソースを参照することをお勧めします。
* [NIST の概要](nist-overview.md): さまざまな AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md): 重要な用語と認証の種類です。
* [NIST 認証システムの種類](nist-authenticator-types.md): 認証システムの種類それぞれについて説明します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md): AAL の構成要素と、それらの要素に対する Azure Active Directory (Azure AD) 認証方法のマッピング、トラステッド プラットフォーム モジュール (TPM) の概要について取り上げます。 

## <a name="permitted-authenticator-types"></a>許可される Authenticator の種類

 AAL1 の達成には、単一要素認証または多要素認証に関して NIST で[許可されている Authenticator ](nist-authenticator-types.md) であればどれでも使用できます。 次の表は、[AAL2](nist-authenticator-assurance-level-2.md) および [AAL3](nist-authenticator-assurance-level-2.md) でカバーされていないものを示したものです。

| Azure AD の認証方法| NIST 認証子の種類 |
| - | - |
| Password |記憶シークレット |
| 電話 (SMS)|  帯域外 |
|  FIDO 2 セキュリティ キー <br>iOS 用の Microsoft Authenticator アプリ (パスワードレス)<br>ソフトウェア TPM と Windows Hello for Business <br>スマート カード (Active Directory フェデレーション サービス) |  多要素の暗号化ソフトウェア |

> [!TIP]
> 少なくとも AAL2 を満たすようにすることをお勧めします。 ビジネス上の理由、業界標準、またはコンプライアンス要件により必要な場合は、AAL3 を満たすようにします。

## <a name="fips-140-validation"></a>FIPS 140 検証

### <a name="verifier-requirements"></a>検証の要件

Azure AD では、認証に関連するすべての暗号処理に、Windows FIPS 140 レベル 1 の全体の検証が行われた暗号モジュールが使用されています。 したがって、これは、政府機関が必要としている FIPS 140 に準拠した検証ツールです。

## <a name="man-in-the-middle-resistance"></a>中間者攻撃への耐性 

中間者 (MitM) 攻撃への耐性を実現するために、認証要求者と Azure AD の間のすべての通信は認証済みの保護されたチャネルを介して実行されます。 これは、AAL1、AAL2、および AAL3 の MitM への耐性要件を満たしています。

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md) 