---
title: JavaScript シングルページ アプリのチュートリアル 2.0 - Microsoft ID プラットフォーム | Azure
description: JavaScript SPA アプリケーションで、認証コード フローを使用して、Azure Active Directory v2.0 エンドポイントからのアクセス トークンを必要とする API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890377"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>ユーザーをサインインして、JavaScript シングルページ アプリケーション (SPA) から Microsoft Graph API を呼び出す - MSAL.js 2.0

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

このチュートリアルでは、PKCE を使用する OAuth 2.0 認証コード フローを利用するバージョンの MSAL.js を使用します。 このプロトコルの詳細や、暗黙的フローと認証コード フローの違いについては、[ドキュメント](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)を参照してください。 暗黙的フローを使用するチュートリアルをお探しの場合は、[MSAL.js v1 チュートリアル](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa)を参照してください。

このバージョンの MSAL.js は、現在の msal コア ライブラリで改善され、ブラウザーで認証コード フローを使用します。 古いライブラリで使用可能なほとんどの機能は、このバージョンで使用できますが、両方の認証フローには微妙な違いがあります。 このバージョンでは、暗黙的フローはサポートされて**いません**。

このガイドでは、JavaScript のシングルページ アプリケーション (SPA) で次のことを行う方法について説明します。
- 個人用アカウントと職場または学校アカウントへのサインイン
- アクセス トークンの取得
- *Microsoft ID プラットフォーム エンドポイント*のアクセス トークンを必要とする Microsoft Graph API などの API の呼び出し

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>詳細情報

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API、または Microsoft ID プラットフォーム エンドポイントのトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|JavaScript ブラウザー パッケージ用の Microsoft Authentication Library|

## <a name="set-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

代わりにこのサンプルのプロジェクトをダウンロードすることもできます。 Node.js などのローカル Web サーバーを使用してプロジェクトを実行するには、プロジェクト ファイルを複製します。

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

コード サンプルを実行する前に構成する場合は、[構成手順](#register-your-application)に進んでください。

## <a name="prerequisites"></a>前提条件

* このチュートリアルを実行するには、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) などのローカル Web サーバーが必要です。

* Node.js を使用してプロジェクトを実行する場合は、[Visual Studio Code](https://code.visualstudio.com/download) などの統合開発環境 (IDE) をインストールして、プロジェクト ファイルを編集します。

* このチュートリアルの手順は、Node.js に基づいています。

## <a name="create-your-project"></a>プロジェクトを作成する

[Node.js](https://nodejs.org/en/download/) がインストールされていることを確認し、アプリケーションをホストするフォルダーを作成します。 次に、`index.html` ファイルを提供する小規模な [Express](https://expressjs.com/) Web サーバーを実装します。

1. 最初に、ターミナル内のプロジェクト フォルダーに移動し、以下の NPM コマンドを実行します。
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. 次に、*server.js* という名前の .js ファイルを作成し、次のコードを追加します。

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

これで、SPA を提供する簡単なサーバーが完成しました。 このチュートリアルの最後に完成する予定のフォルダー構造は次のとおりです。

![完成予定の SPA フォルダー構造を表すテキスト](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>SPA UI を作成する

1. *app* フォルダーに、JavaScript SPA 用の *index.html* ファイルを作成します。 このファイルは、**Bootstrap 4 Framework** で作成された UI を実装し、構成、認証、および API 呼び出しのためのスクリプト ファイルをインポートします。

   *Index.html* ファイルに、次のコードを追加します。

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > 上記のスクリプトにある MSAL.js のバージョンを、[MSAL.js リリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)の最新のリリース バージョンに置き換えることができます。


2. ここで、DOM 要素にアクセスして更新する *ui.js* という名前の .js ファイルを作成し、次のコードを追加します。

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>アプリケーションの登録

[新しいシングル ページ アプリケーションを登録する](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)手順を実行します。

#### <a name="set-a-redirect-url-for-nodejs"></a>Node.js でリダイレクト URL を設定する

Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、ポート 3000 を使用しますが、使用可能なその他の任意のポートを使用できます。

アプリケーション登録情報の中にリダイレクト URL を設定するには、 **[アプリケーションの登録]** ペインに切り替え、新しい **SPA** を登録して、以下のいずれかを行います。

- **リダイレクト URL** として *`http://localhost:3000/`* を設定します。
- カスタム TCP ポートを使用している場合は、 *`http://localhost:<port>/`* を使用します (ここで、 *\<port>* はカスタム TCP ポート番号です)。

### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

*authConfig.js* という名前の新しい .js ファイルを作成します。このファイルには、認証用の構成パラメーターを記述することになります。次のコードを追加します。

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 各値の説明:
 - *\<Enter_the_Application_Id_Here>* は、登録したアプリケーションの**アプリケーション (クライアント) ID** です。
 - *\<Enter_the_Cloud_Instance_Id_Here>* は、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、単に「 *https://login.microsoftonline.com* 」と入力します。 **各国**のクラウド (中国など) の場合は、「[各国のクラウド](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)」を参照してください。
 - *\<Enter_the_Tenant_info_here>* は、次のオプションのいずれかに設定されます。
   - アプリケーションで "*この組織のディレクトリ内のアカウントのみ*" がサポートされる場合は、この値を**テナント ID** または**テナント名** (例: *contoso.microsoft.com*) に置き換えます。
   - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。
   - アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。
- *\Enter_the_Redirect_Uri_Here>* は、ポータルで登録したポートです ( *`http://localhost:3000/`* )。


`graphConfig.js` という名前の新しい .js ファイルを作成します。このファイルには、Microsoft Graph API を呼び出す構成パラメーターを記述することになります。次のコードを追加します。
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- *\<Enter_the_Graph_Endpoint_Here>* は、MS Graph API のインスタンスです。 グローバル MS Graph API エンドポイントの場合は、この文字列を `https://graph.microsoft.com` に置き換えるだけでかまいません。 国内クラウド デプロイの場合は、[Graph API のドキュメント](https://docs.microsoft.com/graph/deployments)を参照してください。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

### <a name="popup"></a>ポップアップ
`authPopup.js` という名前の新しい .js ファイルを作成します。このファイルには、ログイン ポップアップ用の認証とトークン取得のロジックを記述することになります。次のコードを追加します。

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>リダイレクト
`authRedirect.js` という名前の新しい .js ファイルを作成します。このファイルには、ログイン リダイレクト用の認証とトークン取得のロジックを記述することになります。次のコードを追加します。

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>詳細情報

ユーザーが初めて **[Sign In]** ボタンを選択すると、`signIn` メソッドによって、ユーザーがサインインするための `loginPopup` が呼び出されます。 このメソッドによって、"*Microsoft ID プラットフォーム エンドポイント*" のポップアップ ウィンドウが開き、ユーザーの資格情報が要求されて検証が行われます。 サインインに成功すると、*msal.js* によって[認証コード フロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)が開始されます。

この時点で、PKCE で保護された認証コードが CORS によって保護されたトークン エンドポイントに送信され、トークンと交換されます。 ID トークン、アクセス トークン、および更新トークンが受信されて *msal.js* によって処理され、トークンに含まれる情報がキャッシュされます。

ID トークンには、表示名など、ユーザーについての基本的な情報が含まれています。 このトークンによって提供されるデータを使用する予定がある場合は、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、このトークンがバックエンド サーバーで検証される必要があります。 更新トークンの有効期間は限られており、24 時間後に有効期限が切れます。 更新トークンは、新しいアクセス トークンを自動的に取得するために使用できます。

このガイドで生成する SPA は、ユーザー プロファイル情報のため、`acquireTokenSilent`、`acquireTokenPopup`、またはその両方を呼び出して、Microsoft Graph API の照会に使用される*アクセス トークン*を取得します。 ID トークンを検証するサンプルが必要な場合は、GitHub の [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) サンプル アプリケーションを参照してください。 このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後は、ユーザーがリソースにアクセスするためのトークンを要求するたびに再認証を求めるべきではありません。 このような再認証要求を防ぐには、`acquireTokenSilent` を使用します。 ただし、ユーザーに Microsoft ID プラットフォーム エンドポイントとのやり取りを強制しなければならない場合があります。 次に例を示します。

- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
- アプリケーションがリソースへのアクセスを要求し、ユーザーの同意が必要である。
- 2 要素認証が必須である。

`acquireTokenPopup` を呼び出すと、ポップアップ ウィンドウが表示されます (または、`acquireTokenRedirect` によってユーザーが Microsoft ID プラットフォーム エンドポイントにリダイレクトされます)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginPopup` (または `loginRedirect`) を実行した後、後続の呼び出しでは、通常、`acquireTokenSilent` メソッドを使用して、保護されたリソースにアクセスするためのトークンを取得します  (トークンを要求または更新するための呼び出しは自動的に行われます)。場合によっては、`acquireTokenSilent` が失敗することがあります。 たとえば、ユーザーのパスワードの期限が切れている場合です。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1. すぐに `acquireTokenPopup` を呼び出し、ユーザー サインイン プロンプトをトリガーします。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。

1. ユーザーが適切なタイミングでサインインできるようにするか、アプリケーションが後で `acquireTokenSilent` を再試行できるように、ユーザーに対して対話形式でのサインインが必要であることを視覚的に示します。 他に中断なく使用できる機能がアプリケーションにある場合は、一般に、この方法が使用されます。 たとえば、使用可能な非認証コンテンツがアプリケーションに含まれている場合が考えられます。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

> [!NOTE]
> このクイックスタートでは、既定では `loginPopup` メソッドと `acquireTokenPopup` メソッドを使用します。 ブラウザーとして Internet Explorer を使用している場合は、`loginRedirect` メソッドと `acquireTokenRedirect` メソッドを使用することをお勧めします。Internet Explorer には、ポップアップ ウィンドウの処理に関して[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるためです。 リダイレクト メソッドを使用して同じ結果を得る方法については、[*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) を参照してください。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>取得したトークンを使用して Microsoft Graph API を呼び出す


 *graph.js* という名前の .js ファイルを作成します。このファイルには、Microsoft Graph API の REST 呼び出しを記述することになります。次のコードを追加します。

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成するサンプル アプリケーションでは、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー*に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

## <a name="test-your-code"></a>コードのテスト

1. Node.js では、アプリケーション フォルダーから次のコマンドを実行することで、Web サーバーを起動します。

   ```bash
   npm install
   npm start
   ```
1. ブラウザーに「 **http://localhost:3000** 」または「 **http://localhost:{port}** 」と入力します。ここで、*port* は、Web サーバーがリッスンしているポートです。 *index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーに *index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォーム エンドポイントにサインインするように求められます。

![JavaScript SPA アカウント サインイン ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスを許可してサインインすることを求められます。

![[Permissions requested]\(アクセス許可が要求されています\) ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインすると、自分のユーザー プロファイル情報が Microsoft Graph API の応答で返されて表示されます。

![Microsoft Graph API 呼び出しの結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 既定では、このスコープは、Azure portal に登録されているすべてのアプリケーションに自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API は、ユーザーのメールを一覧表示するには *Mail.Read* スコープが必要です。

> [!NOTE]
> スコープを追加すると、ユーザーは追加の同意を求められることがあります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出しでスコープとして *clientId* を使用できます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

[MSAL.js GitHub リポジトリ](https://github.com/AzureAD/microsoft-authentication-library-for-js)には、追加のライブラリ ドキュメント、FAQ、および問題のサポートが含まれています。
