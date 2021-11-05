---
title: MSAL.js クライアント アプリを初期化する | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用することによるクライアント アプリケーションの初期化について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 2680f78f7bcd1caaa3d6d1280c51c81b97a52048
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050643"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js を使用してクライアント アプリケーションを初期化する

この記事では、ユーザー エージェント アプリケーションのインスタンスを使用して JavaScript 用 Microsoft Authentication Library (MSAL.js) を初期化する方法について説明します。

ユーザー エージェント アプリケーションは、Web ブラウザーなどのユーザー エージェントでクライアント コードが実行されるパブリック クライアント アプリケーションの一種です。 このようなクライアントは、ブラウザーコンテキストにアクセスできるため、シークレットを保存しません。

クライアント アプリケーションの種類とアプリケーションの構成オプションの詳細については、[MSAL のパブリック クライアント アプリケーションと機密クライアント アプリケーション](msal-client-applications.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

アプリケーションを初期化する前に、まず、その[アプリケーションを Azure portal に登録](scenario-spa-app-registration.md)して、アプリケーションと Microsoft ID プラットフォーム間で信頼関係を確立する必要があります。

アプリの登録後、Azure portal で確認できる次の値の一部またはすべてが必要になります。

| 値                   | 必須 | 説明                                                                                                                                                                |
| :---------------------- | :------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| アプリケーション (クライアント) ID | 必須 | Microsoft ID プラットフォーム内でアプリケーションを一意に識別する GUID。                                                                                   |
| Authority               | 省略可能 | ID プロバイダーの URL (_インスタンス_) とアプリケーションの _サインイン対象ユーザー_。 インスタンスとサインイン対象ユーザーが連結されると、_Authority_ が構成されます。 |
| ディレクトリ (テナント) ID   | 省略可能 | 組織専用の基幹業務アプリケーション (_シングルテナント アプリケーション_ とも呼ばれる) をビルドしている場合は、ディレクトリ(テナント)IDを指定します。          |
| リダイレクト URI            | 省略可能 | Web アプリを構築している場合、`redirectUri` では、ID プロバイダー (Microsoft ID プラットフォーム) が発行済みのセキュリティ トークンを返す場所を指定します。   |

## <a name="initialize-msaljs-2x-apps"></a>MSAL.js 2.x アプリの初期化

[構成オブジェクト][msal-js-configuration]を使用して [PublicClientApplication][msal-js-publicclientapplication] をインスタンスすることで、MSAL 認証コンテキストを初期化します。 最低限必要な構成プロパティは、アプリケーションの `clientID` です。これは、Azure portal のアプリ登録の **[概要]** ページに **アプリケーション (クライアント) ID** として表示されます。

次に、構成オブジェクトと `PublicClientApplication` のインスタンス化の例を示します。

```javascript
const msalConfig = {
  auth: {
    clientId: "11111111-1111-1111-111111111111",
    authority: "https://login.microsoftonline.com/common",
    knownAuthorities: [],
    redirectUri: "https://localhost:3001",
    postLogoutRedirectUri: "https://localhost:3001/logout",
    navigateToLoginRequestUrl: true,
  },
  cache: {
    cacheLocation: "sessionStorage",
    storeAuthStateInCookie: false,
  },
  system: {
    loggerOptions: {
      loggerCallback: (
        level: LogLevel,
        message: string,
        containsPii: boolean
      ): void => {
        if (containsPii) {
          return;
        }
        switch (level) {
          case LogLevel.Error:
            console.error(message);
            return;
          case LogLevel.Info:
            console.info(message);
            return;
          case LogLevel.Verbose:
            console.debug(message);
            return;
          case LogLevel.Warning:
            console.warn(message);
            return;
        }
      },
      piiLoggingEnabled: false,
    },
    windowHashTimeout: 60000,
    iframeHashTimeout: 6000,
    loadFrameTimeout: 0,
  },
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance
  .handleRedirectPromise()
  .then((tokenResponse) => {
    // Handle redirect response
  })
  .catch((error) => {
    // Handle redirect error
  });
```

### `handleRedirectPromise`

アプリケーションでリダイレクト フローを使用する場合は、[handleRedirectPromise][msal-js-handleredirectpromise] を呼び出します。 リダイレクト フローを使用する場合は、すべてのページ読み込みで `handleRedirectPromise` を実行する必要があります。

Promise では、次の 3 つの結果が考えられます。

- `.then` が呼び出され、`tokenResponse` が truthy である: リダイレクト操作が成功すると、アプリケーションに戻ります。
- `.then` が呼び出され、`tokenResponse` が 偽物 (`null`) である: リダイレクト操作では、アプリケーションに戻りません。
- `.catch` が呼び出される: リダイレクト操作によりアプリケーションに戻りますが、エラーが発生しています。

## <a name="initialize-msaljs-1x-apps"></a>MSAL.js 1.x アプリの初期化

構成オブジェクトを使用して [UserAgentApplication][msal-js-useragentapplication] をインスタンス化することで、MSAL 1.x 認証コンテキストを初期化します。 最低限必要な構成プロパティは、アプリケーションの `clientID` です。これは、Azure portal のアプリ登録の **[概要]** ページに **アプリケーション (クライアント) ID** として表示されます。

リダイレクト フローを使用した認証メソッドの場合 ([loginRedirect][msal-js-loginredirect] と [acquireTokenRedirect][msal-js-acquiretokenredirect])、MSAL.js 1.2.x 以前では、`handleRedirectCallback()` メソッドを介して成功またはエラーに対するコールバックを明示的に登録する必要があります。 MSAL.js 1.2. x以前ではコールバックを明示的にレジスタする必要がある。これは、リダイレクトフローが、ポップアップエクスペリエンスを持つメソッドと同様に promise を返さないためです。 バージョン 1.3.x 以降の MSAL.js では、コールバックの登録は _省略可能_ です。

```javascript
// Configuration object constructed
const msalConfig = {
  auth: {
    clientId: "11111111-1111-1111-111111111111",
  },
};

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
  // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>単一インスタンスと構成

MSAL.js 1.x および 2.x では、`UserAgentApplication` または`PublicClientApplication` の 1 つのインスタンスと構成によって、それぞれが 1 つの認証コンテキストを表すように設計されています。

ブラウザーでキャッシュエントリとビヘイビアーが競合するため、`UserAgentApplication`または`PublicClientApplication`の複数のインスタンスをお勧めしません。

## <a name="next-steps"></a>次の手順

GitHub にある、この MSAL.js 2.x コード サンプルでは、次の[構成][msal-js-configuration]オブジェクトを使用して [PublicClientApplication][msal-js-publicclientapplication] をインスタンスする方法を示しています。

[Azure-Samples/ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->

[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
