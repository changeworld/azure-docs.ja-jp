---
title: NIST 認証の基本と Azure Active Directory
description: NIST の用語と認証要素の説明。
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
ms.openlocfilehash: 028ce5fc6ae3cef586803e1b04948006b58fd17e
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294094"
---
# <a name="nist-authentication-basics"></a>NIST 認証の基本 

NIST ガイドラインを理解するには、用語、およびトラステッド プラットフォーム モジュール (TPM) と認証要素の概念をしっかりと理解している必要があります。

## <a name="terminology"></a>用語

これらの NIST 関連の記事では、次の用語が使用されています。

|期間| 定義 - "*斜体*" の用語はこの表で定義されています|
| - | - |
| アサーション| "*サブスクライバー*" に関する情報が含まれている、"*検証者*" から "*証明書利用者*" への声明。 検証済みの属性が含まれている場合があります。 |
|認証| "*サブジェクト*" の ID を検証するプロセス。 |
| 認証要素| ユーザーが知っていること、ユーザーが所有しているもの、およびユーザー自身。どのような "*認証システム*" にも 1 つ以上の認証要素があります。 |
| 認証システム| "*認証要求者の*" ID を認証するために使用される、"*認証要求者*" が所有し、制御するもの。 |
| 認証要求者| 1 つ以上の認証プロトコルを使用して ID を検証する対象の "*利用者*"。 |
|資格情報| "*サブスクライバー*" が所有および制御する少なくとも 1 つの "*認証システム*" に ID を正式にバインドするオブジェクトまたはデータ構造。 |
| 資格情報サービス プロバイダー (CSP)| "*サブスクライバー認証システム*" を発行または登録し、"*サブスクライバー*" に電子 "*資格情報*" を発行する、信頼されたエンティティ。 |
|証明書利用者| 通常、システムへのアクセスを許可するために、"*検証者のアサーション*"、または "*認証要求者の認証システム*" と "*資格情報*" に依存するエンティティ。 |
|  サブジェクト| 個人、組織、デバイス、ハードウェア、ネットワーク、ソフトウェア、またはサービス。 |
| サブスクライバー (Subscriber)| "*CSP*" から "*資格情報*" または "*認証システム*" を受け取った関係者。 |
|トラステッド プラットフォーム モジュール (TPM)  | TPM は、キーの生成などの暗号化操作を実行する改ざん防止モジュールです。 |
|  検証方法| 認証要求者が "*認証システム*" を所有および制御していることを検証することにより、"*認証要求者の*" 身元を確認するエンティティ。 |


## <a name="about-trusted-platform-modules"></a>トラステッド プラットフォーム モジュールについて 

トラステッド プラットフォーム モジュール (TPM) テクノロジは、ハードウェア ベースのセキュリティ関連機能を提供するために設計されています。 TPM チップ (ハードウェア TPM) は、暗号化キーの生成、保存、使用制限などの操作に役立つ、セキュリティで保護された暗号プロセッサです。 

Microsoft では、TPM が Microsoft Windows とどのように連携するかについての重要な情報を提供しています。 詳細については、[トラステッド プラットフォーム モジュール](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node)に関する記事を参照してください。 

ソフトウェア TPM は、この機能を模倣するエミュレーターです。 

 ## <a name="authentication-factors-and-their-strengths"></a>認証要素とその強度

認証要素は、3 つのカテゴリに分類できます。 次の表は、各グループの下にある要素の種類の例を示しています。

![ユーザーが知っていること、ユーザーが所有しているもの、およびユーザー自身の視覚的な表現。](media/nist-authentication-basics/nist-authentication-basics-0.png)

認証要素の強度は、それがサブスクライバーだけが知っている、所有している、または自身であると確信できるかどうかによって決まります。

NIST には、認証要素の相対的な強度に関する限定されたガイダンスがあります。 Microsoft では、強度を次のように評価しています。 

**ユーザーが知っているもの**: ユーザーが最もよく知っているものであるパスワードは、最大の攻撃対象領域を表しています。 次の軽減策は、サブスクライバーとのアフィニティの信頼性を向上させ、ブルートフォース攻撃、盗聴、ソーシャル エンジニアリングなどのパスワード攻撃を防ぐうえで効果的です。

* [パスワードの複雑さの要件](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [禁止パスワード](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-configure-custom-password-protection)

* [漏洩した資格情報の特定](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [セキュリティで保護されたハッシュされたストレージ](https://aka.ms/AADDataWhitepaper)

* [アカウントのロックアウト](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)

**ユーザーが所有しているもの**: ユーザーが所有しているものの強度は、サブスクライバーがそれを所有し続ける可能性と、攻撃者がそれにアクセスできることの難しさに基づいています。 たとえば、個人のモバイル デバイスやハードウェア キーは、内部の脅威から保護しようとするときに、オフィスのデスクトップ コンピューターよりもアフィニティが高く、したがって安全性が高くなります。

**ユーザー自身**: 攻撃者がいとも簡単にユーザー自身のコピーを取得したり、生体認証を偽装したりできるのは、問題です。 NIST は、生体認証のフレームワークを起草しています。 現時点で NIST は、個別の認証方法として生体認証を受け入れない予定です。 これは多要素認証の要素である必要があります。 それは、生体認証が本質的には確率論的であるためです。 つまり、同じ人物である可能性を判断するアルゴリズムが使用されます。 パスワードのように、必ずしも完全に一致するとは限りません。 [認証システムの機能の強度 - 生体認証](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B) に関するドキュメントを参照してください。 SOFA-B は、誤合致率、偽造、失敗率、指示型攻撃検知の誤り率、および攻撃を開始するために必要な労力の観点から、生体認証の強度を定量化するためのフレームワークを提示しようとしています。 

## <a name="single-factor-authentication"></a>‎単一要素認証

単一要素認証は、ユーザーが知っているものやユーザー自身を構成する単一要素認証システムを使用することによって実現できます。 "ユーザー自身" である認証要素は認証要素として受け入れられますが、それ自体では認証システムとして受け入れられません。 

![単一要素認証の概念図。](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multi-factor-authentication"></a>多要素認証

多要素認証は、多要素認証システムによって、または 2 つの単一要素認証システムの組み合わせによって実現できます。 多要素認証システムでは、1 つの認証トランザクションを実行するために 2 つの認証要素が必要です。

### <a name="multi-factor-authentication-using-two-single-factor-authenticators"></a>2 つの単一要素認証システムを使用した多要素認証

多要素認証には、2 つの異なる認証要素が必要です。 次のような 2 つの独立した認証システムにすることができます 

* 記憶シークレット [パスワード] と帯域外 [SMS]

* 記憶シークレット [パスワード] とワンタイム パスワード [ハードウェアまたはソフトウェア]

これらの方法では、Azure AD で 2 つの独立した認証トランザクションが実行されます。

![2 つの別々の認証システムを使用する多要素認証の概念図。](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multi-factor-authentication-using-a-single-multi-factor-authenticator"></a>1 つの多要素認証システムを使用した多要素認証

多要素認証では、2 つ目の認証要素のロックを解除するために、1 つの認証要素 (ユーザーが知っているものかユーザー自身) が必要です。 これは、通常、複数の独立した認証システムよりも単純なユーザー エクスペリエンスです。

![1 つの多要素認証システムの多要素認証の概念図。](media/nist-authentication-basics/nist-authentication-basics-3a.png)

1 つの例として、パスワードレス モードで使用される Microsoft Authenticator アプリがあります。 この方法では、ユーザーはセキュリティで保護されたリソース (証明書利用者) へのアクセスを試みて、自分の認証アプリで通知を受信します。 ユーザーは、生体認証 (ユーザー自身) または PIN (ユーザーが知っているもの) のいずれかを提供することによって、通知に応答します。これにより、後で検証者によって検証される電話 (ユーザーが所有しているもの) の暗号化キーのロックが解除されます。

## <a name="next-steps"></a>次の手順 

[NIST の概要](nist-overview.md)

[AAL の詳細](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST Authenticator の種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md) 