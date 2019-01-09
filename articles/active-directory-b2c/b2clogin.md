---
title: Azure Active Directory B2C の b2clogin.com にリダイレクト URL を設定する | Microsoft Docs
description: Azure Active Directory B2C のリダイレクト URL での b2clogin.com の使用について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ffceb8fd6f1afcd054bfc4c4035fb2b8b93ed390
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720543"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の b2clogin.com にリダイレクト URL を設定する

Azure Active Directory (Azure AD) B2C アプリケーションへのサインアップおよびサインイン用に ID プロバイダーを設定する場合は、リダイレクト URL を指定する必要があります。 以前は login.microsoftonline.com が使用されていましたが、現在は b2clogin.com を使用する必要があります。

b2clogin.com を使用すると、次のような利点が加わります。

- Cookie は、他の Microsoft サービスと共有されなくなりました。
- お使いの URL に、Microsoft への参照が含まれなくなりました。 たとえば、「 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 」のように入力します。

b2clogin.com を使用する際は、変更が必要になる可能性があるこれらの設定に配慮してください。

- ID プロバイダー アプリケーションのリダイレクト URL を、b2clogin.com を使用するように設定します。 
- Azure AD B2C アプリケーションを、ユーザー フロー参照とトークン エンドポイントに b2clogin.com を使用するように設定します。 
- MSAL を使用している場合は、**ValidateAuthority** プロパティを `false` に設定する必要があります。
- [ユーザー インターフェイスのカスタマイズ](active-directory-b2c-ui-customization-custom-dynamic.md)のため、CORS の設定で定義した任意の**許可される元のドメイン**を必ず変更します。  

## <a name="change-redirect-urls"></a>リダイレクト URL の変更

b2clogin.com を使用するには、お使いの ID プロバイダー アプリケーションの設定で信頼できる URL の一覧を探して、Azure AD B2C にリダイレクトするよう変更します。  現時点では、おそらくはいくつかの login.microsoftonline.com サイトにリダイレクトするよう設定されています。 

`your-tenant-name.b2clogin.com` が承認されるように、リダイレクト URL を変更する必要があります。 `your-tenant-name` をお使いの Azure AD B2C テナントの名前と置き換え、`/te` が URL 内に存在する場合は削除してください。 この URL には ID プロバイダーごとに若干のバリエーションがあるため、対応するページを確認して正確な URL を取得してください。

ID プロバイダーの設定情報については、次の記事を参照してください。

- [Microsoft アカウント](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [カスタム OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>アプリケーションの更新

お使いの Azure AD B2C アプリケーションでは、おそらく、ユーザー フロー参照やトークン エンドポイントなどのいくつかの場所で `login.microsoftonline.com` が参照されています。  承認エンドポイント、トークン エンドポイント、および発行者が、`your-tenant-name.b2clogin.com` を使用するように更新されていることを確認してください。  

## <a name="set-the-validateauthority-property"></a>ValidateAuthority プロパティの設定

MSAL を使用している場合は、**ValidateAuthority** を `false` に設定します。 次の例は、このプロパティを設定する方法を示しています。

[MSAL for .Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) の場合:

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

[MSAL for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) の場合:

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
