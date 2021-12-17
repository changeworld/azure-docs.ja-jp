---
title: 'チュートリアル: 認証コード フローを使用する React シングルページ アプリを作成する | Azure'
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインを処理すると共に、認証コード フローを使用して Microsoft ID プラットフォームからアクセス トークンを取得し、Microsoft Graph API を呼び出すことができる React SPA を作成します。
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/16/2021
ms.author: jamesmantu
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a42f4e5cd0b58c2078a4ba498b262efb804edcf8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050282"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-react-single-page-app-spa-using-auth-code-flow"></a>チュートリアル: 認証コード フローを使用して、ユーザーをサインインさせて React シングルページ アプリ (SPA) から Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを行い、PKCE による承認コード フローを使用して Microsoft Graph を呼び出す React シングルページ アプリケーション (SPA) を構築します。 構築する SPA では、React 用の Microsoft Authentication Library (MSAL) を使用します。

このチュートリアルでは、次の作業を行います。
> [!div class="checklist"]
> * `npm` を使用して React プロジェクトを作成する
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

MSAL React では、暗黙的な許可のフローではなくブラウザーでの承認コード フローがサポートされています。 MSAL React では、暗黙的フローはサポートされて **いません**。

## <a name="prerequisites"></a>前提条件

* ローカル Web サーバーを実行するための [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

## <a name="how-the-tutorial-app-works"></a>チュートリアル アプリの動作

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="シングルページ アプリケーションでの認証コード フローを示す図":::

このチュートリアルで作成するアプリケーションにより、React SPA では、Microsoft ID プラットフォームからセキュリティ トークンを取得して、Microsoft Graph API に対してクエリを実行できるようになります。 React 用の Microsoft Authentication Library (MSAL)、MSAL.js v2 ライブラリのラッパーを使用します。 MSAL React では、React (16 以降) のアプリケーションで Azure Active Directory (Azure AD) を使用してエンタープライズ ユーザーを認証できるほか、Microsoft アカウントを持つユーザーとソーシャル ID (Facebook、Google、LinkedIn など) を認証することができます。 また、アプリケーションは、このライブラリを通じて、Microsoft クラウド サービスや Microsoft Graph にアクセスすることができます。

このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、HTTP 要求の Authorization ヘッダーに追加されます。 トークンの取得と更新は、React 用の Microsoft Authentication Library (MSAL React) によって処理されます。

### <a name="libraries"></a>ライブラリ

このチュートリアルでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)|JavaScript React Wrapper 用の Microsoft Authentication Library|
|[MSAL ブラウザー](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|JavaScript v2 ブラウザー パッケージ用の Microsoft Authentication Library|

## <a name="get-the-completed-code-sample"></a>完成したコード サンプルを入手する

代わりに、このチュートリアルの完成したサンプル プロジェクトをダウンロードすることもできます。 Node.js などのローカル Web サーバーを使用してプロジェクトを実行するには、[ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) リポジトリをクローンします。

`git clone https://github.com/Azure-Samples/ms-identity-javascript-react-spa`

その後、コード サンプルを実行前に構成するために、[構成手順](#register-your-application)に進みます。

チュートリアルを続行してアプリケーションを自分でビルドする場合は、次のセクション「[前提条件](#prerequisites)」に進みます。

## <a name="create-your-project"></a>プロジェクトを作成する

[Node.js](https://nodejs.org/en/download/) をインストールしたら、ターミナル ウィンドウを開いて次のコマンドを実行します。

```console
npx create-react-app msal-react-tutorial # Create a new React app
cd msal-react-tutorial # Change to the app directory
npm install @azure/msal-browser @azure/msal-react # Install the MSAL packages
npm install react-bootstrap bootstrap # Install Bootstrap for styling
```

[create-react-app](https://create-react-app.dev/docs/getting-started) を使用して、小型の React プロジェクトをブートストラップできました。 これが、このチュートリアルの残りの作業を進めるうえでの出発点になります。 このチュートリアルの作業を進めていった場合のアプリの変更を見たいときは、次のコマンドを実行できます。 

```console
npm start
```

ブラウザー ウィンドウが開いて自動的にアプリに移動するはずです。 そうならない場合は、ブラウザーを開いて http://localhost:3000 に移動します。 更新したコードでファイルを保存するたびに、ページが再度読み込まれて変更が反映されます。

## <a name="register-your-application"></a>アプリケーションを登録する

「[シングルページ アプリケーション: アプリの登録](./scenario-spa-app-registration.md)」の手順に従って、Azure portal を使用して SPA のアプリ登録を作成します。

「[リダイレクト URI: 承認コード フローを使用した MSAL.js 2.0](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)」の手順で、「`http://localhost:3000`」(create-react-app によってアプリケーションが生成される既定の場所) を入力します。

### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

1. *src* フォルダー内に *authConfig.js* という名前のファイルを作成し、認証用の構成パラメーターを記述します。さらに、次のコードを追加します。

    ```javascript
    export const msalConfig = {
      auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here", // This is a URL (e.g. https://login.microsoftonline.com/{your tenant ID})
        redirectUri: "Enter_the_Redirect_Uri_Here",
      },
      cache: {
        cacheLocation: "sessionStorage", // This configures where your cache will be stored
        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      }
    };
    
    // Add scopes here for ID token to be used at Microsoft identity platform endpoints.
    export const loginRequest = {
     scopes: ["User.Read"]
    };
    
    // Add the endpoints here for Microsoft Graph API services you'd like to use.
    export const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me"
    };
    ```

1. 以下の説明に従って、`msalConfig` セクションの値を変更します。
    
    |値の名前| 概要|
    |----------|------|
    |`Enter_the_Application_Id_Here`| 登録したアプリケーションの **アプリケーション (クライアント) ID**。|
    |`Enter_the_Cloud_Instance_Id_Here`| アプリケーションが登録されている Azure クラウド インスタンス。 メイン ("*グローバル*") Azure クラウドの場合は、「`https://login.microsoftonline.com`」と入力します。 **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」に適切な値が記載されています。
    |`Enter_the_Tenant_Info_Here`| 次のいずれかのオプションに設定します。1) お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値をディレクトリ (テナント) ID またはテナント名 (例: "**contoso.microsoft.com**") に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。 |
    |`Enter_the_Redirect_Uri_Here`|**http://localhost:3000** に置き換えます。|
    |`Enter_the_Graph_Endpoint_Here`| アプリケーションが通信する必要がある、Microsoft Graph API のインスタンス。 **グローバル** Microsoft Graph API エンドポイントの場合は、この文字列の両方のインスタンスを `https://graph.microsoft.com` に置き換えます。 **各国** のクラウドのデプロイにおけるエンドポイントの場合は、Microsoft Graph のドキュメントで「[各国のクラウドでのデプロイ](/graph/deployments)」を参照してください。|
    
    使用できる構成オプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

1. *src/index.js* ファイルを開いて、次の import を追加します。

    ```javascript
    import "bootstrap/dist/css/bootstrap.min.css";
    import { PublicClientApplication } from "@azure/msal-browser";
    import { MsalProvider } from "@azure/msal-react";
    import { msalConfig } from "./authConfig";
    ```

1. 手順 1 の構成を使用して、*src/index.js* の import の下に `PublicClientApplication` インスタンスを作成します。

    ```javascript
    const msalInstance = new PublicClientApplication(msalConfig);
    ``` 

1. *src/index.js* にある `<App />` コンポーネントを見つけて、`MsalProvider` コンポーネントでそれをラップします。 render 関数が次のようになります。

    ```jsx
    ReactDOM.render(
        <React.StrictMode>
            <MsalProvider instance={msalInstance}>
                <App />
            </MsalProvider>
        </React.StrictMode>,
        document.getElementById("root")
    );
    ``` 


## <a name="sign-in-users"></a>ユーザーのサインイン

*src* に *components* というフォルダーを作成し、そのフォルダー内に *SignInButton.jsx* という名前のファイルを作成します。 次のいずれかのセクションからコードを追加し、ポップアップ ウィンドウまたはフルフレーム リダイレクトを使用したログインを呼び出します。

### <a name="sign-in-using-pop-ups"></a>ポップアップを使用したサインイン

選択時にポップアップ ログインを呼び出すボタン コンポーネントを作成するには、次のコードを *src/components/SignInButton.jsx* に追加します。

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginPopup(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for login
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Popup</Button>
    );
}
```

### <a name="sign-in-using-redirects"></a>リダイレクトを使用したサインイン

選択時にリダイレクト ログインを呼び出すボタン コンポーネントを作成するには、次のコードを *src/components/SignInButton.jsx* に追加します。

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginRedirect(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the login prompt
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Redirect</Button>
    );
}
```

### <a name="add-the-sign-in-button"></a>サインイン ボタンの追加

1. *components* フォルダーに *PageLayout.jsx* という名前の別のフォルダーを作成し、次のコードを追加して、作成したばかりのサインイン ボタンが含まれる navbar コンポーネントを作成します。

    ```jsx
    import React from "react";
    import Navbar from "react-bootstrap/Navbar";
    import { useIsAuthenticated } from "@azure/msal-react";
    import { SignInButton } from "./SignInButton";
    
    /**
     * Renders the navbar component with a sign-in button if a user is not authenticated
     */
    export const PageLayout = (props) => {
        const isAuthenticated = useIsAuthenticated();
    
        return (
            <>
                <Navbar bg="primary" variant="dark">
                    <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                    { isAuthenticated ? <span>Signed In</span> : <SignInButton /> }
                </Navbar>
                <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
                <br />
                <br />
                {props.children}
            </>
        );
    };
    ```

2. ここで *src/App.js* を開いて、既存の内容を次のコードに置き換えます。 

    ```jsx
    import React from "react";
    import { PageLayout } from "./components/PageLayout";
    
    function App() {
      return (
          <PageLayout>
              <p>This is the main app content!</p>
          </PageLayout>
      );
    }
    
    export default App;
    ```

これで、未認証のユーザーにのみ表示されるサインイン ボタンがアプリに追加されました。

**[Sign in using Popup]** または **[Sign in using Redirect]** のボタンをユーザーが初めて選択するとき、`onClick` ハンドラーによって `loginPopup` (または `loginRedirect`) が呼び出され、ユーザーのサインインが行われます。 `loginPopup` メソッドによって、"*Microsoft ID プラットフォーム エンドポイント*" でポップアップ ウィンドウが開き、ユーザーの資格情報が要求されて検証が行われます。 サインインに成功すると、*msal.js* によって [認証コード フロー](v2-oauth2-auth-code-flow.md)が開始されます。

この時点で、PKCE で保護された認証コードが CORS によって保護されたトークン エンドポイントに送信され、トークンと交換されます。 アプリケーションによって ID トークン、アクセス トークン、および更新トークンが受信され、*msal.js* によって処理されて、トークンに含まれる情報がキャッシュされます。

## <a name="sign-users-out"></a>ユーザーのサインアウト

*src/components* に *SignOutButton.jsx* という名前のファイルを作成します。 次のいずれかのセクションからコードを追加し、ポップアップ ウィンドウまたはフルフレーム リダイレクトを使用したログアウトを呼び出します。

### <a name="sign-out-using-pop-ups"></a>ポップアップを使用したサインアウト

選択時にポップアップ ログアウトを呼び出すボタン コンポーネントを作成するには、次のコードを *src/components/SignOutButton.jsx* に追加します。

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutPopup().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for logout
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Popup</Button>
    );
}
```

### <a name="sign-out-using-redirects"></a>リダイレクトを使用したサインアウト

選択時にリダイレクト ログアウトを呼び出すボタン コンポーネントを作成するには、次のコードを *src/components/SignOutButton.jsx* に追加します。

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutRedirect().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the logout prompt
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Redirect</Button>
    );
}
```

### <a name="add-the-sign-out-button"></a>サインアウト ボタンの追加

認証済みのユーザーに新しい `SignOutButton` コンポーネントが表示されるように、*src/components/PageLayout.jsx* の `PageLayout` コンポーネントを更新します。 コードは次のようになります。

```jsx
import React from "react";
import Navbar from "react-bootstrap/Navbar";
import { useIsAuthenticated } from "@azure/msal-react";
import { SignInButton } from "./SignInButton";
import { SignOutButton } from "./SignOutButton";

/**
 * Renders the navbar component with a sign-in button if a user is not authenticated
 */
export const PageLayout = (props) => {
    const isAuthenticated = useIsAuthenticated();

    return (
        <>
            <Navbar bg="primary" variant="dark">
                <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                { isAuthenticated ? <SignOutButton /> : <SignInButton /> }
            </Navbar>
            <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
            <br />
            <br />
            {props.children}
        </>
    );
};
```

## <a name="conditionally-render-components"></a>コンポーネントの条件付き表示

認証済みまたは未認証のユーザーにのみ特定のコンポーネントを表示するには、以下に示すとおり `AuthenticateTemplate`、`UnauthenticatedTemplate`、またはその両方を使用します。

1. *src/App.js* に次の import を追加します。

    ```javascript
    import { AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";
    ```
    
1. 特定のコンポーネントを認証済みのユーザーにのみ表示するには、*src/App.js* の `App` 関数を次のコードで更新します。 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

1. 特定のコンポーネント (ログインの提案など) を未認証のユーザーにのみ表示するには、*src/App.js* の `App` 関数を次のコードで更新します。 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
                <UnauthenticatedTemplate>
                    <p>You are not signed in! Please sign in.</p>
                </UnauthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

## <a name="acquire-a-token"></a>トークンを取得する

1. Microsoft Graph などの API を呼び出す前に、アクセス トークンを取得する必要があります。 次のコードで、`ProfileContent` という新しいコンポーネントを *src/App.js* に追加します。

    ```jsx
    function ProfileContent() {
        const { instance, accounts, inProgress } = useMsal();
        const [accessToken, setAccessToken] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestAccessToken() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                setAccessToken(response.accessToken);
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    setAccessToken(response.accessToken);
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {accessToken ? 
                    <p>Access Token Acquired!</p>
                    :
                    <Button variant="secondary" onClick={RequestAccessToken}>Request Access Token</Button>
                }
            </>
        );
    };
    ```

1. *src/App.js* の import を次のとおりに更新します。

    ```js
    import React, { useState } from "react";
    import { PageLayout } from "./components/PageLayout";
    import { AuthenticatedTemplate, UnauthenticatedTemplate, useMsal } from "@azure/msal-react";
    import { loginRequest } from "./authConfig";
    import Button from "react-bootstrap/Button";
    ```

1. さらに、*src/App.js* で `App` コンポーネントの `AuthenticatedTemplate` の子として新しい `ProfileContent` コンポーネントを追加します。 `App` コンポーネントは次のようになります。

    ```javascript
    function App() {
      return (
          <PageLayout>
              <AuthenticatedTemplate>
                  <ProfileContent />
              </AuthenticatedTemplate>
              <UnauthenticatedTemplate>
                  <p>You are not signed in! Please sign in.</p>
              </UnauthenticatedTemplate>
          </PageLayout>
      );
    }
    ```

上記のコードにより、サインイン済みのユーザーにボタンが表示され、ボタンの選択時に Microsoft Graph のアクセス トークンを要求できるようになります。

ユーザーがサインインした後は、保護されたリソースへのアクセスが必要になるたびに、アプリがユーザーに再認証を行うように要求する (つまり、トークンを要求する) べきではありません。 そのような再認証の要求を防ぐには、`acquireTokenSilent` を呼び出します。これは、キャッシュされている期限切れでないアクセス トークンを最初に検索し、必要な場合に更新トークンを使用して新しいアクセス トークンを取得します。 ただし、ユーザーに Microsoft ID プラットフォームとのやり取りを強制しなければならない場合があります。 次に例を示します。

- セッションの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
- 更新トークンの有効期限が切れている。
- アプリケーションがリソースへのアクセスを要求し、ユーザーの同意が必要である。
- 2 要素認証が必須である。

`acquireTokenPopup` を呼び出すと、ポップアップ ウィンドウが表示されます (または、`acquireTokenRedirect` によってユーザーが Microsoft ID プラットフォームにリダイレクトされます)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

> [!NOTE]
> Internet Explorer を使用している場合は、Internet Explorer とポップアップ ウィンドウに[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)があるため、`loginRedirect` および `acquireTokenRedirect` メソッドを使用することをお勧めします。

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す

1. *src* フォルダーに *graph.js* という名前のファイルを作成し、Microsoft Graph API への REST 呼び出しを行うために、次のコードを追加します。

    ```javascript
    import { graphConfig } from "./authConfig";
    
    /**
     * Attaches a given access token to a Microsoft Graph API call. Returns information about the user
     */
    export async function callMsGraph(accessToken) {
        const headers = new Headers();
        const bearer = `Bearer ${accessToken}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        return fetch(graphConfig.graphMeEndpoint, options)
            .then(response => response.json())
            .catch(error => console.log(error));
    }
    ```

1. 次に、*src/components* に *ProfileData.jsx* という名前のファイルを作成し、次のコードを追加します。

    ```javascript
    import React from "react";
    
    /**
     * Renders information about the user obtained from Microsoft Graph
     */
    export const ProfileData = (props) => {
        return (
            <div id="profile-div">
                <p><strong>First Name: </strong> {props.graphData.givenName}</p>
                <p><strong>Last Name: </strong> {props.graphData.surname}</p>
                <p><strong>Email: </strong> {props.graphData.userPrincipalName}</p>
                <p><strong>Id: </strong> {props.graphData.id}</p>
            </div>
        );
    };
    ```

1. 次に、*src/App.js* を開いて、それらを import に追加します。
    
    ```javascript
    import { ProfileData } from "./components/ProfileData";
    import { callMsGraph } from "./graph";
    ```

1. さらに、*src/App.js* の `ProfileContent` コンポーネントを更新して、Microsoft Graph を呼び出してトークンの取得後にプロファイル データを表示するようにします。 `ProfileContent` コンポーネントは次のようになります。

    ```javascript
    function ProfileContent() {
        const { instance, accounts } = useMsal();
        const [graphData, setGraphData] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestProfileData() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                callMsGraph(response.accessToken).then(response => setGraphData(response));
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    callMsGraph(response.accessToken).then(response => setGraphData(response));
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {graphData ? 
                    <ProfileData graphData={graphData} />
                    :
                    <Button variant="secondary" onClick={RequestProfileData}>Request Profile Information</Button>
                }
            </>
        );
    };
    ```

上記で行った変更では、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このチュートリアルで作成したサンプル アプリケーションでは、保護されているリソースは、サインイン ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

## <a name="test-your-application"></a>アプリケーションのテスト

これでアプリケーションの作成が完了し、Web サーバーを起動して、アプリの機能をテストする準備ができました。

1. プロジェクト フォルダーのルート内から次のコマンドを実行して、アプリを起動します。

   ```console
   npm start
   ```
1. ブラウザー ウィンドウが開いて自動的にアプリに移動するはずです。 そうならない場合は、ブラウザーを開いて `http://localhost:3000` に移動します。 次のようなページが表示されます。

    :::image type="content" source="media/tutorial-v2-react/react-01-unauthenticated.png" alt-text="サインイン ダイアログを表示している Web ブラウザー":::

1. サインイン ボタンを選択して、サインインします。

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスを許可してサインインすることを求められます。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Web ブラウザーに表示されているコンテンツ ダイアログ":::

要求されたアクセス許可に同意すると、Web アプリケーションに自分の名前が表示されます。これは、ログインが成功したことを示しています。

:::image type="content" source="media/tutorial-v2-react/react-02-authenticated.png" alt-text="Web ブラウザーでの成功したサインインの結果":::

### <a name="call-the-graph-api"></a>Graph API を呼び出す

サインインした後、 **[See Profile]** を選択して、Microsoft Graph API への呼び出しからの応答で返されるユーザー プロファイル情報を表示します。

:::image type="content" source="media/tutorial-v2-react/react-03-graph-data.png" alt-text="ブラウザーに表示される Microsoft Graph からのプロファイル情報":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 既定では、このスコープは、Azure portal に登録されているすべてのアプリケーションに自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API では、ユーザーのメールを一覧表示するために *Mail.Read* スコープが必要です。

スコープを追加すると、追加したスコープに対して追加の同意を求めるメッセージがユーザーに表示される場合があります。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順

Microsoft ID プラットフォームでの JavaScript シングルページ アプリケーションの開発についてさらに詳しく知りたい場合は、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ:シングルページ アプリ](scenario-spa-overview.md)
