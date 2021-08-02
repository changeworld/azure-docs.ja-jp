---
title: NIST 認証の基本と Azure Active Directory
description: この記事では、重要な用語を定義し、NIST のためのトラステッド プラットフォーム モジュールと認証要素について説明します。
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
ms.openlocfilehash: eacdb19dcd5121f1d7fecdf6a3cefbafd609c9a9
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889834"
---
# <a name="nist-authentication-basics"></a>NIST の認証の基本 

米国標準技術局 (NIST) のガイドラインを理解するには、用語を知っておく必要があります。 また、トラステッド プラットフォーム モジュール (TPM) テクノロジと認証要素についても理解しておく必要があります。 この記事では、その情報を提供します。 

## <a name="terminology"></a>用語

これらの NIST の記事では、次の用語が使用されています。

|期間| 定義。 "*斜体*" の用語はこの表で定義されています|
| - | - |
| アサーション| "*サブスクライバー*" に関する情報が含まれている、"*検証者*" から "*証明書利用者*" への声明。 アサーションには検証済みの属性が含まれる場合があります。 |
|認証| "*サブジェクト*" の ID を検証するプロセス。 |
| 認証要素| 自分が知っているもの、自分が持っているもの、または自分自身。 すべての "*認証子*" には、1 つまたは複数の認証要素があります。 |
| 認証システム| "*認証要求者の*" ID を認証するために使用される、"*認証要求者*" が所有し、制御するもの。 |
| 認証要求者| 1 つ以上の "*認証*" プロトコルを使用して ID を検証する対象の "*利用者*"。 |
|資格情報| "*サブスクライバー*" が所有および制御する少なくとも 1 つの "*認証システム*" に ID を正式にバインドするオブジェクトまたはデータ構造。 |
| 資格情報サービス プロバイダー (CSP)| "*サブスクライバー認証システム*" を発行または登録し、"*サブスクライバー*" に電子 "*資格情報*" を発行する、信頼されたエンティティ。 |
|証明書利用者| 通常、システムへのアクセスを許可するために、"*検証者のアサーション*"、または "*認証要求者の認証システム*" と "*資格情報*" に依存するエンティティ。 |
|  サブジェクト| 個人、組織、デバイス、ハードウェア、ネットワーク、ソフトウェア、またはサービス。 |
| サブスクライバー (Subscriber)| "*CSP*" から "*資格情報*" または "*認証システム*" を受け取った関係者。 |
|トラステッド プラットフォーム モジュール  | TPM は、キーの生成などの暗号化操作を行う改ざん防止モジュールです。 |
|  検証方法| 認証要求者が "*認証システム*" を所有および制御していることを検証することにより、"*認証要求者の*" 身元を確認するエンティティ。 |


## <a name="about-trusted-platform-module-technology"></a>トラステッド プラットフォーム モジュール テクノロジについて

トラステッド プラットフォーム モジュール テクノロジは、ハードウェア ベースのセキュリティ関連機能を提供するために設計されています。 TPM チップ (ハードウェア TPM) は、暗号化キーの生成、保存、使用制限などの操作に役立つ、セキュリティで保護された暗号プロセッサです。 

Microsoft では、TPM が Windows とどのように連携するかについての重要な情報を提供しています。 詳細については、「[トラステッド プラットフォーム モジュール](/windows/security/information-protection/tpm/trusted-platform-module-top-node)」を参照してください。 

ソフトウェア TPM は、ハードウェア TPM の機能を模倣するエミュレーターです。 

 ## <a name="authentication-factors-and-their-strengths"></a>認証要素とその強度

認証要素は、3 つのカテゴリに分類できます。

![ユーザーが知っていること、ユーザーが持っているもの、ユーザー自身にグループ分けされた、認証要素の例を提供する図。](media/nist-authentication-basics/nist-authentication-basics-0.png)

認証要素の強度は、それがサブスクライバーだけが知っていること、所有していること、または自身であると、どの程度確信できるかによって決まります。

NIST からは、認証要素の相対的な強度に関する限定されたガイダンスが提供されています。 このセクションの残りの部分では、Microsoft によるそれらの強度の評価方法について説明します。 

**ユーザーが知っていること**。 最も一般的な "*ユーザーが知っていること*" であるパスワードは、最大の攻撃対象領域を表します。 以下の軽減策により、サブスクライバーとのアフィニティの信頼性が向上します。 ブルートフォース攻撃、盗聴、ソーシャル エンジニアリングなどのパスワード攻撃を防ぐのに効果的です。

* [パスワードの複雑さの要件](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [禁止パスワード](../authentication/tutorial-configure-custom-password-protection.md)

* [漏洩した資格情報の特定](../identity-protection/overview-identity-protection.md)

* [セキュリティで保護されたハッシュされたストレージ](https://aka.ms/AADDataWhitepaper)

* [アカウントのロックアウト](../authentication/howto-password-smart-lockout.md)

**ユーザーが持っているもの**。 "*ユーザーが持っているもの*" の強度は、サブスクライバーがそれを所有し続ける可能性と、攻撃者がそれにアクセスできることの難しさに基づいています。 たとえば、内部の脅威から保護しようとするときは、個人のモバイル デバイスやハードウェア キーは、アフィニティが高くなります。 したがって、オフィスのデスクトップ コンピューターより安全性が高くなります。

**ユーザー自身**。 攻撃者が簡単に "*ユーザー自身*" のコピーを取得したり、生体認証を偽装したりできるのは、問題です。 NIST は、生体認証のフレームワークを起草しています。 現時点で NIST は、個別の認証方法として生体認証を受け入れない予定です。 これは多要素認証の要素である必要があります。 この予防措置が設けられているのは、生体認証が本質的には確率論的であるためです。 つまり、アフィニティの可能性を判断するアルゴリズムが使用されます。 生体認証は、必ずしも、パスワードのように完全に一致するとは限りません。 詳細については、[認証システムの機能の強度 - 生体認証](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B) に関するドキュメントを参照してください。 

SOFA-B では、次のような生体認証の強度を定量化するフレームワークの提示が試みられています。
- 誤合致率。
- 偽不一致率。
- 指示型攻撃検知誤り率。
- 攻撃を実行するために必要な労力。 

## <a name="single-factor-authentication"></a>‎単一要素認証

単一要素認証は、"*ユーザーが知っていること*" や "*ユーザー自身*" を検証する単一要素認証システムを使用することによって実装できます。 "*ユーザー自身*" 要素は認証要素として受け入れられますが、それ自体では認証システムとして受け入れられません。 

![単一要素認証のしくみを示す図。](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multifactor-authentication"></a>多要素認証

多要素認証は、多要素認証システムまたは 2 つの単一要素認証システムのいずれかを使用することによって、実装できます。 多要素認証システムでは、1 つの認証トランザクションを完了するために、2 つの認証要素が必要です。

### <a name="multifactor-authentication-by-using-two-single-factor-authenticators"></a>2 つの単一要素認証システムを使用した多要素認証

多要素認証には、2 つの異なる認証要素が必要です。 これらの認証システムは独立したものであってもかまいません。 次に例を示します。 

* 記憶シークレット (パスワード) と帯域外 (SMS)

* 記憶シークレット (パスワード) とワンタイム パスワード (ハードウェアまたはソフトウェア)

これらの方法では、Azure Active Directory (Azure AD) で 2 つの独立した認証トランザクションが実行されます。

![2 つの別々の認証システムを使用する多要素認証を説明する図。](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multifactor-authentication-by-using-a-single-multifactor-authenticator&quot;></a>1 つの多要素認証システムを使用した多要素認証

多要素認証では、2 つ目の認証要素のロックを解除するために、1 つの認証要素 (&quot;*ユーザーが知っていること*&quot; または &quot;*ユーザー自身*") が必要です。 通常、そのユーザー エクスペリエンスは、複数の独立した認証システムのものより簡単です。

![1 つの多要素認証システムを使用した多要素認証を示す図。](media/nist-authentication-basics/nist-authentication-basics-3a.png)

1 つの例として、パスワードレス モードで使用される Microsoft Authenticator アプリがあります。 この方法では、ユーザーはセキュリティで保護されたリソース (証明書利用者) へのアクセスを試みて、自分の認証アプリで通知を受信します。 ユーザーは、生体認証 ("*ユーザー自身*") または PIN ("*ユーザーが知っていること*") のいずれかを提供することによって、通知に応答します。 この要因により、後で検証者によって検証される電話 ("*ユーザーが持っているもの*") で暗号化キーのロックが解除されます。

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md)