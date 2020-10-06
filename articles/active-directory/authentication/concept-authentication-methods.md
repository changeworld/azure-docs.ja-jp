---
title: 認証方法と特徴 - Azure Active Directory
description: サインイン イベントの改善とセキュリティ保護のために Azure Active Directory で使用できるさまざまな認証方法と特徴について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: d4b44deda1bd17e65c3e2c2a9c46dddccd411996
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602041"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory で使用できる認証方法と検証方法

Azure Active Directory (Azure AD) のアカウントのサインイン エクスペリエンスの一部として、ユーザーが自分自身を認証する方法はいくつかあります。 従来、ユーザーが資格情報を提供していた最も一般的な方法は、ユーザー名とパスワードです。 Azure AD の先進的な認証とセキュリティ機能を使用して、基本パスワードをよりセキュアな認証方法で補完したり置き換えたりする必要があります。

![Azure AD における認証方法の強度と推奨の表](media/concept-authentication-methods/authentication-methods.png)

Windows Hello、FIDO2 セキュリティ キー、Microsoft Authenticator アプリなどのパスワードレス認証方法は、最も安全なサインイン イベントを提供します。

Azure Multi-Factor Authentication は、ユーザーがサインイン時にパスワードのみを使用している場合に比べて、追加のセキュリティを提供します。 ユーザーに対して、プッシュ通知に応答する、ソフトウェアまたはハードウェア トークンのコードを入力する、または SMS や電話に応答するなど、追加の形式での認証を要求することができます。

ユーザーのオンボード エクスペリエンスを単純化し、MFA と SSPR の両方に登録するには、[統合されたセキュリティ情報の登録を有効にする](howto-registration-mfa-sspr-combined.md)ことをお勧めします。 回復性を確保するために、ユーザーに複数の認証方法の登録を求めることをお勧めします。 サインインや SSPR の間にユーザーがある方法を使用できない場合、そのユーザーは別の方法で認証することを選択できます。 詳細については、[Azure AD で回復性があるアクセス制御管理戦略を作成する](concept-resilient-controls.md)方法に関するページを参照してください。

## <a name="authentication-method-strength-and-security"></a>認証方法の強度とセキュリティ

組織で Azure Multi-Factor Authentication のような機能を展開する場合は、使用可能な認証方法を確認してください。 セキュリティ、ユーザビリティ、および可用性の要件を満たす、あるいは超える方法を選択します。 可能であれば、最高レベルのセキュリティを実現する認証方法を使用してください。

次の表は、使用可能な認証方法のセキュリティに関する考慮事項の概要を示しています。 可用性とは、ユーザーが認証方法を利用できるかどうかの指標を指し、Azure AD のサービス可用性ではありません。

| 認証方法          | セキュリティ | 使いやすさ | 可用性 |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | 高     | 高      | 高         |
| Microsoft Authenticator アプリ    | 高     | 高      | 高         |
| FIDO2 セキュリティ キー (プレビュー)   | 高     | 高      | 高         |
| OATH ハードウェア トークン (プレビュー) | Medium   | Medium    | 高         |
| OATH ソフトウェア トークン           | Medium   | Medium    | 高         |
| SMS                            | Medium   | 高      | Medium       |
| 音声                          | Medium   | Medium    | Medium       |
| Password                       | 低      | 高      | 高         |

セキュリティの詳細については、[認証の脆弱性と攻撃ベクトル](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)に関するページを参照してください。

> [!TIP]
> 柔軟性と使いやすさを実現するために、Microsoft Authenticator アプリを使用することをお勧めします。 この認証方法は、パスワードレス、MFA のプッシュ通知、OATH コードなど、最適なユーザー エクスペリエンスと複数のモードを提供します。

## <a name="how-each-authentication-method-works"></a>各認証方法のしくみ

認証方法の中には、FIDO2 セキュリティ キーやパスワードの利用といった、アプリケーションやデバイスにサインインする際にプライマリ要素として利用できるものがあります。 他の認証方法は、Azure Multi-Factor Authentication または SSPR を使用する場合のセカンダリ要素としてのみ使用できます。

次の表は、サインイン イベント時に各認証方法がどのタイミングで使用できるかの概要を示しています。

| Method                         | プライマリ認証 | セカンダリ認証  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | はい                    | MFA                       |
| Microsoft Authenticator アプリ    | はい (プレビュー)          | MFA と SSPR              |
| FIDO2 セキュリティ キー (プレビュー)   | はい                    | MFA                       |
| OATH ハードウェア トークン (プレビュー) | いいえ                     | MFA                       |
| OATH ソフトウェア トークン           | いいえ                     | MFA                       |
| SMS                            | はい (プレビュー)          | MFA と SSPR              |
| 音声通話                     | いいえ                     | MFA と SSPR              |
| Password                       | はい                    |                           |

これらの認証方法は、すべて Azure portal で構成できます。また、[Microsoft Graph REST API ベータ版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)を使用して構成することが増えています。

各認証方法のしくみの詳細については、次の概念に関する個別の記事を参照してください。

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)
* [FIDO2 セキュリティ キー (プレビュー)](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH ハードウェア トークン (プレビュー)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH ソフトウェア トークン](concept-authentication-oath-tokens.md#oath-software-tokens)
* SMS [サインイン (プレビュー)](howto-authentication-sms-signin.md) および[検証](concept-authentication-phone-options.md#mobile-phone-verification)
* [音声通話の確認](concept-authentication-phone-options.md)
* Password

> [!NOTE]
> Azure AD では、多くの場合、パスワードはプライマリ認証方法の 1 つです。 パスワード認証方法を無効にすることはできません。 プライマリ認証要素としてパスワードを使用する場合は、Azure Multi-Factor Authentication を使用してサインイン イベントのセキュリティを強化します。

特定のシナリオでは、次の追加認証方法を使用できます。

* [アプリ パスワード](howto-mfa-app-passwords.md) - 先進認証をサポートしておらず、ユーザーごとの Azure Multi-Factor Authentication 用に構成できる古いアプリケーションに使用されます。
* [セキュリティの質問](concept-authentication-security-questions.md) - SSPR でのみ使用されます
* [メールアドレス](concept-sspr-howitworks.md#authentication-methods) - SSPR にのみ使用されます

## <a name="next-steps"></a>次のステップ

最初に、[セルフサービス パスワード リセット (SSPR) のチュートリアル][tutorial-sspr]と [Azure Multi-Factor Authentication][tutorial-azure-mfa] に関するページを参照してください。

SSPR の概念の詳細については、[Azure AD のセルフサービス パスワード リセットのしくみ][concept-sspr]に関するページを参照してください。

MFA の概念の詳細については、[Azure Multi-Factor Authentication のしくみ][concept-mfa]に関するページを参照してください。

[Microsoft Graph REST API ベータ版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)を使用した認証方法の構成の詳細について確認してください。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
