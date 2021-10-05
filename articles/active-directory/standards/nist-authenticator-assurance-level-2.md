---
title: Azure Active Directory を使用して NIST AAL2 を達成する
description: Azure Active Directory を使用して NIST Authenticator Assurance Level 2 (AAL2) を達成するためのガイダンスです。
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
ms.openlocfilehash: 30287957ca9d44c27f0d46efb6ae358db7d6ab44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662269"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Azure Active Directory を使用して NIST Authenticator Assurance Level 2 を達成する

米国国立標準技術研究所 (NIST) は、ID ソリューションを実装する米国政府機関の技術要件を策定します。 これらの要件を満たすことは、政府機関と協力している組織にも求められます。 

Authenticator Assurance Level 2 (AAL2) の達成を試みる前に、次のリソースを参照することをお勧めします。
* [NIST の概要](nist-overview.md): 異なる AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md): 重要な用語と認証の種類です。
* [NIST 認証子の種類](nist-authenticator-types.md): 各認証子の種類について説明します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md): AAL のコンポーネントと、それらに Azure Active Directory (Azure AD) の認証方法をマップする方法について説明します。

## <a name="permitted-authenticator-types"></a>許可される Authenticator の種類

次の表に、AAL2 で許可される認証子の種類の詳細を示します。

| Azure AD の認証方法| NIST 認証子の種類 | 
| - | - |
| **推奨される方法** |   | 
| iOS 用の Microsoft Authenticator アプリ (パスワードレス)<br>Windows Hello for Business (ソフトウェア トラステッド プラットフォーム モジュール (TPM) を使用) | 多要素の暗号化ソフトウェア |
| FIDO 2 セキュリティ キー<br>Android 用の Microsoft Authenticator アプリ (パスワードレス)<br>Windows Hello for Business (ハードウェア TPM を使用)<br>スマート カード (Active Directory フェデレーション サービス) | 多要素の暗号化ハードウェア |
| **他の方法** |  |
| パスワード + 電話 (SMS) | 記憶シークレット + 帯域外 |
| パスワード + Microsoft Authenticator アプリ (OTP)<br>パスワード + SF OTP | 記憶シークレット + 単一要素ワンタイム パスワード |
| パスワード + ソフトウェア TPM を搭載した Azure AD 参加済み <br>パスワード + 準拠しているモバイル デバイス<br>パスワード + ソフトウェア TPM を搭載した Hybrid Azure AD 参加済み <br>パスワード + Microsoft Authenticator アプリ (通知) | 記憶シークレット + 単一要素暗号化ソフトウェア |
| パスワード + ハードウェア TPM を搭載した Azure AD 参加済み <br>パスワード + ハードウェア TPM を搭載した Hybrid Azure AD 参加済み | 記憶シークレット + 単一要素暗号化ハードウェア |

> [!NOTE]
> 条件付きアクセス ポリシーでは、準拠または Hybrid Azure AD 参加済みとしてデバイスをマークする必要がある場合、Authenticator が、検証者の偽装耐性の機能を果たします。

### <a name="our-recommendations"></a>推奨事項

AAL2 を達成するには、多要素暗号化ハードウェアまたはソフトウェア認証子を使用します。 パスワードレス認証を使用すると、最大の攻撃対象領域であるパスワードを排除して、ユーザーに効率的な認証方法を提供することができます。 

パスワードレス認証方法の選択に関する詳細なガイダンスについては、「[Azure Active Directory でパスワードレス認証のデプロイを計画する](../authentication/howto-authentication-passwordless-deployment.md)」を参照してください。

Windows Hello for Business の実装の詳細については、[Windows Hello for Business デプロイ ガイド](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)に関するページを参照してください。

## <a name="fips-140-validation"></a>FIPS 140 検証

以降のセクションでは、FIPS 140 検証の実現について説明します。

### <a name="verifier-requirements"></a>検証の要件

Azure AD では、認証に関連するすべての暗号処理に、Windows FIPS 140 レベル 1 の全体の検証が行われた暗号モジュールが使用されています。 したがって、これは、政府機関が必要としている FIPS 140 に準拠した検証ツールです。

### <a name="authenticator-requirements"></a>認証システムの要件

政府機関の暗号化認証子は、FIPS 140 Level 1 全体で検証する必要があります。 これは、非政府機関には必要ありません。 次の Azure AD 認証子は、[FIPS 140 承認済み動作モードの Windows](/windows/security/threat-protection/fips-140-validation) 上で実行されている場合の要件を満たしています。

* Password

* ソフトウェアまたはハードウェア TPM を搭載した Azure AD 参加済み

* ソフトウェアまたはハードウェア TPM を搭載した Hybrid Azure AD 参加済み

* ソフトウェアまたはハードウェア TPM を搭載した Windows Hello for Business

* スマート カード (Active Directory フェデレーション サービス) 

Microsoft Authenticator アプリでは、すべてのモード (通知、OTP、パスワードレス) で FIPS 140 承認済みの暗号化が使用されますが、FIPS 140 レベル 1 では検証されていません。

FIDO2 セキュリティ キー プロバイダーは、検証を完了したものも含め、FIPS 認定のさまざまな段階にあります。 [サポートされている FIDO2 キー ベンダーの一覧](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)を確認し、現在の FIPS 検証の状態をプロバイダーに確認することをお勧めします。


## <a name="reauthentication"></a>再認証 

AAL2 レベルでは、NIST はユーザーの利用状況に関係なく、12 時間ごとに再認証を必要とします。 再認証は、非アクティブな状態が 30 分以上続いた後も必要になります。 セッション シークレットはユーザーが所有しているものなので、ユーザーが知っているものまたはユーザー自身の提示が必要です。

ユーザーの利用状況に関係ない再認証の要件を満たすために、Microsoft では[ユーザーのサインイン頻度](../conditional-access/howto-conditional-access-session-lifetime.md)を 12 時間に構成することをお勧めします。 

NIST では、サブスクライバーの存在を確認するための補完的な制御の使用も許可されています。

* Microsoft System Center Configuration Manager、グループ ポリシー オブジェクト (GPO)、または Intune を使用して、オペレーティング システム レベルでデバイスをロックすることで、セッションの無通信のタイムアウトを 30 分に設定できます。 また、サブスクライバーがロックを解除するには、ローカル認証が必要です。

* 利用状況に関係なくタイムアウトを実現するには、利用状況に関係なく 12 時間後にマシンをロックする、スケジュール化されたタスクを (Configuration Manager、GPO、または Intune を使用して) 実行します。

## <a name="man-in-the-middle-resistance"></a>中間者攻撃への耐性 

中間者 (MitM) 攻撃への耐性を実現するために、認証要求者と Azure AD の間のすべての通信は認証済みの保護されたチャネルを介して実行されます。 これは、AAL1、AAL2、および AAL3 の MitM への耐性要件を満たしています。

## <a name="replay-resistance"></a>リプレイへの耐性

AAL2 のすべての Azure AD 認証方法では、nonce またはチャレンジのいずれかが使用されます。 これらの方法は、再生された認証トランザクションを検証者が簡単に検出できるため、再生攻撃に対する耐性があります。 このようなトランザクションには、適切な nonce や適時性のデータは含まれません。

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md)
