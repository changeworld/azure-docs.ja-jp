---
title: シングルページ アプリケーション (サインイン) - Microsoft ID プラットフォーム
description: シングルページ アプリケーション (サインイン) を構築する方法を説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138800"
---
# <a name="single-page-application---sign-in"></a>シングルページ アプリケーション - サインイン

ご利用のシングルページ アプリケーションのコードにサインインを追加する方法について説明します。

ご利用のアプリケーション内の API にアクセスするトークンを取得する前に、認証されたユーザー コンテキストが必要です。 MSAL.js 内のアプリケーションにユーザーをサインインさせる方法には、次の 2 つがあります。

* `loginPopup` メソッドを使用して[ポップアップ ウィンドウでサインイン](#sign-in-with-a-pop-up-window)する
* `loginRedirect` メソッドを使用して[リダイレクトによりサインイン](#sign-in-with-redirect)する

また、必要に応じて、サインイン時にユーザーに同意してもらう必要がある API のスコープを渡すこともできます。

> [!NOTE]
> 認証されたユーザー コンテキストまたは ID トークンに、ご利用のアプリケーションから既にアクセスできる場合は、ログイン手順を省略して直接トークンを取得することができます。 詳細については、「[sso without msal.js login](msal-js-sso.md#sso-without-msaljs-login)」 (msal.js ログインなしの sso) を参照してください。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

ご利用のアプリケーション内で、ポップアップ メソッドとリダイレクト メソッドの両方を組み合わせて使用することはできません。 ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドの使用をお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* リダイレクト メソッドを使用することが必要になる特定のケースがあります。 アプリケーションのユーザーが使用しているブラウザーに制約またはポリシーが存在し、ポップアップ ウィンドウが無効になっている場合は、リダイレクト メソッドを使用することができます。 ポップアップ ウィンドウを処理するとき [Internet Explorer には特定の問題が存在することがわかっている](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)ので、Internet Explorer ブラウザーではリダイレクト メソッドを使用してください。

## <a name="sign-in-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してサインインする

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular ラッパーを使用すると、ルート定義に `MsalGuard` を追加するだけで、ご利用のアプリケーション内の特定のルートをセキュリティで保護することができます。 このガードにより、そのルートにアクセスすると、サインインのためのメソッドが呼び出されます。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

ポップアップ ウィンドウ エクスペリエンスの場合は、`popUp` 構成オプションを有効にします。 次のように同意を必要とするスコープを渡すこともできます。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>リダイレクトを使用してサインインする

### <a name="javascript"></a>JavaScript

リダイレクト メソッドでは、メイン アプリから移動することから、Promise は返されません。 返されたトークンを処理し、これにアクセスするには、リダイレクト メソッドを呼び出す前に成功とエラーのコールバックを登録する必要があります。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

次のコードは、前述の「ポップアップ ウィンドウを使用してサインインする」セクションで示したものと同じです。 既定のフローはリダイレクトです。

> [!NOTE]
> ID トークンには同意されたスコープは含まれず、認証されたユーザーのみが示されます。 同意されたスコープは、次の手順で取得するアクセス トークンで返されます。

## <a name="sign-out"></a>サインアウトする

MSAL ライブラリには `logout` メソッドが用意されています。これにより、ブラウザー ストレージ内のキャッシュがクリアされ、Azure AD にサインアウト要求が送信されます。 サインアウト後、それは既定ではアプリケーションのスタート ページに再びリダイレクトされます。

`postLogoutRedirectUri` を設定することで、サインアウト後に、それがリダイレクトされる URI を構成することができます。 この URI は、アプリケーションの登録内にログアウト URI として登録することも必要です。

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのトークンの取得](scenario-spa-acquire-token.md)
