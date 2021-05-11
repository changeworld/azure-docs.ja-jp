---
title: Azure Active Directory を使用して NIST AAL2 を達成する
description: Azure Active Directory を使用して NIST 認証保証レベル 2 (AAL 2) を達成するためのガイダンスです。
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
ms.openlocfilehash: aaa40141eaa0617c34a0ae8c4a7cf396d624c1b6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294089"
---
# <a name="achieving-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Azure Active Directory を使用して NIST 認証保証レベル 2 を達成する

米国国立標準技術研究所 (NIST) は、ID ソリューションをデプロイする米国政府機関の技術要件を策定します。 政府機関と連携している組織も、これらの要件を満たす必要があります。 この記事では、NIST 認証保証レベル 2 (AAL2) を達成する手順について説明します。 

AAL 2 の達成を試みる前に確認すべきリソース:
* [NIST の概要](nist-overview.md) -さまざまな AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md) -重要な用語と認証の種類。
* [NIST Authenticator の種類](nist-authenticator-types.md) - 各 Authenticator の種類について説明します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md) -AAL のコンポーネントと、それらに Microsoft Azure Active Directory の認証方法をマップする方法。

## <a name="permitted-authenticator-types"></a>許可される Authenticator の種類


| Azure AD の認証方法| NIST Authenticator の種類 | 
| - | - |
| **推奨される方法** |   | 
| iOS 用の Microsoft Authenticator アプリ (パスワードレス)<br>ソフトウェア TPM を搭載した Windows Hello for Business | 多要素の暗号化ソフトウェア |
| FIDO 2 セキュリティ キー<br>Android 用の Microsoft Authenticator アプリ (パスワードレス)<br>ハードウェア TPM を搭載した Windows Hello for Business<br>スマートカード (ADFS) | 多要素の暗号化ハードウェア |
| **他の方法** |  |
| パスワード + 電話 (SMS) | 記憶シークレット + 帯域外 |
| パスワード + Microsoft Authenticator アプリ (OTP)<br>パスワード + SF OTP | 記憶シークレット +  ‎単一要素ワンタイム パスワード |
| パスワード + ソフトウェア TPM を搭載した Azure AD 参加済み <br>パスワード + 準拠しているモバイル デバイス<br>パスワード + ソフトウェア TPM を搭載した Hybrid Azure AD 参加済み <br>パスワード + Microsoft Authenticator アプリ (通知) | 記憶シークレット + ‎単一要素暗号化ソフトウェア |
| パスワード + ハードウェア TPM を搭載した Azure AD 参加済み <br>パスワード + ハードウェア TPM を搭載した Hybrid Azure AD 参加済み | 記憶シークレット + ‎単一要素暗号化ハードウェア |


### <a name="our-recommendations"></a>推奨事項

AAL2 を達成するには、多要素暗号化ハードウェアまたはソフトウェア認証システムを使用することをお勧めします。 パスワードレス認証は、最大の攻撃対象領域であるパスワードを排除し、ユーザーに効率的な認証方法を提供します。 

パスワードレス認証方法の選択に関する詳細なガイダンスについては、「[Azure Active Directory でパスワードレス認証のデプロイを計画する](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment)」を参照してください。

Windows Hello for Business の実装の詳細については、[Windows Hello for Business デプロイ ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)に関するページを参照してください。

## <a name="fips-140-validation"></a>FIPS 140 検証

次の情報は、FIPS 140 検証を達成するためのガイドです。

### <a name="verifier-requirements"></a>検証ルールの要件

Azure AD は、認証に関連するすべての暗号化操作に、Windows FIPS 140 レベル 1 (総合的) の   
検証済みの暗号化モジュールを使用しています。 したがって、これは、政府機関が必要としている FIPS 140 に準拠した検証ツールです。

### <a name="authenticator-requirements"></a>認証システムの要件

*政府機関の暗号化認証システムは、FIPS 140 レベル 1 (総合的) で検証されている必要があります*。 これは、非政府機関の要件ではありません。 次の Azure AD 認証システムは、[FIPS 140 承認済み動作モードの Windows](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation) 上で実行されている場合の要件を満たしています

* Password

* ソフトウェアまたはハードウェア TPM を搭載した Azure AD 参加済み

* ソフトウェアまたはハードウェア TPM を搭載した Hybrid Azure AD 参加済み

* ソフトウェアまたはハードウェア TPM を搭載した Windows Hello for Business

* スマートカード (ADFS) 

FIDO2 セキュリティ キー、およびすべてのモード (通知、OTP、およびパスワードレス) の Microsoft Authenticator アプリは、この文書の作成時点では、FIPS 140 レベル 1 (総合的) の検証について政府機関の要件を満たしていません。

* Microsoft Authenticator アプリは FIPS 140 承認済みの暗号化を使用しています。ただし、FIPS 140 レベル 1 (総合的) 検証済みではありません。 

* FIDO2 キーはごく最近のイノベーションであり、そのため、まだ FIPS 認定を受けている最中です。

## <a name="reauthentication"></a>再認証 

AAL2 では、NIST は、ユーザーの利用状況に関係なく、12 時間ごと、および非アクティブな期間が 30 分以上続いた後の、再認証を要求しています。 セッション シークレットはユーザーが所有しているものなので、ユーザーが知っているものユーザー自身の提示が必要です。

ユーザーの利用状況に関係ない再認証の要件を満たすために、Microsoft では[ユーザーのサインイン頻度](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)を 12 時間に構成することをお勧めします。 

NIST では、サブスクライバーの存在を確認するための補完的な制御の使用も許可されています。

* Microsoft System Center Configuration Manager (SCCM)、グループ ポリシー オブジェクト (GPO)、または Intune を利用して OS レベルでデバイスをロックすることによって、セッションの無通信のタイムアウトを 30 分に設定できます。 また、サブスクライバーがロックを解除するには、ローカル認証が必要です。

* 利用状況に関係ないタイムアウトは、利用状況に関係なく 12 時間後にマシンをロックする、スケジュールされたタスク (SCCM、GPO、または Intune を利用) を実行することで実現できます。

## <a name="man-in-the-middle-mitm-resistance"></a>中間者攻撃 (MitM) への耐性 

認証要求者と Azure AD の間のすべての通信は認証済みの保護されたチャネルを介して実行されるため、MitM 攻撃への耐性を有しています。 これは、AAL1、AAL2、および AAL3 の MitM への耐性要件を満たしています。

## <a name="replay-resistance"></a>リプレイへの耐性

AAL2 のすべての Azure AD 認証方法では、nonce またはチャレンジのいずれかが使用され、リプレイ攻撃に対する耐性があります。これは、適切な nonce や適時性のデータが含まれていないため、検証者がリプレイされた認証トランザクションを簡単に検出できるためです。

## <a name="next-steps"></a>次の手順 

[NIST の概要](nist-overview.md)

[AAL の詳細](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST Authenticator の種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md)  