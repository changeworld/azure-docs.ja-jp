---
title: シングルページ アプリケーション (API を呼び出すトークンを取得する) - Microsoft ID プラットフォーム
description: シングルページ アプリケーション (API を呼び出すトークンを取得する) を構築する方法を説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138817"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>シングルページ アプリケーション - API を呼び出すトークンを取得する

MSAL.js を使用して API 用のトークンを取得するパターンは、`acquireTokenSilent` メソッドを使用してサイレント トークン要求を最初に試行することです。 このメソッドが呼び出されると、ライブラリでは、まずブラウザー ストレージ内のキャッシュに有効なトークンが存在するかどうかが確認され、それが返されます。 キャッシュ内に有効なトークンがない場合は、非表示の iframe から Azure Active Directory (Azure AD) にサイレント トークン要求が送信されます。 このメソッドを使用すれば、ライブラリでトークンを更新することもできます。 シングル サインオン セッションおよび Azure AD 内のトークン有効期間値の詳細については、[トークンの有効期間](active-directory-configurable-token-lifetimes.md)に関するページを参照してください。

Azure AD へのサイレント トークン要求は、Azure AD セッションの有効期限切れやパスワードの変更などのいくつかの理由により、失敗する場合があります。 その場合は、トークンを取得するために対話型メソッド (ユーザーにプロンプトを表示する) のいずれかを呼び出すことができます。

* `acquireTokenPopup` を使用して[ポップアップ ウィンドウでトークンを取得](#acquire-token-with-a-pop-up-window)する
* `acquireTokenRedirect` を使用して[リダイレクトによりトークンを取得](#acquire-token-with-redirect)する

**ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択**

 ご利用のアプリケーション内で、ポップアップ メソッドとリダイレクト メソッドの両方を組み合わせて使用することはできません。 ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドの使用をお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* リダイレクト メソッドを使用することが必要になる特定のケースがあります。 アプリケーションのユーザーが使用しているブラウザーに制約またはポリシーが存在し、ポップアップ ウィンドウが無効になっている場合は、リダイレクト メソッドを使用することができます。 ポップアップ ウィンドウを処理するとき [Internet Explorer には特定の問題が存在することがわかっている](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)ので、Internet Explorer ブラウザーではリダイレクト メソッドを使用することをお勧めします。

アクセス トークン要求を作成するときにアクセス トークンに含める API スコープを設定することができます。 要求されたすべてのスコープがアクセス トークンに付与されるとは限らず、ユーザーの同意に依存することに注意してください。

## <a name="acquire-token-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してトークンを取得する

### <a name="javascript"></a>JavaScript

ポップアップ エクスペリエンス用のメソッドを使用する上記のパターン:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付する HTTP インターセプター `MsalInterceptor` を容易に追加できます。

トークンの自動取得の際に MsalInterceptor から要求される `protectedResourceMap` 構成オプション内に API のスコープを指定することができます。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

サイレント トークン取得の成功と失敗については、MSAL Angular からコールバックが提供され、それはサブスクライブすることができます。 サブスクライブ解除を忘れずに行うことも重要です。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

あるいは、コア MSAL.js ライブラリ内に記述されているように、取得トークン メソッドを使用してトークンを明示的に取得することもできます。

## <a name="acquire-token-with-redirect"></a>リダイレクトを使用してトークンを取得する

### <a name="javascript"></a>JavaScript

パターンは前述したとおりですが、トークンを対話形式で取得するリダイレクト メソッドを使用して示しています。 前述のようにリダイレクト コールバックを登録する必要があるので注意してください。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

これは上記と同じです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Web API の呼び出し](scenario-spa-call-api.md)
