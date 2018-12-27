---
title: Azure Active Directory B2C のユーザー フロー | Microsoft Docs
description: Azure Active Directory B2C の拡張可能なポリシー フレームワークと、さまざまなユーザー フロー タイプの作成方法に関するトピック。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877084"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C:ユーザー フロー


Azure Active Directory (Azure AD) B2C の拡張可能なポリシー フレームワークは、このサービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイル編集などのコンシューマーの ID エクスペリエンスが完全に記述されています。 最も一般的な ID タスクを設定しやすくするために、Azure AD B2C ポータルには、**ユーザー フロー**という事前定義済みで構成できるポリシーが用意されています。 たとえば、サインアップ ユーザー フローを使用すると、次の設定を構成して動作を制御できます。

* コンシューマーがアプリケーションにサインアップするために使用できるアカウントの種類 (Facebook などのソーシャル アカウント、または電子メール アドレスなどのローカル アカウント)
* サインアップ中にコンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)
* Azure Multi-Factor Authentication の使用
* すべてのサインアップ ページの外観
* ユーザー フローの実行が完了したときにアプリケーションが受信する情報 (トークン内で要求として明示される)

テナント内でさまざまな種類のユーザー フローを複数作成し、必要に応じてそれらをアプリケーションで使用できます。 ユーザー フローは、アプリケーション間で再利用することができます。 この柔軟性により、開発者は最小限のコード変更で、またはコードをまったく変更せずにコンシューマーの ID エクスペリエンスを定義および変更できます。

ユーザー フローは、簡単な開発者インターフェイスを使用して入手できます。 アプリケーションは (要求でユーザー フロー パラメーターを渡す) 標準の HTTP 認証要求を使用してユーザー フローをトリガーし、カスタマイズされたトークンを応答として受信します。 たとえば、サインアップ ユーザー フローを呼び出す要求とサインイン ユーザー フローを呼び出す要求の唯一の違いは、"p" クエリ文字列パラメーターで使用されるユーザー フロー名です。

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>サインアップまたはサインイン ユーザー フローを作成する

このユーザー フローは、1 つの構成でコンシューマーのサインアップ エクスペリエンスとサインイン エクスペリエンスの両方を処理します。 コンシューマーは、状況に応じて正しいパス (サインアップまたはサインイン) に誘導されます。 このポリシーはまた、サインアップまたはサインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。**サインアップまたはサインイン** ユーザー フローのサンプル コードは、[こちらで入手できます](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。  **サインアップ** ユーザー フローまたは**サインイン** ユーザー フローには、このユーザー フローを使用することをお勧めします。  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>サインアップ ユーザー フローを作成する

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>サインイン ユーザー フローを作成する

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>プロファイル編集ユーザー フローを作成する

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>パスワードのリセット ユーザー フローを作成する

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>プレビュー ユーザー フロー

新機能がリリースされると、これらのいくつかは、既存のポリシーまたはユーザー フローからなくなる場合があります。  これらのユーザー フローが GA になったら、古いバージョンは同種の最新のものに置き換えられる予定です。  既存のポリシーまたはユーザー フローには変更はなく、これらの新機能を利用するには、新しいユーザー フローを作成する必要があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>サインアップまたはサインイン ユーザー フローをパスワード リセット ユーザー フローにリンクするにはどうすればよいですか?
**サインアップまたはサインイン** ユーザー フローを (ローカル アカウントで) 作成すると、エクスペリエンスの最初のページに **[パスワードを忘れた場合]** リンクが表示されます。 このリンクをクリックしても、パスワード リセット ユーザー フローは自動的にはトリガーされません。 

代わりに、エラー コード **`AADB2C90118`** がアプリに返されます。 アプリは、特定のパスワード リセット ユーザー フローを呼び出すことによって、このエラー コードを処理する必要があります。 詳細については、[ユーザー フローをリンクする方法を示すサンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)を参照してください。

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>サインアップまたはサインイン ユーザー フローと、サインアップ ユーザー フローおよびサインイン ユーザー フローのどちらを使用すればよいですか?
**サインアップ ポリシー** ユーザー フローおよび**サインイン** ユーザー フローより、**サインアップまたはサインイン** ユーザー フローを使用することをお勧めします。  

**サインアップまたはサインイン** ユーザー フローは、**サインイン** ユーザー フローより多くの機能を備えています。 また、ページ UI のカスタマイズを使用したり、ローカライズのより適切なサポートを受けたりすることもできます。 

ユーザー フローをローカライズする必要がなく、必要なのはブランドのマイナーなカスタマイズ機能だけであり、それにパスワード リセットを組み込みたい場合は、**サインイン** ユーザー フローをお勧めします。

## <a name="next-steps"></a>次の手順
* [トークン、セッション、およびシングル サインオンの構成](active-directory-b2c-token-session-sso.md)
* [コンシューマーのサインアップ時の電子メール検証の無効化](active-directory-b2c-reference-disable-ev.md)

