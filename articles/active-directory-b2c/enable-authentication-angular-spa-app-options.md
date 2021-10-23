---
title: Azure Active Directory B2C を使用して Angular アプリケーション オプションを有効にする
description: いくつかの方法で、Angular アプリケーション オプションの使用を有効にします。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 94057e0b1d566d092089725dea96a53e907f2a71
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041103"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Angular アプリケーションで認証オプションを構成する

この記事では、Angular シングルページ アプリケーション (SPA) の Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスをカスタマイズおよび拡張できる方法について説明します。 開始する前に、[Angular SPA での認証の構成](configure-authentication-sample-angular-spa-app.md)または[独自の Angular SPA での認証の有効化](enable-authentication-angular-spa-app.md)に関する記事の内容をよく理解しておいてくだささい。


## <a name="sign-in-and-sign-out-behavior"></a>サインインとサインアウトの動作


次の 2 つの方法で、MSAL.js を使用してユーザーをサインさせるようにシングルページ アプリケーションを構成することができます。

- **ポップアップ ウィンドウ**: 認証はポップアップ ウィンドウで行われ、アプリケーションの状態は保持されます。 認証中にユーザーをアプリケーション ページから移動させたくない場合は、この方法を使用します。  [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)があることに注意してください。
  - ポップアップ ウィンドウでサインインするには、`src/app/app.component.ts` クラスで `loginPopup` メソッドを使用します。  
  - `src/app/app.module.ts` クラスで、`interactionType` 属性を `InteractionType.Popup` に設定します。
  - ポップアップ ウィンドウでサインアウトするには、`src/app/app.component.ts` クラスで `logoutPopup` メソッドを使用します。 要求の一部として `mainWindowRedirectUri` を渡すことにより、サインアウトが完了した後、メイン ウィンドウを別のページ (ホーム ページやサインインページなど) にリダイレクトするように `logoutPopup` を構成することもできます。
- **リダイレクト**: ユーザーは認証フローを完了するために Azure AD B2C にリダイレクトされます。 ユーザーのブラウザーに制約またはポリシーがあり、ポップアップ ウィンドウが無効になっている場合は、この方法を使用します。 
  - リダイレクトでサインインするには、`src/app/app.component.ts` クラスで `loginRedirect` メソッドを使用します。  
  - `src/app/app.module.ts` クラスで、`interactionType` 属性を `InteractionType.Redirect` に設定します。
  - リダイレクトでサインアウトするには、`src/app/app.component.ts` クラスで `logoutRedirect` メソッドを使用します。 `postLogoutRedirectUri` を設定することで、サインアウト後にリダイレクトする必要がある URI を構成します。 この URI は、アプリケーションの登録でリダイレクト URI として登録する必要があります。
  
次のサンプルは、サインインとサインアウトの方法を示しています。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
  } else {
    this.authService.loginRedirect();
  }
}

logout() { 
  this.authService.logoutRedirect({
    postLogoutRedirectUri: 'http://localhost:4200'
  });
}
```

---

MSAL Angular ライブラリには、対話型サインイン (ユーザーがサインイン ボタンを選択する)、MSAL Guard、MSAL Interceptor の 3 つのサインイン フローがあります。 MSAL Guard と MSAL Interceptor の構成は、ユーザーが有効なアクセス トークンなしで保護されたリソースにアクセスしようとした場合に有効になります。 このような場合、MSAL ライブラリではユーザーにサインインを強制します。 

次のサンプルでは、ポップアップ ウィンドウまたはリダイレクトでのサインイン用に MSAL Guard と MSAL Interceptor を構成する方法を示します。 

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用する場合は、「[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)」の説明に従って、必要な入力要求を追加します。 
1. 既存の `PopupRequest` または `RedirectRequest` MSAL 構成オブジェクトを作成または使用します。
1. 対応するサインイン ヒントを使用して `loginHint` 属性を設定します。 

次のコード スニペットは、サインイン ヒント パラメーターを渡す方法を示しています。 属性値として `bob@contoso.com` が使用されます。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. 既存の `PopupRequest` または `RedirectRequest` MSAL 構成オブジェクトを作成または使用します。
1. 対応するドメイン ヒントを使用して `domainHint` 属性を設定します。

次のコード スニペットは、ドメイン ヒント パラメーターを渡す方法を示しています。 属性値として `facebook.com` が使用されます。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。 
1. `extraQueryParameters` 属性を持つ既存の `PopupRequest` または `RedirectRequest` MSAL 構成オブジェクトを作成または使用します。
1. 対応する言語コードが含まれる `ui_locales` パラメーターを、`extraQueryParameters` 属性に追加します。

次のコード スニペットは、ドメイン ヒント パラメーターを渡す方法を示しています。 属性値として `es-es` が使用されます。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. `extraQueryParameters` 属性を持つ既存の `PopupRequest` または `RedirectRequest` MSAL 構成オブジェクトを作成または使用します。
1. `campaignId` などのカスタム クエリ文字列パラメーターを追加します。 パラメーターの値を設定します。 

次のコード スニペットは、カスタム クエリ文字列パラメーターを渡す方法を示しています。 属性値として `germany-promotion` が使用されます。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. `extraQueryParameters` 属性を持つ既存の `PopupRequest` または `RedirectRequest` MSAL 構成オブジェクトを作成または使用します。
1. ID トークンを格納する、対応する変数が含まれる `id_token_hint` パラメーターを追加します。

次のコード スニペットは、ID トークン ヒントを定義する方法を示しています。

#### <a name="pop-up"></a>[ポップアップ](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[リダイレクト](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でテナント ID にカスタム ドメインを使用する場合は、[カスタム ドメインの有効化](custom-domain.md)に関するガイダンスに従ってください。 `src/app/auth-config.ts` MSAL 構成オブジェクトを開き、カスタム ドメイン名とテナント ID を使用するように `authorities` と `knownAuthorities` を変更します。  

次の JavaScript は、変更前の MSAL 構成オブジェクトを示しています。 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

次の JavaScript は、変更後の MSAL 構成オブジェクトを示しています。 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

Angular [ログ記録](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md)を構成するには、*src/app/auth-config.ts* で、次のキーを構成します。

- `loggerCallback` はロガー コールバック関数です。 
- `logLevel` では、ログ記録のレベルを指定できます。 使用できる値: `Error`、`Warning`、`Info`、および `Verbose`。
- `piiLoggingEnabled` では、個人データの入力を有効にします。 指定できる値: `true` または `false`。
 
次のコード スニペットでは、MSAL ログを構成する方法を示します。

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>次の手順

- 詳細情報: [MSAL.js の構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)。
