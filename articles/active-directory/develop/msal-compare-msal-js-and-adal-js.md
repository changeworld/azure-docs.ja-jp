---
title: JavaScript アプリケーションを ADAL.js から MSAL.js に移行する | Azure
titleSuffix: Microsoft identity platform
description: 認証と認可に Active Directory 認証ライブラリ (ADAL) ではなく、Microsoft 認証ライブラリ (MSAL) を使用するために既存の JavaScript アプリケーションを更新する方法。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: marsma
ms.custom: has-adal-ref
ms.openlocfilehash: 6614c1d06dd1e5093dfbe7e3ea3a20ff13ec74c1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501041"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>JavaScript アプリを ADAL.js から MSAL.js に移行する方法

[Microsoft Authentication Library for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) (MSAL.js、*msal-browser* とも呼ばれます) 2.x は、Microsoft ID プラットフォーム上の JavaScript アプリケーションに使用することが推奨される認証ライブラリです。 この記事では、ADAL.js を使用しているアプリを MSAL.js 2.x に移行するために必要な変更について説明します。

> [!NOTE]
> MSAL.js 1.x ではなく MSAL.js 2.x を強くお勧めします。 認証コードの付与フローがより安全になり、サードパーティーの Cookie をブロックするために Safari などのブラウザーに実装されているプライバシー対策に関係なく、シングルページ アプリケーションが良好なユーザー エクスペリエンスを維持できるなどの利点があります。

## <a name="prerequisites"></a>前提条件

- [アプリの登録] ポータルで **[プラットフォーム]**  /  **[Reply URL Type]\(返信 URL の種類\)** を **[Single-page application]\(シングルページ アプリケーション\)** に設定する必要があります (アプリの登録で、**Web** など、他のプラットフォームを追加している場合は、リダイレクト URI が重複しないようにする必要があります。 [リダイレクト URI に関する制限](./reply-url.md)のページを参照してください)
- **Internet Explorer** 上でアプリを実行するには、MSAL.js が依存する ES6 の機能 (Promise など) に対して [polyfills](./msal-js-use-ie-browser.md) を指定する必要があります。
- Azure AD アプリをまだ [v2 エンドポイント](../azuread-dev/azure-ad-endpoint-comparison.md)に移行していない場合は、必ず移行してください。

## <a name="install-and-import-msal"></a>MSAL をインストールしてインポートする

MSAL.js 2.x のライブラリをインストールするには 2 つの方法があります。

### <a name="via-npm"></a>NPM 経由:

```console
npm install @azure/msal-browser
```

この場合、お使いのモジュール システムに応じて、次のようにインポートします。

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>CDN 経由:

HTML ドキュメントのヘッダー セクションにスクリプトを読み込みます。

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

CDN を使用する場合の代替の CDN リンクとベスト プラクティスについては、[CDN の使用方法](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)に関するページを参照してください。

## <a name="initialize-msal"></a>MSAL の初期化

ADAL.js で、[AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) クラスのインスタンスを作成します。これにより、認証を実現するために使用できるメソッド (`login`、`acquireTokenPopup` など) が公開されます。 このオブジェクトは、アプリケーションから承認サーバーや ID プロバイダーへの接続を表すものです。 初期化するときに必須のパラメーターは、**clientId** のみです。

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

MSAL.js の場合は、代わりに [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) クラスのインスタンスを作成します。 ADAL.js と同様に、コンストラクターには、少なくとも `clientId` パラメーターを含む[構成オブジェクト](#configure-msal)が必要です。 詳細については、[MSAL.js の初期化](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md)に関するページを参照してください。

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

ADAL.js と MSAL.js のいずれも、機関 URI を指定しない場合の既定値は `https://login.microsoftonline.com/common` です。

> [!NOTE]
> v2.0 で `https://login.microsoftonline.com/common` 機関を使用する場合、ユーザーが Azure AD 組織または個人用 Microsoft アカウント (MSA) でサインインできるようにします。 MSAL.js では、Azure AD アカウントへのログインを制限する場合 (ADAL.js の場合と同じ動作)、代わりに `https://login.microsoftonline.com/organizations` を使用してください。

## <a name="configure-msal"></a>MSAL を構成する

[AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) の初期化時に使用される [ADAL.js の構成オプション](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context)の一部は MSAL.js では非推奨になり、いくつかの新しいオプションが導入されました。 [使用できるオプションの詳細な一覧](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)を参照してください。 重要な点は、これらのオプションの多くは、`clientId` を除き、トークン取得時にオーバーライドできるため、*要求ごと* に設定できるということです。 たとえば、トークンを取得するときに、初期化時に設定したものとは異なる **機関 URI** または **リダイレクト URI** を使用することができます。

さらに、構成オプションでログイン エクスペリエンス (ポップアップ ウィンドウを使用するか、ページをリダイレクトするかなど) を指定する必要もなくなりました。 その代わり、`MSAL.js` では、`PublicClientApplication` インスタンスを介して `loginPopup` と `loginRedirect` のメソッドが公開されています。

## <a name="enable-logging"></a>ログの有効化

ADAL.js の場合、コード内の任意の場所でログを別途構成します。

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

MSAL.js の場合、ログは構成オプションの一部であり、`PublicClientApplication` の初期化時に作成されます。

```javascript
const msalConfig = {
  auth: {
      // authentication related parameters
  },
  cache: {
      // cache related parameters
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
}

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>MSAL API に切り替える

ADAL.js の一部のパブリック メソッドには、MSAL.js に同等のものがあります。

| ADAL                                | MSAL                              | メモ                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 名前が変更され、[アカウント](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) オブジェクトが必要になりました |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | 非同期になり、Promise が返されるようになりました              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | 非同期になり、Promise が返されるようになりました              |
| `handleWindowCallback`              | `handleRedirectPromise`           | リダイレクト機能を使用する場合に必要です          |
| `getCachedUser`                     | `getAllAccounts`                  | 名前が変更され、アカウントの配列が返されるようになりました。|

その他のものは非推奨になりましたが、MSAL.js に新しいメソッドが用意されています。

| ADAL                              | MSAL                            | メモ                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | 該当なし                             | 非推奨になりました。 `loginPopup` または `loginRedirect` を使用します  |
| `logOut`                          | 該当なし                             | 非推奨になりました。 `logoutPopup` または `logoutRedirect` を使用します|
| 該当なし                               | `loginPopup`                    |                                                  |
| 該当なし                               | `loginRedirect`                 |                                                  |
| 該当なし                               | `logoutPopup`                   |                                                  |
| 該当なし                               | `logoutRedirect`                |                                                  |
| 該当なし                               | `getAccountByHomeId`            | ホーム ID (oid + テナント ID) を使用してアカウントをフィルター処理します    |
| 該当なし                               | `getAccountLocalId`             | ローカル ID を使用してアカウントをフィルター処理します (ADFS に役立ちます)   |
| 該当なし                               | `getAccountUsername`            | ユーザー名を使用してアカウントをフィルター処理します (存在する場合)         |

さらに、MSAL.js は ADAL.js とは異なり、TypeScript で実装されているため、プロジェクトで使用できるさまざまな型とインターフェイスが公開されています。 詳細については [MSAL.js API リファレンス](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)のページを参照してください。

## <a name="use-scopes-instead-of-resources"></a>リソースの代わりにスコープを使用する

Azure AD **v1.0** と **v2.0** のエンドポイントの重要な違いは、リソースへのアクセス方法に関するものです。 **v1.0** エンドポイントに ADAL.js を使用する場合、最初にアプリ登録ポータルにアクセス許可を登録してから、下に示すように、リソース (Microsoft Graph など) のアクセス トークンを要求します。

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js は、**v1.0** と **v2.0** の両方のエンドポイントをサポートします。 **v2.0** エンドポイントには、リソースへのアクセスに "*スコープ中心*" モデルが採用されています。 したがって、リソースのアクセス トークンを要求するときは、そのリソースのスコープも指定する必要があります。

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

スコープ中心モデルの利点の 1 つは、"*動的スコープ*" を使用できることです。 v1.0 エンドポイントを使用してアプリケーションを作成するときは、ユーザーがログイン時に同意するアプリケーションで必要なアクセス許可 ("*静的スコープ*" と呼ばれます) の完全なセットを登録する必要がありました。 v2.0 では、スコープ パラメーターを使用して、アクセス許可を必要なときに要求できます (この理由により、"*動的スコープ*")。 これによって、ユーザーはスコープに **増分同意** を与えることができます。 最初ユーザーにはアプリケーションへのサインインだけを行わせ、どのような種類のアクセスも必要としない場合、そうすることができます。 その後、ユーザーの予定表を読み取る機能が必要になった場合は、acquireToken メソッドで予定表のスコープを要求してユーザーの同意を得ることができます。 詳細については、「[リソースとスコープ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)」を参照してください

## <a name="use-promises-instead-of-callbacks"></a>コールバックの代わりに Promise を使用する

ADAL.js では、認証が成功し、応答が取得された後に、すべての操作にコールバックが使用されます。

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

MSAL.js では、Promise が代わりに使用されます。

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

ES8 に付属する **async と await** 構文を使用することもできます。

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>トークンのキャッシュと取得

トークンとその他の認証成果物をブラウザーのストレージにキャッシュするために、ADAL.js と同様に、MSAL.js には [Web Storage API](https://developer.mozilla.org/docs/Web/API/Web_Storage_API) が使用されます。 ユーザーが取得したトークンをより安全に格納できるため、`sessionStorage` オプションを使用することが推奨されます ([構成](#configure-msal)に関するページを参照してください)。ただし、`localStorage` を使用すると、タブとユーザー セッションをまたいだ[シングル サインオン](./msal-js-sso.md)を実行できるようになります。

重要な点は、キャッシュに直接アクセスすることは想定されていないということです。 その代わり、適切な MSAL.js の API を使用して、アクセス トークンやユーザー アカウントなどの認証成果物を取得する必要があります。

## <a name="renew-tokens-with-refresh-tokens"></a>更新トークンを使用してトークンを更新する

ADAL.js には [OAuth 2.0 暗黙的フロー](./v2-oauth2-implicit-grant-flow.md)が使用されています。この場合、セキュリティ上の理由から更新トークンが返されません (更新トークンはアクセス トークンよりも有効期間が長いので、悪意のあるアクターの手に渡るとより危険です)。 そのため、ADAL.js の場合、ユーザーが何度も認証を求められないように、トークンの更新に非表示のフレームが使用されています。

PKCE をサポートする認証コード フローの場合、MSAL.js 2.x を使用するアプリは、ID トークンとアクセス トークンと共に更新トークンを受け取ります。更新にこれを使用することができます。 更新トークンの使用方法は抽象化されており、開発者がそれに関するロジックを構築することはことは想定されていません。 その代わり、更新トークンを使用したトークンの更新は、MSAL によって自動的に管理されます。 ADAL.js を使用した以前のトークン キャッシュは MSAL.js に転送できません。これは、トークン キャッシュのスキーマが変更され、ADAL.js で使用されているスキーマとは互換性がないためです。

## <a name="handle-errors-and-exceptions"></a>エラーと例外を処理する

MSAL.js を使用している場合に、最も一般的な種類のエラーは、`interaction_in_progress` エラーです。 このエラーは、ある対話型 API (`loginPopup`、`loginRedirect`、`acquireTokenPopup`、`acquireTokenRedirect`) が呼び出されたときに、別の対話型 API がまだ進行中だった場合に発生します。 `login*` と `acquireToken*` の API は "*非同期*" なので、別の API を呼び出す前に、結果の Promise が解決されていることを確認する必要があります。

もう 1 つの一般的なエラーは `interaction_required` です。 多くの場合、このエラーは単に対話型トークン取得のプロンプトを開始するだけで解決されます。 たとえば、アクセスしようとしている Web API に[条件付きアクセス](../conditional-access/overview.md) ポリシーが設定されている場合があり、その場合、ユーザーは[多要素認証](../authentication/concept-mfa-howitworks.md) (MFA) を実行する必要があります。 この場合、`acquireTokenPopup` または `acquireTokenRedirect` をトリガーして `interaction_required` エラーを処理すると、ユーザーに MFA を要求するプロンプトが表示され、それを実行できるようになります。

発生する可能性のあるもう一つの一般的なエラーは `consent_required` です。これは、保護されたリソースのアクセス トークンを取得するために必要な権限がユーザーによって同意されていない場合に発生します。 `interaction_required` の場合と同様に、`consent_required` エラーのソリューションによって、`acquireTokenPopup` または `acquireTokenRedirect` を使用した対話的なトークン取得のプロンプトが開始されることがよくあります。

詳細については、[一般的な MSAL.js エラーとその処理方法](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md)に関するページを参照してください

## <a name="use-the-events-api"></a>イベント API を使用する

MSAL.js (v2.4 以降) には、アプリで使用できるイベント API が導入されました。 これらのイベントは、認証プロセスと、その時点で MSAL によって実行されていることに関連しており、UI の更新、エラー メッセージの表示、何らかの対話が進行中かどうかの確認などに使用できます。 たとえば、次のイベント コールバックは、何らかの理由でログイン処理が失敗したときに呼び出されます。

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

パフォーマンスのためには、イベント コールバックが不要になったら登録を解除することが重要です。 詳細については、[MSAL.js のイベント API](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md) に関するページを参照してください

## <a name="handle-multiple-accounts"></a>複数のアカウントを処理する

ADAL.js には、現在認証されているエンティティを表す "*ユーザー*" という概念があります。 MSAL.js の場合、ユーザーは複数のアカウントを自身に関連付けられるという事実を考慮して、"*ユーザー*" は "*アカウント*" に置き換えられています。 これは、複数のアカウントを管理し、適切なアカウントを選択する必要があることも意味します。 次のスニペットは、このプロセスを表しています。

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

詳細については、[MSAL.js のアカウント](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md)に関するページを参照してください。

## <a name="use-the-wrappers-libraries"></a>ラッパー ライブラリを使用する

Angular や React のフレームワーク用に開発している場合、それぞれ [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) と [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) を使用できます。 これらのラッパーにより、MSAL.js と同じパブリック API が公開されています。さらに、フレームワーク固有のメソッドやコンポーネントが用意されているので、認証とトークンの取得プロセスを効率化することができます。

## <a name="run-the-app"></a>アプリを実行する

変更が完了したら、アプリを実行し、認証シナリオをテストします。

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>例: ADAL Node と MSAL Node を使用した Web アプリのセキュリティ保護

次のスニペットは、ADAL.js と MSAL.js を使用して、Microsoft ID プラットフォームでユーザーを認証し、Microsoft Graph のアクセス トークンを取得するシングルページ アプリケーションに必要な最小限のコードを示しています。

<table>
<tr><td> ADAL.js の使用 </td><td> MSAL.js の使用 </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com",
            null, null,
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>次の手順

- [MSAL.js API リファレンス](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [MSAL.js コード サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)
