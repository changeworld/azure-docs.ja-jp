---
title: Azure Active Directory を使用して NIST AAL3 を達成する
description: この記事では、Azure Active Directory を使用して NIST 認証保証レベル 3 (AAL3) を達成するためのガイダンスを提供します。
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
ms.openlocfilehash: e8821d2c3390ddfd07c7230b20e27b46330e6229
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816160"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>Azure Active Directory を使用して NIST 認証保証レベル 3 を達成する

この記事は、米国国立標準技術研究所の認証保証レベル (NIST AAL) 3 の達成に役立ちます。 AAL3 を達成しようとする前に、以下のリソースを確認することをお勧めします。
* [NIST の概要](nist-overview.md): さまざまな AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md): 重要な用語と認証の種類です。
* [NIST 認証システムの種類](nist-authenticator-types.md): 認証システムの種類それぞれについて説明します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md): AAL の構成要素と、それらに対する Azure Active Directory (Azure AD) の認証方法の位置付けについて説明します。

## <a name="permitted-authenticator-types"></a>許可される Authenticator の種類
Microsoft では、必要な NIST Authenticator の種類に対応することができるようにする認証方法を提供しています。 このセクションでは、Microsoft の推奨事項について説明します。
 

| Azure AD の認証方法| NIST 認証システムの種類 |
| - | -|
| **推奨される方法**|    |
| FIDO2 セキュリティ キー<br>or<br> スマート カード (Active Directory フェデレーション サービス [AD FS])<br>or<br>Windows Hello for Business (ハードウェア TPM を使用)| 多要素暗号化ハードウェア |
| **他の方法**|   |
| Password<br> and<br>(ハードウェア TPM を使用して Hybrid Azure AD に参加 <br>or <br> ハードウェア TPM を使用して Azure AD に参加)| 記憶シークレット<br>and<br> 単一要素暗号化ハードウェア |
| Password <br>and<br>単一要素ワンタイム パスワード ハードウェア (OTP 製造元のもの) <br>および<br>(ソフトウェア TPM を使用して Hybrid Azure AD に参加 <br>or <br> ソフトウェア TPM を使用して Azure AD に参加 <br>or<br> 準拠しているマネージド デバイス)| 記憶シークレット <br>and<br>単一要素ワンタイム パスワード ハードウェア<br> and<br>単一要素暗号化ソフトウェア |

### <a name="our-recommendations"></a>推奨事項 

AAL3 を達成するには、多要素暗号化ハードウェア認証システムを使用することをお勧めします。 パスワードレス認証は、最大の攻撃対象領域であるパスワードを排除し、ユーザーに効率的な認証方法を提供します。 組織が完全にクラウド ベースの場合は、FIDO2 セキュリティ キーを使用することをお勧めします。

Windows Hello for Business は、必要な FIPS 140 セキュリティ レベルで検証済みではないため、連邦政府のお客様は、AAL3 としてそれを受け入れる前にリスク評価と評価を行う必要があることに注意してください。 .

詳細なガイダンスについては、「[Azure Active Directory でパスワードレス認証のデプロイを計画する](../authentication/howto-authentication-passwordless-deployment.md)」を参照してください。

Windows Hello for Business の実装の詳細については、[Windows Hello for Business デプロイ ガイド](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)に関するページを参照してください。

## <a name="fips-140-validation"></a>FIPS 140 検証

### <a name="verifier-requirements"></a>検証の要件

Azure AD は、認証に関連するすべての暗号化操作に、Windows FIPS 140 レベル 1 (総合的) の   
検証済みの暗号化モジュールを使用しています。 そのため、これは FIPS-140 に準拠した検証ツールです。

### <a name="authenticator-requirements"></a>認証システムの要件

単一要素と多要素の暗号化ハードウェア認証システムには、異なる認証システム要件があります。 

**単一要素暗号化ハードウェア** 認証システムには、以下が必要です。

* FIPS 140 レベル 1 (総合的) (またはそれ以上)。

* FIPS 140 レベル 3 の物理的なセキュリティ (またはそれ以上)。

Azure AD 参加および Hybrid Azure AD 参加のデバイスは、次の場合にこの要件を満たします。 

* [FIPS-140 承認済み動作モードで Windows](/windows/security/threat-protection/fips-140-validation) を実行している。 

* TPM が FIPS 140 レベル 1 (総合的) (またはそれ以上) で、FIPS 140 レベル 3 の物理的なセキュリティを備えたマシン。 

   * 準拠している TPM は、「[暗号化モジュール検証プログラム](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)」ページで「Trusted Platform Module」と「TPM」を検索して見つけてください。

モバイル デバイスのベンダーの FIPS 140 の準拠については、ベンダーに問い合わせてください。

**多要素暗号化ハードウェア** 認証システムには、以下が必要です。 

* FIPS 140 レベル 2 (総合的) (またはそれ以上)。

* FIPS 140 レベル 3 の物理的なセキュリティ (またはそれ以上)。

FIDO2 セキュリティ キー、スマート カード、および Windows Hello for Business は、これらの要件を満たすのに役立ちます。

* FIDO2 キー プロバイダーは、検証を完了したものも含め、FIPS 認定のさまざまな段階にあります。 [サポートされている FIDO2 キー ベンダーの一覧](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)を確認し、現在の FIPS 検証の状態をプロバイダーに確認することをお勧めします。

* スマート カードは、実績のあるテクノロジです。 複数のベンダー製品が FIPS 要件を満たしています。

   * 詳細については、「[暗号化モジュール検証プログラム](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)」ページを参照してください。

**Windows Hello for Business**

FIPS 140 では、ソフトウェア、ファームウェア、ハードウェアを含む暗号境界全体を評価の対象にすることを求めています。 Windows オペレーティング システムは、何千ものハードウェアの組み合わせと組み合わせることができるオープン コンピューティング プラットフォームです。 Microsoft は、各組み合わせの FIPS 認定を維持できません。 Windows Hello for Business を AAL3 認証システムとして使用する場合のリスク評価の一環として、コンポーネントの次の個々の認定を評価する必要があります。

* **Windows 10 および Windows Server** では、National Information Assurance Partnership (NIAP) の [US Government Approved Protection Profile for General Purpose Operating Systems バージョン 4.2.1](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442) を使用します。 NIAP は、民生 (COTS) 情報技術 (IT) 製品が国際コモン クライテリアに準拠しているかを評価する国内プログラムを監督しています。 

* **Windows 暗号化ライブラリ** は、[NIST 暗号化モジュール検証プログラム (CMVP) で FIPS レベル 1 (総合的) を達成しています](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544)。 NIST とカナダ サイバー セキュリティ センターの共同事業である CMVP は、FIPS 標準に対して暗号化モジュールを検証します。 

* FIPS 140 レベル 2 (総合的)、および FIPS 140 レベル 3 の物理的なセキュリティである **トラステッド プラットフォーム モジュール (TPM)** を選択してください。 組織は、使用しているハードウェア TPM が、達成する必要がある AAL レベルの要件を満たしているようにする責任があります。   
現在の標準を満たす TPM を確認するには、[NIST Computer Security Resource Center の「Cryptographic Module Validation Program」](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)ページにアクセスしてください。 **[Module Name]\(モジュール名\)** ボックスに「**Trusted Platform Module**」と入力します。 結果の一覧には、現在の標準を満たすハードウェア TPM が含まれています。

## <a name="reauthentication"></a>再認証 

AAL3 レベルでは、NIST はユーザーの利用状況に関係なく、12 時間ごとに再認証を求めています。 再認証は、非アクティブな状態が 15 分以上続いた後も必要になります。 両方の要素の提示が必要です。

ユーザーの利用状況に関係ない再認証の要件を満たすために、Microsoft では[ユーザーのサインイン頻度](../conditional-access/howto-conditional-access-session-lifetime.md)を 12 時間に構成することをお勧めします。 

NIST では、サブスクライバーの存在を確認するための補完的な制御の使用も許可されています。

* 15 分のセッション非活動タイムアウトを設定するには、OS レベルでデバイスをロックします。 これを行うには、Microsoft Endpoint Configuration Manager、グループ ポリシー オブジェクト (GPO)、または Intune を使用します。 また、サブスクライバーがロックを解除するには、ローカル認証が必要です。

* 利用状況に関係なくタイムアウトを実現するには、利用状況に関係なく 12 時間後にマシンをロックする、スケジュール化されたタスクを (Configuration Manager、GPO、または Intune を使用して) 実行します。

## <a name="man-in-the-middle-resistance"></a>中間者への耐性 

中間者 (MitM) 攻撃への耐性を実現するために、認証要求者と Azure AD の間のすべての通信は認証済みの保護されたチャネルを介して実行されます。 この構成は、AAL1、AAL2、および AAL3 の MitM 耐性要件を満たしています。

## <a name="verifier-impersonation-resistance"></a>検証者の偽装耐性

AAL3 を満たしているすべての Azure AD 認証方法は、認証されている特定のセッションに認証システムの出力をバインドする暗号化認証システムを使用します。 その実行には、公開キーが検証者に知られている認証要求者によって制御される、秘密キーを使用します。 この構成は、AAL3 の検証者の偽装耐性要件を満たします。

## <a name="verifier-compromise-resistance"></a>検証者の侵害耐性

AAL3 を満たしているすべての Azure AD 認証方法は、次のいずれかを実行します。
- 暗号認証システムによって保持されている秘密キーに対応する公開キーを検証者が格納することを求める認証システムを使用します。 
- FIPS-140 検証済みハッシュ アルゴリズムを使用して、予期される認証システムの出力を格納します。 

詳細については、[Azure AD データのセキュリティに関する考慮事項](https://aka.ms/AADDataWhitepaper)に関するファイルを参照してください。

## <a name="replay-resistance"></a>リプレイへの耐性

AAL3 を満たしているすべての Azure AD 認証方法では、nonce またはチャレンジのいずれかを使用します。 これらの方法は、再生された認証トランザクションを検証者が簡単に検出できるため、再生攻撃に対する耐性があります。 このようなトランザクションには、適切な nonce や適時性のデータが含まれていません。

## <a name="authentication-intent"></a>認証意図

認証意図の目的は、直接接続された物理認証システム (多要素暗号化デバイスなど) が利用者の知らないうちに使用されることをより困難にすることです。 たとえば、エンドポイント上のマルウェアなどです。

NIST では、マルウェアのリスク軽減のための補完的な制御の使用が許可されています。 Windows Defender System Guard および Windows Defender ATP が実行されている Intune 準拠のデバイスは、この軽減要件を満たしています。

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)
