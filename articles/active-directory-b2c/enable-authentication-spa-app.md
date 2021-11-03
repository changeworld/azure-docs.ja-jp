---
title: Azure Active Directory B2C の構成要素を使用して SPA アプリケーションで認証を有効にする
description: この記事では、SPA アプリケーションでユーザーのサインインとサインアップを行うための Azure Active Directory B2C の構成要素について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: a42d8f356286e2936ca9d6bdd15151bbf9ef1064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007336"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して独自のシングルページ アプリケーションで認証を有効にする

この記事では、独自のシングルページ アプリケーション (SPA) に Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 [JavaScript 用の Microsoft Authentication Library (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用して SPA アプリケーションを作成する方法について説明します。 

この記事は、[サンプル SPA アプリケーションでの認証の構成](./configure-authentication-sample-spa-app.md)に関する記事と共に使用し、サンプルの SPA アプリを独自の SPA アプリに置き換えてください。

## <a name="overview"></a>概要

この記事では、Node.js と [Express](https://expressjs.com/) を使用して、基本的な Node.js Web アプリを作成します。 Express は、Web およびモバイル アプリケーション用の一連の機能を提供する、最小限の柔軟な Node.js Web アプリ フレームワークです。

[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 認証ライブラリは、SPA アプリへの認証と認可のサポートの追加を簡略化する、Microsoft 提供のライブラリです。

> [!TIP]
> MSAL.js コード全体がクライアント側で実行されます。 Node.js と Express のサーバー側のコードは、.NET Core、Java、Hypertext Preprocessor (PHP) スクリプト言語などの他のソリューションに置き換えることができます。

## <a name="prerequisites"></a>前提条件

前提条件と統合の手順を確認するには、[サンプル SPA アプリケーションでの認証の構成](configure-authentication-sample-spa-app.md)に関する記事を参照してください。

## <a name="step-1-create-an-spa-app-project"></a>ステップ 1: SPA アプリ プロジェクトを作成する

既存の SPA アプリ プロジェクトを使用するか、新しく作成することができます。 新しいプロジェクトを作成するには、次のようにします。

1. コマンド シェルを開き、新しいディレクトリ (*myApp* など) を作成します。 このディレクトリには、アプリのコード、ユーザー インターフェイス、構成ファイルが格納されます。

1. 作成したディレクトリに移動します。

1. `npm init` コマンドを使用して、アプリ用の `package.json` ファイルを作成します。 このコマンドでは、アプリに関する情報の入力を求められます (アプリの名前とバージョン、最初のエントリ ポイントの名前、*index.js* ファイルなど)。 次のコマンドを実行し、既定の値をそのまま使用します。

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>ステップ 2: 依存関係をインストールする

Express パッケージをインストールするには、コマンド シェルで次のコマンドを実行します。

```
npm install express
```

アプリの静的ファイルを探すため、サーバー側コードでは [Path](https://www.npmjs.com/package/path) パッケージが使用されます。 

Path パッケージをインストールするには、コマンド シェルで次のコマンドを実行します。

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>ステップ 3: Web サーバーを構成する

*myApp* フォルダーに、次のコードが含まれる *index.js* という名前のファイルを作成します。

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>ステップ 4: SPA のユーザー インターフェイスを作成する

SAP アプリの `index.html` ファイルを追加します。 このファイルにはブートストラップ フレームワークで構築されたユーザー インターフェイスが実装されており、構成、認証、Web API の呼び出しのためのスクリプト ファイルがインポートされます。

次の表では、*index.html* ファイルによって参照されるリソースについて詳しく説明します。 

|関連項目 |定義|
|---|---|
|MSAL.js ライブラリ| MSAL.js 認証 JavaScript ライブラリの [CDN パス](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)。|
|[Bootstrap スタイルシート](https://getbootstrap.com/) | 高速で簡単な Web 開発用の無料のフロントエンド フレームワーク。 フレームワークには、HTML ベースと CSS ベースのデザイン テンプレートが含まれています。 |
|[`policies.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js) | Azure AD B2C のカスタム ポリシーとユーザー フローが含まれます。 |
|[`authConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js) | 認証構成パラメーターが含まれます。|
|[`authRedirect.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | 認証ロジックが含まれます。 |
|[`apiConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | Web API スコープと API エンドポイントの場所が含まれます。 |
|[`api.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | API を呼び出してその応答を処理するために使用するメソッドが定義されています。|
|[`ui.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | UI 要素を制御します。 |
| | |

SPA の index ファイルを提供するには、*myApp* フォルダーに、次の HTML スニペットが含まれる *index.html* という名前のファイルを作成します。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>ステップ 5: 認証ライブラリを構成する

MSAL.js ライブラリと Azure AD B2C の統合方法を構成します。 MSAL.js ライブラリは、共通の構成オブジェクトを使用して、Azure AD B2C テナントの認証エンドポイントに接続します。

認証ライブラリを構成するには、次のようにします。

1. *myApp* フォルダーに、*App* という名前の新しいフォルダーを作成します。 
1. *App* フォルダー内に、*authConfig.js* という名前の新しいファイルを作成します。
1. *authConfig.js* ファイルに、次の JavaScript コードを追加します。

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. `<Application-ID>` は、アプリの登録のアプリケーション ID に置き換えます。 詳細については、[サンプル SPA アプリケーションでの認証の構成](./configure-authentication-sample-spa-app.md#step-23-register-the-spa)に関する記事を参照してください。

> [!TIP]
> MSAL オブジェクトの他の構成オプションについては、[認証オプション](./enable-authentication-spa-app-options.md)に関する記事を参照してください。

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>ステップ 6: Azure AD B2C のユーザー フローを指定する

Azure AD B2C 環境に関する情報を提供する *policies.js* ファイルを作成します。 MSAL.js ライブラリにより、この情報を使用して、Azure AD B2C への認証要求が作成されます。

Azure AD B2C のユーザー フローを指定するには、次のようにします。

1. *App* フォルダー内に、*policies.js* という名前の新しいファイルを作成します。
1. 次のコードを *policies.js* ファイルに追加します。

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. `B2C_1_SUSI` を、サインインの Azure AD B2C ポリシー名に置き換えます。
1. `B2C_1_EditProfile` を、編集プロファイルの Azure AD B2C ポリシー名に置き換えます。
1. `contoso` のすべてのインスタンスを、[Azure AD B2C のテナント名](./tenant-management.md#get-your-tenant-name)に置き換えます。

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>ステップ 7: MSAL を使用してユーザーをサインインさせる

このステップでは、サインイン フロー、API アクセス トークンの取得、サインアウト メソッドを初期化するためのメソッドを実装します。 

詳細については、[MSAL の PublicClientApplication クラスのリファレンス](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html)に関する記事および「[ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user)」を参照してください。

ユーザーをサインインさせるには、次のようにします。

1. *App* フォルダー内に、*authRedirect.js* という名前の新しいファイルを作成します。
1. 次のコードをコピーして、*authRedirect.js* に貼り付けます。

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>ステップ 8: Web API の場所とスコープを構成する

SPA アプリで Web API を呼び出せるようにするには、Web API へのアクセスを承認するために使用する、Web API エンドポイントの場所と[スコープ](./configure-authentication-sample-spa-app.md#app-registration-overview)を指定します。

Web API の場所とスコープを構成するには、次のようにします。

1. *App* フォルダー内に、*apiConfig.js* という名前の新しいファイルを作成します。
1. 次のコードをコピーして、*apiConfig.js* に貼り付けます。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. `contoso` を、実際のテナント名に置き換えます。 必要なスコープ名は、[スコープの構成](./configure-authentication-sample-spa-app.md#step-22-configure-scopes)に関する記事で説明されているようにして見つけることができます。
1. `webApi` の値を、WEB API エンドポイントの場所に置き換えます。

## <a name="step-9-call-your-web-api"></a>ステップ 9: Web API を呼び出す

API エンドポイントへの HTTP 要求を定義します。 *MSAL.js* で取得されたアクセス トークンを要求の `Authorization` HTTP ヘッダーに渡すように、HTTP 要求を構成します。

次のコードでは、`Authorization` HTTP ヘッダーでアクセス トークンを渡す、API エンドポイントへの HTTP `GET` 要求が定義されます。 API の場所は、*apiConfig.js* の `webApi` キーによって定義されています。 

取得したトークンを使用して Web API を呼び出すには、次のようにします。

1. *App* フォルダー内に、*api.js* という名前の新しいファイルを作成します。
1. 次のコードを *api.js* ファイルに追加します。

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>ステップ 10: UI 要素の参照を追加する

SPA アプリでは、UI 要素を制御するために JavaScript が使用されます。 たとえば、サインインとサインアウトのボタンが表示されたり、ユーザー ID トークン クレームが画面にレンダリングされたりします。

UI 要素の参照を追加するには、次のようにします。

1. *App* フォルダー内に、*ui.js* という名前のファイルを作成します。
1. 次のコードを *ui.js* ファイルに追加します。

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>ステップ 11: SPA アプリケーションを実行する

コマンド シェルで、次のコマンドを実行します。

``` powershell
npm install  
npm ./index.js
```

1. https://localhost:6420 に移動します。 
1. **[サインイン]** を選択します。
1. サインインアップまたはサインイン プロセスを完了します。

認証が正常に完了すると、解析された ID トークンが画面に表示されます。 [`Call API`] を選択して、API エンドポイントを呼び出します。

## <a name="next-steps"></a>次のステップ

* [コード サンプル](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)についてさらに確認する。
* [Azure AD B2C を使用した独自の SPA アプリケーションで認証オプション](enable-authentication-spa-app-options.md)を構成する。
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)。
