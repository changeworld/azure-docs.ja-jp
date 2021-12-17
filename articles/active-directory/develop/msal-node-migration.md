---
title: Node.js アプリケーションを ADAL から MSAL に移行する | Azure
titleSuffix: Microsoft identity platform
description: 認証と認可に Active Directory 認証ライブラリ (ADAL) ではなく、Microsoft 認証ライブラリ (MSAL) を使用するために既存の Node.js アプリケーションを更新する方法。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/26/2021
ms.author: marsma
ms.custom: has-adal-ref
ms.openlocfilehash: 33dd6efdbd03ce14beaf392e7bc24e051381903e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503017"
---
# <a name="how-to-migrate-a-nodejs-app-from-adal-to-msal"></a>Node.js アプリを ADAL から MSAL に移行する方法

現在、Microsoft ID プラットフォームに登録されているアプリケーションに対する認証と認可を有効にする場合に推奨される SDK は、[Node 用 Microsoft 認証ライブラリ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) (MSAL Node) です。 この記事では、Node 用 Active Directory 認証ライブラリ (ADAL Node) から MSAL Node にアプリを移行するために実行する必要がある重要な手順について説明します。

## <a name="prerequisites"></a>前提条件

- Node バージョン 10、12、または 14。 [バージョン サポートに関する注意事項](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node#node-version-support)を参照してください

## <a name="update-app-registration-settings"></a>アプリの登録設定を更新する

ADAL Node の使用時、**Azure AD v1.0 エンドポイント** を使用していた可能性があります。 ADAL から MSAL に移行するアプリでは、**Azure AD v2.0 エンドポイント** への切り替えを検討することも必要です。

1. [v1 と v2 のエンドポイントの違い](../azuread-dev/azure-ad-endpoint-comparison.md)を確認します
1. 必要に応じて、既存のアプリの登録を適切に更新します。

> [!NOTE]
> 下位互換性を確保するために、MSAL Node では v1.0 と v2.0 の両方のエンドポイントがサポートされています。

## <a name="install-and-import-msal"></a>MSAL をインストールしてインポートする

1. NPM を使用して MSAL Node パッケージをインストールします。

```console
npm install @azure/msal-node
```

1. その後、ご利用のコードに MSAL Node をインポートします。

```javascript
const msal = require('@azure/msal-node');
```

1. 最後に、ADAL Node パッケージをアンインストールし、コード内のすべての参照を削除します。

```console
npm uninstall adal-node
```

## <a name="initialize-msal"></a>MSAL の初期化

ADAL Node では、`AuthenticationContext` オブジェクトを初期化します。これにより、さまざまな認証フローで使用できるメソッドが公開されます (Web アプリに対する `acquireTokenWithAuthorizationCode` など)。 初期化するときに必須のパラメーターは、**機関 URI** のみです。

```javascript
var adal = require('adal-node');

var authorityURI = "https://login.microsoftonline.com/common";
var authenticationContex = new adal.AuthenticationContext(authorityURI);
```

MSAL Node では、代わりに 2 つの代替方法があります。モバイル アプリまたはデスクトップ アプリを作成する場合は、`PublicClientApplication` オブジェクトのインスタンスを作成します。 そのコンストラクターには、少なくとも `clientId` パラメーターを含む[構成オブジェクト](#configure-msal)が必要です。 MSAL によって機関 URI は既定で `https://login.microsoftonline.com/common` に設定されます (指定しない場合)。

```javascript
const msal = require('@azure/msal-node');

const pca = new msal.PublicClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID"
        }
    });
```

> [!NOTE]
> v2.0 で `https://login.microsoftonline.com/common` 機関を使用する場合、ユーザーが Azure AD 組織または個人用 Microsoft アカウント (MSA) でサインインできるようにします。 MSAL Node では、Azure AD アカウントへのログインを制限する場合 (ADAL Node の場合と同じ動作)、代わりに `https://login.microsoftonline.com/organizations` を使用してください。

一方、Web アプリまたはデーモン アプリを作成する場合は、`ConfidentialClientApplication` オブジェクトのインスタンスを作成します。 このようなアプリでは、クライアント シークレットや証明書などの "*クライアント資格情報*" も指定する必要があります。

```javascript
const msal = require('@azure/msal-node');

const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID",
            clientSecret = "YOUR_CLIENT_SECRET"
        }
    });
```

ADAL の `AuthenticationContext` とは異なり、`PublicClientApplication` と `ConfidentialClientApplication` はいずれもクライアント ID にバインドされています。 これは、アプリケーションで使用したいさまざまなクライアント ID がある場合、それぞれに対して新しい MSAL インスタンスを作成する必要があることを意味します。 詳細については、[MSAL Node の初期化](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)に関する記事を参照してください

## <a name="configure-msal"></a>MSAL を構成する

Microsoft ID プラットフォームでアプリを作成する場合、アプリには認証に関連する多くのパラメーターが含まれることになります。 ADAL Node では、`AuthenticationContext` オブジェクトには、そのインスタンスの作成に使用できる構成パラメーターの数が制限されていますが、残りのパラメーターはコード内で制限されずに使用されます (例: *clientSecret*)。

```javascript
var adal = require('adal-node');

var authority = "https://login.microsoftonline.com/YOUR_TENANT_ID"
var validateAuthority = true,
var cache = null;

var authenticationContext = new adal.AuthenticationContext(authority, validateAuthority, cache);
```

- `authority`: トークン機関を識別する URL
- `validateAuthority`: コードが悪意のある可能性のある機関にトークンを要求しないようにする機能
- `cache`: この AuthenticationContext インスタンスによって使用されるトークン キャッシュを設定します。  このパラメーターが設定されていない場合は、既定値のインメモリ キャッシュが使用されます

一方、MSAL Node では、種類 [Configuration](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html#configuration) の構成オブジェクトを使用します。 これには、次のプロパティが含まれます。

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "YOUR_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        clientSecret: "YOUR_CLIENT_SECRET",
        knownAuthorities: [],
    },
    cache: {
        // your implementation of caching
    },
    system: {
        loggerOptions: { /** logging related options */ }
    }
}


const cca = new msal.ConfidentialClientApplication(msalConfig);
```

重要な違いとして、MSAL には、機関の検証を無効にするフラグがなく、機関は必ず既定で検証されます。 要求した機関は、MSAL によって、Microsoft が認識している機関の一覧、または構成で指定した機関の一覧と比較されます。 詳細については、「[構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)」を参照してください

## <a name="switch-to-msal-api"></a>MSAL API に切り替える

ADAL Node のパブリック メソッドのほとんどには、MSAL Node に同等のものがあります。

| ADAL                                | MSAL                              | メモ                             |
|-------------------------------------|-----------------------------------|-----------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 名前が変更され、[アカウント](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) オブジェクトが必要になりました |
| `acquireTokenWithAuthorizationCode` | `acquireTokenByCode`              |                                   |
| `acquireTokenWithClientCredentials` | `acquireTokenByClientCredential` |                                   |
| `acquireTokenWithRefreshToken`      | `acquireTokenByRefreshToken`      | 有効な[更新トークン](#remove-logic-around-refresh-tokens)を移行する場合に便利です              |
| `acquireTokenWithDeviceCode`        | `acquireTokenByDeviceCode`        | ユーザー コードの取得を抽象化するようになりました (下記参照) |
| `acquireTokenWithUsernamePassword`  | `acquireTokenByUsernamePassword`  |                                   |

ただし、ADAL Node の一部のメソッドは非推奨とされ、その一方、MSAL Node には新しいメソッドが用意されています。

| ADAL                              | MSAL                            | メモ                             |
|-----------------------------------|---------------------------------|-----------------------------------|
| `acquireUserCode`                   | 該当なし                             | `acquireTokeByDeviceCode` とマージされました (上記参照)|
| 該当なし                               | `acquireTokenOnBehalfOf`          | [OBO フロー](./v2-oauth2-on-behalf-of-flow.md)を抽象化する新しいメソッド |
| `acquireTokenWithClientCertificate` | 該当なし                             | 初期化中に証明書が割り当てられるようになったため、不要になりました ([構成オプション](#configure-msal)に関するセクションを参照) |
| 該当なし                               | `getAuthCodeUrl`                  | [承認エンドポイント](./active-directory-v2-protocols.md#endpoints)の URL の構成を抽象化する新しいメソッド |

## <a name="use-scopes-instead-of-resources"></a>リソースの代わりにスコープを使用する

v1.0 と v2.0 のエンドポイントの重要な違いは、リソースへのアクセス方法に関するものです。 ADAL Node では、最初にアプリ登録ポータルにアクセス許可を登録してから、下に示すように、リソース (Microsoft Graph など) のアクセス トークンを要求します。

```javascript
authenticationContext.acquireTokenWithAuthorizationCode(
    req.query.code,
    redirectUri,
    resource, // e.g. 'https://graph.microsoft.com'
    clientId,
    clientSecret,
    function (err, response) {
        // do something with the authentication response
    }
);
```

MSAL Node では、**v1.0** と **v2.0** の両方のエンドポイントがサポートされています。 v2.0 エンドポイントは、"*スコープ中心*" モデルを使用してリソースにアクセスします。 したがって、リソースのアクセス トークンを要求するときは、そのリソースのスコープも指定する必要があります。

```javascript
const tokenRequest = {
    code: req.query.code,
    scopes: ["https://graph.microsoft.com/User.Read"],
    redirectUri: REDIRECT_URI,
};

pca.acquireTokenByCode(tokenRequest).then((response) => {
    // do something with the authentication response
}).catch((error) => {
    console.log(error);
});
```

スコープ中心モデルの利点の 1 つは、"*動的スコープ*" を使用できることです。 v1.0 を使用してアプリケーションを作成するときは、ユーザーがログイン時に同意するアプリケーションで必要なアクセス許可 ("*静的スコープ*" と呼ばれます) の完全なセットを登録する必要がありました。 v2.0 では、スコープ パラメーターを使用して、アクセス許可を必要なときに要求できます (この理由により、"*動的スコープ*")。 これによって、ユーザーはスコープに **増分同意** を与えることができます。 最初ユーザーにはアプリケーションへのサインインだけを行わせ、どのような種類のアクセスも必要としない場合、そうすることができます。 その後、ユーザーの予定表を読み取る機能が必要になった場合は、acquireToken メソッドで予定表のスコープを要求してユーザーの同意を得ることができます。 詳細については、「[リソースとスコープ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)」を参照してください

## <a name="use-promises-instead-of-callbacks"></a>コールバックの代わりに Promise を使用する

ADAL Node では、認証が成功し、応答が取得された後に、すべての操作にコールバックが使用されます。

```javascript
var context = new AuthenticationContext(authorityUrl, validateAuthority);

context.acquireTokenWithClientCredentials(resource, clientId, clientSecret, function(err, response) {
    if (err) {
        console.log(err);
    } else {
        // do something with the authentication response
    }
});
```

MSAL Node では、Promise が代わりに使用されます。

```javascript
    const cca = new msal.ConfidentialClientApplication(msalConfig);

    cca.acquireTokenByClientCredential(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

ES8 に付属する **async と await** 構文を使用することもできます。

```javascript
    try {
        const authResponse = await cca.acquireTokenByCode(tokenRequest);
    } catch (error) {
        console.log(error);
    }
```

## <a name="enable-logging"></a>ログの有効化

ADAL Node では、コード内の任意の場所でログを別途構成します。

```javascript
var adal = require('adal-node');

//PII or OII logging disabled. Default Logger does not capture any PII or OII.
adal.logging.setLoggingOptions({
  log: function (level, message, error) {
    console.log(message);

    if (error) {
        console.log(error);
    }
  },
  level: logging.LOGGING_LEVEL.VERBOSE, // provide the logging level
  loggingWithPII: false  // Determine if you want to log personal identification information. The default value is false.
});
```

MSAL Node では、ログは構成オプションの一部であり、MSAL Node インスタンスの初期化で作成されます。

```javascript
const msal = require('@azure/msal-node');

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

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

## <a name="enable-token-caching"></a>トークンのキャッシュの有効化

ADAL Node では、インメモリ トークン キャッシュをインポートするオプションがありました。 このトークン キャッシュは、`AuthenticationContext` オブジェクトを初期化するときにパラメーターとして使用されます。

```javascript
var MemoryCache = require('adal-node/lib/memory-cache');

var cache = new MemoryCache();
var authorityURI = "https://login.microsoftonline.com/common";

var context = new AuthenticationContext(authorityURI, true, cache);
```

MSAL Node は、既定ではインメモリ トークン キャッシュを使用します。 これは明示的にインポートする必要はありません。インメモリ トークン キャッシュは `ConfidentialClientApplication` および `PublicClientApplication` クラスの一部として公開されています。

```javascript
const msalTokenCache = publicClientApplication.getTokenCache();
```

重要なことは、ADAL Node を使用した以前のトークン キャッシュは、キャッシュ スキーマに互換性がないため、MSAL Node に転送できないということです。 ただし、MSAL Node で ADAL Node を使用して以前にアプリで取得した有効な更新トークンを使用することができます。 詳細については、[更新トークン](#remove-logic-around-refresh-tokens)のセクションを参照してください。

独自の **キャッシュ プラグイン** を用意して、ディスクにキャッシュを書き込むこともできます。 キャッシュ プラグインには、インターフェイス [ICachePlugin](https://azuread.github.io/microsoft-authentication-library-for-js/ref/interfaces/_azure_msal_common.icacheplugin.html) の実装が必要です。 ログと同様に、キャッシュは構成オプションの一部であり、MSAL Node インスタンスの初期化で作成されます。

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters
    },
    cache: {
        cachePlugin // your implementation of cache plugin
    },
    system: {
        // logging related options
    }
}

const msalInstance = new ConfidentialClientApplication(msalConfig);
```

キャッシュ プラグインの例は、下のように実装できます。

```javascript
const fs = require('fs');

// Call back APIs which automatically write and read into a .json file - example implementation
const beforeCacheAccess = async (cacheContext) => {
    cacheContext.tokenCache.deserialize(await fs.readFile(cachePath, "utf-8"));
};

const afterCacheAccess = async (cacheContext) => {
    if(cacheContext.cacheHasChanged) {
        await fs.writeFile(cachePath, cacheContext.tokenCache.serialize());
    }
};

// Cache Plugin
const cachePlugin = {
    beforeCacheAccess,
    afterCacheAccess
};
```

デスクトップ アプリなどの[パブリック クライアント アプリケーション](./msal-client-applications.md)を開発している場合は、[Node 用 Microsoft 認証拡張機能](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions)に、クライアント アプリケーションがクロスプラットフォーム トークン キャッシュのシリアル化と永続化を実行するための安全なメカニズムが用意されています。 サポートされているプラットフォームは、Windows、Mac、Linux です。

> [!NOTE]
> [Node 用 Microsoft 認証拡張機能](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions)は、スケーリングとパフォーマンスに関する問題を引き起こす可能性があるため、Web アプリケーションには推奨 **されません**。 代わりに、Web アプリではセッションでキャッシュを永続化することをお勧めします。

## <a name="remove-logic-around-refresh-tokens"></a>更新トークンに関するロジックを削除する

ADAL Node では、更新トークン (RT) が公開されました。これにより、これらのトークンをキャッシュし、`acquireTokenWithRefreshToken` メソッドを使用することで、そのトークンの使用に関するソリューションを開発できます。 RT が特に関連する一般的なシナリオは次のとおりです。

- ユーザーが接続されなくなったダッシュボードの更新などのアクションをユーザーの代わりに行う実行時間の長いサービス。
- クライアントが RT を Web サービスに渡せるようにする WebFarm シナリオ (キャッシュはサーバー側ではなく、クライアント側で行われます (暗号化された Cookie))。

MSAL Node とその他の MSAL は、セキュリティ上の理由により、更新トークンは公開されません。 代わりに、MSAL がトークンの更新を処理します。 そのため、これに関するロジックを構築する必要がなくなりました。 ただし、以前に取得した (有効な) 更新トークンを ADAL ノードのキャッシュから利用して、MSAL Node を含む新しいトークン セットを取得 **できます**。 これを行うために、MSAL Node では `acquireTokenByRefreshToken` が提供されています。これは、ADAL Node の `acquireTokenWithRefreshToken` メソッドに相当するものです。

```javascript
var msal = require('@azure/msal-node');

const config = {
    auth: {
        clientId: "ENTER_CLIENT_ID",
        authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
        clientSecret: "ENTER_CLIENT_SECRET"
    }
};

const cca = new msal.ConfidentialClientApplication(config);

const refreshTokenRequest = {
    refreshToken: "", // your previous refresh token here
    scopes: ["user.read"],
};

cca.acquireTokenByRefreshToken(refreshTokenRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

> [!NOTE]
> 上記で示されているように、MSAL Node の `acquireTokenByRefreshToken` メソッドを使用して新しいトークンのセットを取得するために現在も有効なトークンを使用した後は、古い ADAL Node トークン キャッシュを破棄することをお勧めします。

## <a name="handle-errors-and-exceptions"></a>エラーと例外を処理する

MSAL Node を使用している場合に、最も一般的な種類のエラーは、`interaction_required` エラーです。 多くの場合、このエラーは単に対話型トークン取得のプロンプトを開始するだけで解決されます。 たとえば、`acquireTokenSilent` を使用する場合に、キャッシュされた更新トークンがない場合、MSAL Node はアクセス トークンをサイレントで取得できません。 同様に、アクセスしようとしている Web API に[条件付きアクセス](../conditional-access/overview.md) ポリシーが設定されている場合があり、その場合、ユーザーは[多要素認証](../authentication/concept-mfa-howitworks.md) (MFA) を実行する必要があります。 そのような場合に、`acquireTokenByCode` をトリガーして `interaction_required` エラーを処理すると、ユーザーに MFA を要求するプロンプトが表示され、それを実行できるようになります。

発生する可能性のあるもう一つの一般的なエラーは `consent_required` です。これは、保護されたリソースのアクセス トークンを取得するために必要な権限がユーザーによって同意されていない場合に発生します。 `interaction_required` の場合と同様に、`consent_required` エラーのソリューションによって、`acquireTokenByCode` メソッドを使用した対話的なトークン取得のプロンプトが開始されることがよくあります。

## <a name="run-the-app"></a>アプリを実行する

変更が完了したら、アプリを実行し、認証シナリオをテストします。

```console
npm start
```

## <a name="example-acquiring-tokens-with-adal-node-vs-msal-node"></a>例: ADAL Node または MSAL Node を使用したトークンの取得

下のスニペットは、Express.js フレームワークの機密クライアント Web アプリを示しています。 これは、ユーザーが認証ルート `/auth` に達したときにサインインを実行し、`/redirect` ルートを介して Microsoft Graph のアクセス トークンを取得し、そのトークンの内容を表示します。


<table>
<tr><td> ADAL Node を使用する場合 </td><td> MSAL Node を使用する場合 </td></tr>
<tr>
<td>

```javascript
// Import dependencies
var express = require('express');
var crypto = require('crypto');
var adal = require('adal-node');

// Authentication parameters
var clientId = 'Enter_the_Application_Id_Here';
var clientSecret = 'Enter_the_Client_Secret_Here';
var tenant = 'common';
var authorityUrl = 'https://login.microsoftonline.com/' + tenant;
var redirectUri = 'http://localhost:3000/redirect';
var resource = 'https://graph.microsoft.com';

// Configure logging
adal.Logging.setLoggingOptions({
    log: function (level, message, error) {
        console.log(message);
    },
    level: adal.Logging.LOGGING_LEVEL.VERBOSE,
    loggingWithPII: false
});

// Auth code request URL template
var templateAuthzUrl = 'https://login.microsoftonline.com/'
    + tenant + '/oauth2/authorize?response_type=code&client_id='
    + clientId + '&redirect_uri=' + redirectUri
    + '&state=<state>&resource=' + resource;

// Initialize express
var app = express();

// State variable persists throughout the app lifetime
app.locals.state = "";

app.get('/auth', function(req, res) {

    // Create a random string to use against XSRF
    crypto.randomBytes(48, function(ex, buf) {
        app.locals.state = buf.toString('base64')
            .replace(/\//g, '_')
            .replace(/\+/g, '-');

        // Construct auth code request URL
        var authorizationUrl = templateAuthzUrl
            .replace('<state>', app.locals.state);

        res.redirect(authorizationUrl);
    });
});

app.get('/redirect', function(req, res) {
    // Compare state parameter against XSRF
    if (app.locals.state !== req.query.state) {
        res.send('error: state does not match');
    }

    // Initialize an AuthenticationContext object
    var authenticationContext =
        new adal.AuthenticationContext(authorityUrl);

    // Exchange auth code for tokens
    authenticationContext.acquireTokenWithAuthorizationCode(
        req.query.code,
        redirectUri,
        resource,
        clientId,
        clientSecret,
        function(err, response) {
            res.send(response);
        }
    );
});

app.listen(3000, function() {
    console.log(`listening on port 3000!`);
});
```

</td>
<td>

```javascript
// Import dependencies
const express = require("express");
const msal = require('@azure/msal-node');

// Authentication parameters
const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/common",
        clientSecret: "Enter_the_Client_Secret_Here"
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
};

const REDIRECT_URI = "http://localhost:3000/redirect";

// Initialize MSAL Node object using authentication parameters
const cca = new msal.ConfidentialClientApplication(config);

// Initialize express
const app = express();

app.get('/auth', (req, res) => {

    // Construct a request object for auth code
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Request auth code, then redirect
    cca.getAuthCodeUrl(authCodeUrlParameters)
        .then((response) => {
            res.redirect(response);
        }).catch((error) => res.send(error));
});

app.get('/redirect', (req, res) => {

    // Use the auth code in redirect request to construct
    // a token request object
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Exchange the auth code for tokens
    cca.acquireTokenByCode(tokenRequest)
        .then((response) => {
            res.send(response);
        }).catch((error) => res.status(500).send(error));
});

app.listen(3000, () =>
    console.log(`listening on port 3000!`));
```

</td>
</tr>
</table>

## <a name="next-steps"></a>次のステップ

- [MSAL Node API リファレンス](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html)
- [MSAL Node のコード サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples)
