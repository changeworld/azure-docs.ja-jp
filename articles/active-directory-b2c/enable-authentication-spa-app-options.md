---
title: Azure Active Directory B2C を使用して SPA アプリケーション オプションを有効にする
description: この記事では、SPA アプリケーションの使用を可能にするいくつかの方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fb4d234f2ec6d991faa1c671a6b311eeff5ed299
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037114"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>Azure AD B2C を使用してシングルページ アプリケーションで認証オプションを構成する

この記事では、シングルページ アプリケーション (SPA) の Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスをカスタマイズおよび拡張する方法について説明します。 

始める前に、[サンプル Web アプリケーションでの認証の構成](configure-authentication-sample-spa-app.md)に関する記事を理解しておいてください。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でカスタム ドメインとテナント ID を使用するには、[カスタム ドメインを有効にする](custom-domain.md)方法に関するページのガイダンスに従ってください。 Microsoft Authentication Library (MSAL) 構成オブジェクトを見つけて、*authorities* と *knownAuthorities* を実際のカスタム ドメイン名とテナント ID を使用するように変更します。

次の JavaScript コードは、変更 "*前*" の MSAL 構成オブジェクトを示しています。 

```Javascript
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

次の JavaScript コードは、変更 "*後*" の MSAL 構成オブジェクトを示しています。 

```Javascript
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

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. **login_hint** を格納するオブジェクトを作成し、このオブジェクトを **MSAL loginPopup()** メソッドに渡します。

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. **extraQueryParameters** を格納するオブジェクトを作成し、このオブジェクトを **MSAL loginPopup()** メソッドに渡します。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。
1. **extraQueryParameters** を格納するオブジェクトを作成し、このオブジェクトを **MSAL loginPopup()** メソッドに渡します。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. **extraQueryParameters** を格納するオブジェクトを作成し、このオブジェクトを **MSAL loginPopup()** メソッドに渡します。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. **extraQueryParameters** を格納するオブジェクトを作成し、このオブジェクトを **MSAL loginPopup()** メソッドに渡します。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>ログアウトのリダイレクトをセキュリティで保護する

ログアウト後、ユーザーは、アプリケーションに対して指定されている応答 URL に関係なく、`post_logout_redirect_uri` パラメーターに指定された URI にリダイレクトされます。 ただし、有効な `id_token_hint` が渡され、 [[ログアウト要求に ID トークンが必要]](session-behavior.md#secure-your-logout-redirect) が有効になっている場合、Azure AD B2C では、リダイレクトの実行前に、`post_logout_redirect_uri` の値がいずれかのアプリケーションの構成済みのリダイレクト URI と一致するかどうかが検証されます。 一致する応答 URL がアプリケーションで構成されていない場合は、エラー メッセージが表示され、ユーザーはリダイレクトされません。

セキュリティで保護されたログアウトのリダイレクト URI をサポートするには、次の手順を実行します。

1. グローバルにアクセス可能な変数を作成し、`id_token` を格納します。
    ```javascript
    let id_token = "";
    ```
    
1. MSAL の `handleResponse` 関数で、`authenticationResult` オブジェクトの `id_token` を解析して `id_token` 変数に格納します。
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. `signOut` 関数で、**logoutRequest** オブジェクトに `id_token_hint` パラメーターを追加します。
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

上記の例で、ログアウト要求に渡される **post_logout_redirect_uri** は、`https://your-app.com/` の形式になります。 この URL をアプリケーション登録の応答 URL に追加する必要があります。

## <a name="enable-single-logout"></a>シングル ログアウトを有効にする

Azure AD B2C のシングル ログアウトには、OpenId Connect のフロント チャネル ログアウトが使用され、ユーザーが Azure AD B2C を介してサインインしたすべてのアプリケーションに対してログアウト要求が行われます。

これらのログアウト要求は、Azure AD B2C のログアウト ページから、非表示の Iframe で行われます。 ログイン時に Azure AD B2C によって記録されたアプリに登録されたすべてのフロントチャネル ログアウト エンドポイントに対して、Iframe から HTTP 要求が行われます。 

各アプリケーションのログアウト エンドポイントから、**MSAL logout()** メソッドを呼び出す必要があります。 また、このシナリオでは、`allowRedirectInIframe` を `true` に設定することで、Iframe 内で実行するように MSAL を明示的に構成する必要もあります。

次のコード例では、`allowRedirectInIframe` を `true` に設定しています。

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>次のステップ

[MSAL.js の構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)の詳細を確認します。
