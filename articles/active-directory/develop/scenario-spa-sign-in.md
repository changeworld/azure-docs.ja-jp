---
title: シングルページ アプリのサインインとサインアウト
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (サインイン) を構築する方法を学習する
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 2fa29b8cfd15ccee133ef416d729c1807aa0a400
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017922"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>シングルページ アプリケーション：サインインとサインアウト

ご利用のシングルページ アプリケーションのコードにサインインを追加する方法について学びます。

アプリケーション内の API にアクセスするトークンを取得する前に、認証されたユーザー コンテキストが必要です。 MSAL.js 内のアプリケーションにユーザーをサインインさせる方法には、次の 2 つがあります。

* `loginPopup` メソッドを使用して[ウィンドウをポップアップ](#sign-in-with-a-pop-up-window) する
* `loginRedirect` メソッドを使用して[リダイレクト](#sign-in-with-redirect)する

また、オプションで、サインイン時にユーザーに同意してもらう必要がある API のスコープを渡すこともできます。

> [!NOTE]
> 認証されたユーザー コンテキストまたは ID トークンに、ご利用のアプリケーションから既にアクセスできる場合は、ログイン手順を省略して直接トークンを取得することができます。 詳細については、「[SSO without MSAL.js login](msal-js-sso.md#sso-without-msaljs-login)」を参照してください。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドをお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* ポップアップウィンドウが無効になっているブラウザの制約またはポリシーがある場合は、リダイレクト方法を使用できます。 [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups) があるので、Internet Explorer ブラウザーでは、リダイレクト メソッドを使用してください。

## <a name="sign-in-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してサインインする

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        accountId = loginResponse.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

MSAL Angular ラッパーを使用すると、ルート定義に `MsalGuard` を追加するだけで、ご利用のアプリケーション内の特定のルートを保護することができます。 このガードにより、そのルートにアクセスすると、サインインのためのメソッドが呼び出されます。

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
    {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [
            MsalGuard
        ]
    },
    {
        path: '',
        component: HomeComponent
    }
];

@NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: false })],
    exports: [RouterModule]
})
export class AppRoutingModule { }
```

ポップアップ ウィンドウ エクスペリエンスの場合は、Guard 構成で `interactionType` 構成を `InteractionType.Popup` に設定します。 次のように同意を必要とするスコープを渡すこともできます。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Popup, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
MSAL Angular ラッパーを使用すると、ルート定義に `MsalGuard` を追加するだけで、ご利用のアプリケーション内の特定のルートを保護することができます。 このガードにより、そのルートにアクセスすると、サインインのためのメソッドが呼び出されます。
```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

ポップアップ ウィンドウ エクスペリエンスの場合は、`popUp` 構成オプションを有効にします。 次のように同意を必要とするスコープを渡すこともできます。

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```

# <a name="react"></a>[React](#tab/react)

MSAL React ラッパーを使用すると、特定のコンポーネントを `MsalAuthenticationTemplate` コンポーネントにラップして保護することができます。 このコンポーネントによって、ユーザーがまだサインインしていない場合はログインが呼び出され、それ以外の場合は子コンポーネントがレンダリングされます。

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Popup}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

また、`@azure/msal-browser` API を直接使用して、`AuthenticatedTemplate` および `UnauthenticatedTemplate` またはいずれかのコンポーネントとペアになっているログインを呼び出し、サインインまたはサインアウトしたユーザーそれぞれに特定のコンテンツをレンダリングすることもできます。 これは、ボタン クリックなどのユーザー操作の結果としてログインを呼び出す必要がある場合に推奨される方法です。

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginPopup();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-in-with-redirect"></a>リダイレクトを使用してサインインする

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    if (response !== null) {
        accountId = response.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    } else {
        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();

        if (currentAccounts.length === 0) {
            // no accounts signed-in, attempt to sign a user in
            myMsal.loginRedirect(loginRequest);
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            accountId = currentAccounts[0].homeAccountId;
        }
    }
}

myMsal.handleRedirectPromise().then(handleResponse);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

リダイレクトメソッドは、メインアプリから離れるため、Promise　を返しません。 返されたトークンを処理してアクセスするには、リダイレクト メソッドを呼び出す前に成功とエラーのコールバックを登録します。

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

このコードは、前述のポップアップ ウィンドウを使用したサインインに関するセクションで説明したものと同じですが、`interactionType` が MsalGuard 構成の `InteractionType.Redirect` に設定され、`MsalRedirectComponent` がリダイレクトを処理するためにブートストラップされている点が異なります。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

このコードは、ポップアップウィンドウを使用したサインインに関するセクションで説明したものと同じです。 既定のフローはリダイレクトです。

# <a name="react"></a>[React](#tab/react)

MSAL React ラッパーを使用すると、特定のコンポーネントを `MsalAuthenticationTemplate` コンポーネントにラップして保護することができます。 このコンポーネントによって、ユーザーがまだサインインしていない場合はログインが呼び出され、それ以外の場合は子コンポーネントがレンダリングされます。

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Redirect}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

また、`@azure/msal-browser` API を直接使用して、`AuthenticatedTemplate` および `UnauthenticatedTemplate` またはいずれかのコンポーネントとペアになっているログインを呼び出し、サインインまたはサインアウトしたユーザーそれぞれに特定のコンテンツをレンダリングすることもできます。 これは、ボタン クリックなどのユーザー操作の結果としてログインを呼び出す必要がある場合に推奨される方法です。

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginRedirect();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してサインアウトする

MSAL.js v2 には `logoutPopup` メソッドが用意されています。このメソッドでは、ブラウザー ストレージのキャッシュをクリアし、Azure Active Directory (Azure AD) サインアウト ページへのリンクを含むポップアップ ウィンドウを開くことができます。 サインアウト後、Azure AD によってアプリケーションにポップアップがリダイレクトされ、MSAL.js がポップアップを閉じます。

`postLogoutRedirectUri` を設定すると、サインアウト後に Azure AD によってリダイレクトされる URI を構成することができます。 この URI は、アプリケーションの登録でリダイレクト URI として登録する必要があります。

要求の一部として `mainWindowRedirectUri` を渡すことによって、ログアウトの完了後にメイン ウィンドウを別のページ (ホーム ページやサインイン ページなど) にリダイレクトするように `logoutPopup` を構成することもできます。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", // defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId),
    mainWindowRedirectUri: "your_app_main_window_redirect_uri"
}

await myMsal.logoutPopup(logoutRequest);
```
# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

MSAL.js v1 では、ポップアップ ウィンドウを使用したサインアウトをサポートしていません

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutPopup({
        mainWindowRedirectUri: "/"
    });
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

MSAL Angular v1 では、ポップアップ ウィンドウを使用したサインアウトをサポートしていません

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        mainWindowRedirectUri: "your_app_main_window_redirect_uri",
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutPopup(logoutRequest);
}

// SignOutButton Component returns a button that invokes a popup logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-redirect"></a>リダイレクトを使用してサインアウトする

MSAL.js には、v1 では `logout` メソッド、v2 では `logoutRedirect` メソッドが用意されています。このメソッドは、ブラウザー ストレージのキャッシュをクリアし、ウィンドウを Azure Active Directory (Azure AD) サインアウト ページにリダイレクトします。 サインアウト後、既定では Azure AD によって logout を呼び出したページにリダイレクトされます。

`postLogoutRedirectUri` を設定することで、サインアウト後に、リダイレクトされる URI を構成することができます。 この URI は、アプリケーションの登録でリダイレクト URI として登録する必要があります。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId)
}

myMsal.logoutRedirect(logoutRequest);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutRedirect();
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})
// In app.component.ts
this.authService.logout();
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutRedirect(logoutRequest);
}

// SignOutButton Component returns a button that invokes a redirect logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリのトークンの取得](scenario-spa-acquire-token.md)に関する記事に進みます。
