---
title: Web API を呼び出すトークンを取得する (シングルページ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (API を呼び出すトークンを取得する) を構築する方法を説明します
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/2/2021
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 2e4369c729fc5497f615f64c1f7235d19c293f98
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227742"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>シングルページ アプリケーション：API を呼び出すトークンを取得する

[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用して API 用のトークンを取得するパターンは、`acquireTokenSilent` メソッドを使用してサイレント トークン要求を最初に試行することです。 このメソッドが呼び出されると、ライブラリでは、まずブラウザー ストレージ内のキャッシュに有効なトークンが存在するかどうかが確認され、それが返されます。 キャッシュに有効なトークンがない場合は、更新トークンを使用してトークンを取得しようとします。 更新トークンの 24 時間の有効期限が切れている場合、MSAL.js は非表示の iframe を開き、新しい認証コードをサイレントに要求します。この認証コードは新しい有効な更新トークンと交換されます。 シングル サインオン セッションおよび Azure AD 内のトークン有効期間値の詳細については、[トークンの有効期間](active-directory-configurable-token-lifetimes.md)に関するページを参照してください。

Azure AD へのサイレント トークン要求は、パスワードの変更や条件付きアクセス ポリシーの更新などの理由により、失敗する場合があります。  エラーは多くの場合、更新トークンの 24 時間の有効期限が切れ、[ブラウザーがサード パーティの Cookie をブロックしている](reference-third-party-cookies-spas.md)ことが原因で発生します。この場合、非表示の iframe を使用してユーザーの認証を継続することができなくなります。  このような場合は、トークンを取得するために、(ユーザーにプロンプトを表示できる) 対話型のメソッドのいずれかを呼び出す必要があります。

* `acquireTokenPopup` を使用して[ポップアップ ウィンドウ](#acquire-a-token-with-a-pop-up-window)でトークンを取得する
* `acquireTokenRedirect` を使用して[リダイレクト](#acquire-a-token-with-a-redirect)によりトークンを取得する

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドをお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* ポップアップウィンドウが無効になっているブラウザの制約またはポリシーがある場合は、リダイレクト方法を使用できます。 [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups) があるので、Internet Explorer ブラウザーでは、リダイレクト メソッドを使用してください。

アクセス トークン要求を作成するときにアクセス トークンに含める API スコープを設定することができます。 要求されたすべてのスコープがアクセス トークンに付与されるとは限らないことに注意してください。 これは、ユーザーの同意によって異なります。

## <a name="acquire-a-token-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してトークンを取得する

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

次のコードでは、前に説明したパターンと、ポップアップ エクスペリエンスのメソッドを組み合わせています。

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error instanceof InteractionRequiredAuthError) {
        publicClientApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
            let accessToken = accessTokenResponse.accessToken;
            // Call your API with token
            callApi(accessToken);
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

次のコードでは、前に説明したパターンと、ポップアップ エクスペリエンスのメソッドを組み合わせています。

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付する HTTP インターセプターが提供されます。

API のスコープは、`protectedResourceMap` 構成オプションで指定できます。 `MsalInterceptor` は、トークンを自動的に取得するときにこれらのスコープを要求します。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule } from '@azure/msal-angular';

@NgModule({
    declarations: [
        // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
        auth: {
            clientId: 'Enter_the_Application_Id_Here',
        },
        cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
        }
        }), {
            interactionType: InteractionType.Popup,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Popup,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

サイレント トークン取得の成功と失敗について、MSAL Angular からサブスクライブできるイベントが提供されます。 サブスクライブ解除を忘れずに行うことも重要です。

```javascript
import { MsalBroadcastService } from '@azure/msal-angular';
import { EventMessage, EventType } from '@azure/msal-browser';

// In app.component.ts
export class AppComponent implements OnInit {
    private readonly _destroying$ = new Subject<void>();

    constructor(private broadcastService: MsalBroadcastService) { }

    ngOnInit() {
        this.broadcastService.msalSubject$
        .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.ACQUIRE_TOKEN_SUCCESS),
            takeUntil(this._destroying$)
        )
        .subscribe((result: EventMessage) => {
            // Do something with event payload here
        });
    }

    ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
    }
}
```

あるいは、コア MSAL.js ライブラリ内に記述されているように、取得トークン メソッドを使用してトークンを明示的に取得することができます。

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付する HTTP インターセプターが提供されます。
API のスコープは、`protectedResourceMap` 構成オプションで指定できます。 `MsalInterceptor` は、トークンを自動的に取得するときにこれらのスコープを要求します。

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
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
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

あるいは、コア MSAL.js ライブラリ内に記述されているように、取得トークン メソッドを使用してトークンを明示的に取得することができます。

# <a name="react"></a>[React](#tab/react)

次のコードでは、前に説明したパターンと、ポップアップ エクスペリエンスのメソッドを組み合わせています。

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        if (!apiData && inProgress === InteractionStatus.None) {
            const accessTokenRequest = {
                scopes: ["user.read"],
                account: accounts[0]
            }
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
                        // Acquire token interactive success
                        let accessToken = accessTokenResponse.accessToken;
                        // Call your API with token
                        callApi(accessToken).then((response) => { setApiData(response) });
                    }).catch(function(error) {
                        // Acquire token interactive failure
                        console.log(error);
                    });
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

または、React コンポーネントの外部でトークンを取得する必要がある場合は、`acquireTokenSilent` を呼び出すことはできますが、失敗した場合には対話にフォールバックしないでください。 コンポーネント ツリーの `MsalProvider` コンポーネントの下で、すべての対話が行われます。

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="acquire-a-token-with-a-redirect"></a>リダイレクトを使用してトークンを取得する

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

次のパターンは前述したとおりですが、トークンを対話形式で取得するリダイレクト メソッドを使用して示しています。 ページの読み込み時に `handleRedirectPromise` を呼び出して待つ必要があります。

```javascript
const redirectResponse = await publicClientApplication.handleRedirectPromise();
if (redirectResponse !== null) {
    // Acquire token silent success
    let accessToken = redirectResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
} else {
    // MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
    const account = publicClientApplication.getAllAccounts()[0];
    
    const accessTokenRequest = {
        scopes: ["user.read"],
        account: account
    }
    
    publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
        // Acquire token silent success
        // Call API with token
        let accessToken = accessTokenResponse.accessToken;
        // Call your API with token
        callApi(accessToken);
    }).catch(function (error) {
        //Acquire token silent failure, and send an interactive request
        console.log(error);
        if (error instanceof InteractionRequiredAuthError) {
            publicClientApplication.acquireTokenRedirect(accessTokenRequest);
        }
    });
}
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

次のパターンは前述したとおりですが、トークンを対話形式で取得するリダイレクト メソッドを使用して示しています。 前述のようにリダイレクト コールバックを登録する必要があります。

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>省略可能な要求を要請する

次の目的で省略可能な要求を使用できます。

- アプリケーションのトークンに省略可能な要求を含める。
- Azure AD からトークンで返される特定の要求の動作を変更する。
- アプリケーションのカスタムの要求を追加してアクセスする。

`IdToken` で省略可能な要求を要請するには、`AuthenticationParameters.ts` クラスの `claimsRequest` フィールドに文字列化された要求オブジェクトを送信します。

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

詳細については、[省略可能な要求](active-directory-optional-claims.md)に関するページを参照してください。

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

このコードは、リダイレクトを処理するために `MsalRedirectComponent` をブートストラップすることを推奨していることを除いて、前に説明したものと同じです。 リダイレクトを使用するように `MsalInterceptor` 構成を変更することもできます。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    declarations: [
      // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Redirect,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
このコードは前述と同じです。

# <a name="react"></a>[React](#tab/react)

`acquireTokenSilent` が失敗した場合は、`acquireTokenRedirect` にフォールバックします。 この方法により、フル フレーム リダイレクトが開始され、応答はアプリケーションに戻るときに処理されます。 リダイレクトから戻った後にこのコンポーネントがレンダリングされるときに、トークンがキャッシュからプルされるため、`acquireTokenSilent` は成功します。

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        const accessTokenRequest = {
            scopes: ["user.read"],
            account: accounts[0]
        }
        if (!apiData && inProgress === InteractionStatus.None) {
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenRedirect(accessTokenRequest);
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

または、React コンポーネントの外部でトークンを取得する必要がある場合は、`acquireTokenSilent` を呼び出すことはできますが、失敗した場合には対話にフォールバックしないでください。 コンポーネント ツリーの `MsalProvider` コンポーネントの下で、すべての対話が行われます。

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である [Web API の呼び出し](scenario-spa-call-api.md)に関する記事に進みます。
