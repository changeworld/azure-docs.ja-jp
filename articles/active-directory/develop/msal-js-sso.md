---
title: シングル サインオン (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用したシングル サインオン エクスペリエンスのビルドについて説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e715b0b2176564c8a492df20b9193d66a8fb85c8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466088"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js でのシングル サインオン

シングル サインオン (SSO) を使用すると、ユーザーは、資格情報を 1 回入力してサインインし、認証をもう一度行う必要なく複数のアプリケーションで再利用できるセッションを確立することができます。 このセッションにより、ユーザーにシームレスなエクスペリエンスが提供され、資格情報を繰り返し要求されることが減ります。

Azure Active Directory (Azure AD) では、ユーザーが初めて認証を行うときに、セッション Cookie を設定することで、アプリケーションに SSO 機能を提供します。 MSAL.js ライブラリを使用すると、アプリケーションによってセッション Cookie をいくつかの方法で適用できます。

## <a name="sso-between-browser-tabs"></a>ブラウザーのタブ間の SSO

アプリケーションで複数のタブが開かれ、ユーザーは最初に 1 つのタブでサインインする場合、ユーザーは要求されることなく他のタブにもサインインします。 MSAL.js は、ブラウザーの `localStorage` にユーザーの ID トークンをキャッシュし、他の開いているタブでユーザーをアプリケーションにサインインさせます。

既定で MSAL.js によって使用される `sessionStorage` では、セッションをタブ間で共有することはできません。 タブ間の SSO を利用するには、次に示すように、MSAL.js で `cacheLocation` を `localStorage` に設定します。

```javascript
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>アプリ間の SSO

ユーザーが認証を行うと、ブラウザーの Azure AD ドメインでセッション Cookie が設定されます。 MSAL.js では、このセッション Cookie に依存して、ユーザーに異なるアプリケーション間の SSO が提供されます。 また、MSAL.js では、アプリケーション ドメインごとに、ユーザーの ID トークンとアクセス トークンがブラウザーのストレージにキャッシュされます。 その結果、SSO の動作は場合によって異なります。

### <a name="applications-on-the-same-domain"></a>同じドメイン上のアプリケーション

アプリケーションが同じドメインでホストされている場合、ユーザーは、アプリに 1 回サインインすれば、他のアプリについてもプロンプトなしで認証されます。 MSAL.js では、ドメインにキャッシュされているユーザーのトークンを使用して、SSO が提供されます。

### <a name="applications-on-different-domain"></a>異なるドメイン上のアプリケーション

アプリケーションが異なるドメインでホストされているときは、ドメイン A でキャッシュされたトークンに、ドメイン B の MSAL.js はアクセスできません。

ユーザーがドメイン A にサインインしているときに、ドメイン B 上のアプリケーションに移動すると、ユーザーがリダイレクトされるか、サインイン ページが表示されます。 ユーザー セッション Cookie はまだ Azure AD によって保持されているので、ユーザーはサインインし、資格情報を要求するプロンプトは表示されません。

ユーザーが Azure AD でセッションに複数のユーザー アカウントを持っている場合、ユーザーはサインインするアカウントの選択を求められます。

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD でのアカウントの自動選択

場合によっては、アプリケーションがユーザーの認証コンテキストにアクセスでき、複数のアカウントでサインインしているときに、Azure AD のアカウント選択プロンプトを省略する必要があります。 Azure AD アカウントの選択プロンプトの省略は、いくつかの方法で行うことができます。

**セッション ID の使用**

セッション ID (SID) は、ID トークンで構成できる[オプションの要求](active-directory-optional-claims.md)です。 要求により、アプリケーションでは、ユーザーのアカウント名やユーザー名とは関係なく、ユーザーの Azure AD セッションを識別できます。 `acquireTokenSilent` の呼び出しへの要求パラメーターで、SID を渡すことができます。 要求パラメーター内で `acquireTokenSilent` を使用すると、Azure AD はアカウントの選択を省略できます。 SID はセッション Cookie にバインドされていて、ブラウザーのコンテキストをまたぎません。

```javascript
var request = {
  scopes: ["user.read"],
  sid: sid,
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

SID は、MSAL.js での `acquireTokenSilent` の呼び出しによって行われるサイレント認証要求でのみ使用できます。 アプリケーション マニフェストでオプションの要求を構成する手順については、「[アプリに省略可能な要求を提供する](active-directory-optional-claims.md)」をご覧ください。

**ログイン ヒントの使用**

SID の要求を構成していない場合、または対話型認証の呼び出しでアカウント選択のプロンプトをバイパスする必要がある場合は、MSAL.js の対話型メソッド (`loginPopup`、`loginRedirect`、`acquireTokenPopup`、`acquireTokenRedirect`) において、要求パラメーターで `login_hint` を提供し、必要に応じて `domain_hint` を `extraQueryParameters` として渡すことにより、これを行うことができます。 次に例を示します。

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication.loginRedirect(request);
```

ユーザーに対する ID トークンで返される要求を読み取ることにより、login_hint と domain_hint の値を取得できます。

- **loginHint** には、ID トークンの `preferred_username` 要求を設定する必要があります。

- **domain_hint** は、/common 機関を使用するときにのみ、渡す必要があります。 ドメイン ヒントは、テナント ID (tid) によって決定されます。 ID トークンの `tid` 要求が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、それはコンシューマーです。 それ以外の場合は、組織になります。

**login_hint** と **domain_hint** の詳細については、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)に関する記事をご覧ください。

## <a name="sso-without-msaljs-login"></a>MSAL.js ログインなしの SSO

仕様上、MSAL.js では、API のトークンを取得する前に、ログイン メソッドを呼び出してユーザー コンテキストを確立する必要があります。 ログイン メソッドは対話形式であるため、ユーザーにはプロンプトが表示されます。

アプリケーションが別のアプリケーションで開始された認証を通じて認証されたユーザーのコンテキストまたは ID トークンにアクセスすることができ、最初に MSAL.js 経由でサインインすることなく、SSO を使用してトークンを取得することが望ましい場合があります。

例: ユーザーは、アドオンまたはプラグインとして実行されている別の JavaScript アプリケーションをホストするブラウザーで Microsoft アカウントにサインインしています。この場合、Microsoft アカウント サインインが必要です。

このシナリオでの SSO は、次のようにして実現できます。

次のように、MSAL.js の `acquireTokenSilent` の呼び出しへの要求パラメーターとして、使用可能な場合は `sid` を渡します (または、`login_hint` および必要に応じて `domain_hint`)。

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>MSAL.js の更新に対する ADAL.js での SSO

MSAL.js には、Azure AD の認証シナリオに対する ADAL.js での機能パリティがあります。 ADAL.js から MSAL.js への移行を容易にし、ユーザーにサインインを繰り返し要求しなくて済むように、ライブラリは ADAL.js キャッシュ内にあるユーザーのセッションを表す ID トークンを読み取り、ユーザーを MSAL.js にシームレスにサインインさせます。

ADAL.js から更新するときに SSO の動作を利用するには、ライブラリでトークンのキャッシュ用に `localStorage` が使用されるようにする必要があります。 次のように、初期化時に、MSAL.js と ADAL.js 両方の構成で、`cacheLocation` を `localStorage` に設定します。

```javascript
// In ADAL.js
window.config = {
  clientId: "g075edef-0efa-453b-997b-de1337c29185",
  cacheLocation: "localStorage",
};

var authContext = new AuthenticationContext(config);

// In latest MSAL.js version
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

`cacheLocation` が構成されると、MSAL.js では ADAL.js で認証されたユーザーのキャッシュされた状態を読み取って、MSAL.js で SSO を提供するために使用することができます。

## <a name="next-steps"></a>次の手順

SSO について詳しくは、以下をご覧ください。

- [シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)
- [構成可能なトークンの有効期間](active-directory-configurable-token-lifetimes.md)
