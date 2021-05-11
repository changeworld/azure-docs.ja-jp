---
title: Azure Active Directory を使用して NIST AAL3 を達成する
description: Azure Active Directory を使用して NIST 認証保証レベル 3 (AAL 3) を達成するためのガイダンスです。
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
ms.openlocfilehash: 1d7ebba72877d5c4e5df111ab574e28156003bf9
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294088"
---
# <a name="achieving-nist-authenticator-assurance-level-3-with-the-azure-active-directory"></a>Azure Active Directory を使用して NIST 認証保証レベル 3 を達成する

この記事では、米国国立標準技術研究所の認証保証レベル (NIST AAL) 3 を達成する方法について説明します。 AAL 3 の達成を試みる前に確認すべきリソース:
* [NIST の概要](nist-overview.md) -さまざまな AAL レベルについて説明します。
* [認証の基本](nist-authentication-basics.md) -重要な用語と認証の種類。
* [NIST Authenticator の種類](nist-authenticator-types.md) - 各 Authenticator の種類について説明します。
* [NIST AAL](nist-about-authenticator-assurance-levels.md) -AAL のコンポーネントと、それらに Microsoft Azure Active Directory の認証方法をマップする方法。

## <a name="permitted-authenticator-types"></a>許可される Authenticator の種類
Microsoft では、必要な NIST Authenticator の種類に対応することができるようにする認証方法を提供しています。 推奨事項を参照してください。
 

| Azure AD の認証方法| NIST Authenticator の種類 |
| - | -|
| **推奨される方法**|    |
| FIDO2 セキュリティ キー、"**または**"<br> スマートカード (AD FS)、"**または**"<br>ハードウェア TPM を搭載した Windows Hello for Business| 多要素暗号化ハードウェア |
| **他の方法**|   |
| パスワード、"**および**"<br>(ハードウェア TPM を搭載した Hybrid Azure AD 参加済み、"**または**" <br> ハードウェア TPM を搭載した Azure AD 参加済み)| 記憶シークレット **+** 単一要素暗号化ハードウェア |
| パスワード、"**および**"<br>単一要素ワンタイム パスワード ハードウェア (OTP 製造元のもの)、"**または**"<br>ソフトウェア TPM を搭載した Hybrid Azure AD 参加済み、"**または**" <br> ソフトウェア TPM を搭載した Azure AD 参加済み、"**または**"<br> 準拠しているマネージド デバイス| 記憶シークレット、"**および**"<br>単一要素ワンタイム パスワード ハードウェア、"**および**"<br>単一要素暗号化ソフトウェア |

### <a name="our-recommendations"></a>推奨事項 

AAL3 を達成するには、多要素暗号化ハードウェア認証システムを使用することをお勧めします。 パスワードレス認証は、最大の攻撃対象領域であるパスワードを排除し、ユーザーに効率的な認証方法を提供します。 組織が完全にクラウドベースの場合は、FIDO2 セキュリティ キーを使用することをお勧めします。

FIDO2 キーと Windows Hello for Business は、必要な FIPS 140 セキュリティ レベルで検証済みではないため、連邦政府のお客様は、これらの認証システムを AAL3 として受け入れる前にリスク評価と評価を行う必要があることに注意してください。

詳細なガイダンスについては、「[Azure Active Directory でパスワードレス認証のデプロイを計画する](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment)」を参照してください。

Windows Hello for Business の実装の詳細については、[Windows Hello for Business デプロイ ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)に関するページを参照してください。

## <a name="fips-140-validation"></a>FIPS 140 検証

### <a name="verifier-requirements"></a>検証ルールの要件

Azure AD は、認証に関連するすべての暗号化操作に、Windows FIPS 140 レベル 1 (総合的) の   
検証済みの暗号化モジュールを使用しています。 したがって、これは FIPS 140 に準拠した検証ツールです。

### <a name="authenticator-requirements"></a>認証システムの要件

単一要素と多要素の暗号化ハードウェア認証システムには、異なる認証システム要件があります。 

単一要素暗号化ハードウェア認証システムには、以下が必要です 

* FIPS 140 レベル 1 (総合的) (またはそれ以上)

* FIPS 140 レベル 3 の物理的なセキュリティ (またはそれ以上)

Azure AD 参加済みおよび Hybrid Azure AD 参加済みデバイスは、次の場合にこの要件を満たします 

* [FIPS 140 承認済み動作モードで Windows](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation) を実行している 

* TPM が FIPS 140 レベル 1 (総合的) (またはそれ以上) で、FIPS 140 レベル 3 の物理的なセキュリティを備えたマシン。 

   * 準拠している TPM は、「[暗号化モジュール検証プログラム](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)」で「トラステッド プラットフォーム モジュール」と「TPM」を検索して見つけてください。

FIPS 140 の準拠については、モバイル デバイスのベンダーに問い合わせてください。

"**多要素暗号化ハードウェア**" 認証システムには、以下が必要です 

* FIPS 140 レベル 2 (総合的) (またはそれ以上)

* FIPS 140 レベル 3 の物理的なセキュリティ (またはそれ以上)

FIDO2 セキュリティ キー、スマートカード、および Windows Hello for Business は、これらの要件を満たすのに役立ちます。

* FIDO2 キーはごく最近のイノベーションであり、そのため、まだ FIPS 認定を受けている最中です。

* スマートカードは、複数のベンダー製品が FIPS 要件を満たしている、実績のあるテクノロジです。

   * 詳細については、[暗号化モジュール検証プログラム](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)に関するページを参照してください。

**Windows Hello for Business**

FIPS 140 では、ソフトウェア、ファームウェア、ハードウェアを含む暗号境界全体を評価の対象にすることを要求しています。 Windows オペレーティング システムは、何千ものハードウェアの組み合わせと組み合わせることができるオープン コンピューティング プラットフォームです。 そのため、Microsoft では、各組み合わせの FIPS 認定を維持することはできません。 WHfB を AAL3 認証システムとして使用するためのリスク評価の一環として、コンポーネントの次の個々の認証を評価する必要があります。

* **Microsoft Windows 10、および Microsoft Windows Server** では、国家情報保証パートナーシップ (NIAP) の[汎用オペレーティング システム バージョン 4.2.1 の米国政府認定保護プロファイル](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442) を使用しています。 NIAP は、国際コモンクライテリアに準拠するために商用オフザシェルフ (COTS) 情報テクノロジ (IT) 製品を評価するための国内プログラムを監督しています。 

* **Microsoft Windows 暗号化ライブラリ** は、[NIST 暗号化モジュール検証プログラム (CMVP) で FIPS レベル 1 (総合的) を達成しています](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544)。 NIST とカナダ サイバー セキュリティ センターの共同作業である CMVP は、FIPS 標準に対して 暗号化モジュールを検証します。 

* FIPS 140 レベル 2 (総合的)、および FIPS 140 レベル 3 の物理的なセキュリティである **トラステッド プラットフォーム モジュール (TPM)** を選択してください。 **組織の場合は、使用しているハードウェア TPM が達成したい AAL レベルのニーズを満たしていることを保証する責任があります**。   
現在の標準を満たす TPM を確認するには、[NIST コンピューター セキュリティ リソース センターの暗号化モジュール検証プログラム](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)に関するページを参照してください。 [モジュール名] フィールドに、「トラステッド プラットフォーム モジュール」と入力してください。 結果の一覧には、現在の標準を満たすハードウェア TPM が含まれています。

## <a name="reauthentication"></a>再認証 

AAL3 では、NIST は、ユーザーの利用状況に関係なく、12 時間ごと、および非アクティブな期間が 15 分以上続いた後の、再認証を要求しています。 両方の要素の提示が必要です。

ユーザーの利用状況に関係なく再認証の要件を満たすために、Microsoft では[ユーザーのサインイン頻度](https://aka.ms/NIST/38)を 12 時間に構成することをお勧めします。 

NIST では、サブスクライバーの存在を確認するための補完的な制御の使用も許可されています。

* Microsoft System Center Configuration Manager (SCCM)、グループ ポリシー オブジェクト (GPO)、または Intune を利用して OS レベルでデバイスをロックすることによって、セッションの無通信のタイムアウトを 15 分に設定できます。 また、サブスクライバーがロックを解除するには、ローカル認証が必要です。

* 利用状況に関係ないタイムアウトは、利用状況に関係なく 12 時間後にマシンをロックする、スケジュールされたタスク (SCCM、GPO、または Intune を利用) を実行することで実現できます。

## <a name="man-in-the-middle-mitm-resistance"></a>中間者攻撃 (MitM) への耐性 

認証要求者と Azure AD の間のすべての通信は認証済みの保護されたチャネルを介して実行されるため、MitM 攻撃への耐性を有しています。 これは、AAL1、AAL2、および AAL3 の MitM への耐性要件を満たしています。

## <a name="verifier-impersonation-resistance"></a>検証者の偽装耐性

AAL3 を満たすすべての Azure AD 認証方法は、認証される特定のセッションに認証システムの出力をバインドする暗号認証システムを活用します。 これを行うには、公開キーが検証者に知られている認証要求者によって制御される、秘密キーを使用します。 これは、AAL3 の検証者の偽装耐性を満たします。

## <a name="verifier-compromise-resistance"></a>検証者の侵害耐性

AAL3 を満たすすべての Azure AD 認証方法では、検証者が認証システムによって保持されている秘密キーに対応する公開キーを格納することを要求する暗号認証システムを使用するか、FIPS 140 検証済みハッシュ アルゴリズムを使用して予期される認証システムの出力を格納します。 詳細については、[Azure AD データのセキュリティに関する考慮事項](https://aka.ms/AADDataWhitepaper)に関するページを参照してください。

## <a name="replay-resistance"></a>リプレイへの耐性

AAL3 のすべての Azure AD 認証方法では、nonce またはチャレンジのいずれかが使用され、リプレイ攻撃に対する耐性があります。これは、適切な nonce や適時性のデータが含まれていないため、検証者がリプレイされた認証トランザクションを簡単に検出できるためです。

## <a name="authentication-intent"></a>認証意図

認証意図の目的は、エンドポイントのマルウェアなど、利用者の知らないうちに直接接続された物理認証システム (多要素暗号化デバイスなど) が使用されることをより困難にすることです。

NIST では、マルウェアのリスク軽減のための補完的な制御の使用が許可されています。 Windows Defender System Guard および Windows Defender ATP を実行している Intune 準拠のデバイスは、この軽減要件を満たしています。

## <a name="next-steps"></a>次の手順 

[NIST の概要](nist-overview.md)

[AAL の詳細](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST Authenticator の種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md) 
