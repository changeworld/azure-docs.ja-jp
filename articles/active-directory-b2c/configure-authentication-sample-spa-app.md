---
title: Azure Active Directory B2C を使用してサンプル SPA アプリケーションで認証を構成する
description: SPA アプリケーションで Azure Active Directory B2C を使用してユーザーをサインインおよびサインアップします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: addf3870c22105a2ff42202e768d1e8cda4ffbde
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073086"
---
# <a name="configure-authentication-in-a-sample-single-page-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用してサンプルのシングル ページ アプリケーションで認証を構成する

この記事では、サンプルの JavaScript シングル ページ アプリケーションを使用して、SPA アプリに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は OAuth 2.0 を基盤とした認証プロトコルであり、ユーザーをアプリケーションに安全にサインインさせるために利用できます。 このシングル ページ アプリケーションのサンプルでは、[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) と OIDC PKCE フローが使用されます。 MSAL.js は、SPA アプリへの認証と承認サポートの追加を簡略化する、Microsoft で提供されているライブラリです。

### <a name="sign-in-flow"></a>サインイン フロー
サインイン フローでは、次の手順が実行されます。

1. ユーザーが Web アプリに移動し、 **[サインイン]** を選択します。 
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーは[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)するか、[パスワードをリセット](add-password-reset-policy.md)するか、[ソーシャル アカウント](add-identity-provider.md)を使用してサインインします。
1. サインインに成功すると、Azure AD B2C によって ID トークンがアプリに返されます。
1. シングル ページ アプリケーションは、ID トークンを検証し、クレームを読み取り、保護されたリソースまたは API をユーザーが呼び出すことを許可します。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録する必要があります。  

- **Web アプリケーション** の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証された後にリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。

- **Web API** の登録により、アプリではセキュリティで保護された Web API を呼び出すことができます。 登録には、Web API の "*スコープ*" が含まれます。 スコープを使用することで、Web API などの保護されたリソースへのアクセス許可を管理できます。 Web アプリケーションのアクセス許可を Web API のスコープに付与します。 アクセス トークンが要求されると、アプリでは、必要なアクセス許可が要求の scope パラメーターに指定されます。  

次の図では、アプリの登録とアプリケーションのアーキテクチャについて説明します。

![Web API 呼び出しの登録とトークンが使用されている Web アプリ](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>サインアウト フロー

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

次が実行されているコンピューター:

* [Visual Studio Code](https://code.visualstudio.com/)、または別のコード エディター
* [Node.js ランタイム](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>手順 2: SPA と API を登録する

この手順では、SPA アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="21-register-the-web-api-application"></a>2.1 Web API アプリケーションを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 スコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-client-app"></a>2.3 クライアント アプリを登録する

アプリ登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*MyApp* とします。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Single-page application (SPA)]\(シングルページ アプリケーション (SPA)\)** を選択し、URL テキスト ボックスに「`http://localhost:6420`」と入力します。
1. **[アクセス許可]** で、 **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェック ボックスをオンにします。
1. **[登録]** を選択します。

次に、暗黙的な許可のフローを有効にします。

1. [管理] で、 [認証] を選択します。
1. [新しいエクスペリエンスを試す] (表示されている場合) を選択します。
1. [暗黙的な許可] で、[ID トークン] チェック ボックスをオンにします。
1. [保存] を選択します。

**アプリケーション (クライアント) ID** を記録しておきます。これは、後の手順で Web アプリケーションを構成するときに使用します。
    ![アプリケーション ID を取得する](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 アクセス許可を付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>手順 3: SPA のサンプル コードを取得する

このサンプルでは、シングルページ アプリケーションでユーザーのサインアップおよびサインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています。 下のサンプルをダウンロードします。

  [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)するか、GitHub からサンプルを複製します。

  ```
  git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
  ```

### <a name="31-update-the-spa-sample"></a>3.1 SPA のサンプルを更新する

サンプルを入手したので、Azure AD B2C テナント名と手順 2.3 で記録した *myApp* のアプリケーション ID を使用してコードを更新します。

*App* フォルダー内の *authConfig.js* ファイルを開きます。
1. `msalConfig` オブジェクトで `clientId` の割り当てを見つけ、手順 2.3 で記録した **アプリケーション (クライアント) ID** に置き換えます。

`policies.js` ファイルを開きます。
1. `names` の下のエントリを見つけて、それらの割り当てを、前の手順で作成したユーザー フローの名前 (`b2c_1_susi`など) に置き換えます。
1. `authorities` の下のエントリを見つけて、それらの割り当てを、前の手順で作成した適切なユーザー フローの名前 (`https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`など) に置き換えます。
1. `authorityDomain` の割り当てを見つけ、`<your-tenant-name>.b2clogin.com` に置き換えます。

`apiConfig.js` ファイルを開きます。
1. `b2cScopes` の割り当てを見つけ、URL を、Web API 用に作成したスコープ URL (たとえば、`b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`) に置き換えます。
1. `webApi` の割り当てを見つけ、現在の URL を `http://localhost:5000/tasks` に置き換えます。


結果のコードは次のサンプルのようになります。

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>手順 4: Web API のサンプル コードを取得する

Web API を登録し、そのスコープを定義したので、Azure AD B2C テナントで動作するように Web API コードを構成します。 下のサンプルをダウンロードします。

[\*.zip アーカイブをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)するか、GitHub からサンプルの Web API プロジェクトを複製します。 また、GitHub で [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) プロジェクトを直接参照することもできます。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-update-the-web-api"></a>4.1 Web API を更新する

1. コード エディターで *config.json* ファイルを開きます。
1. 前に作成したアプリケーション登録で変数の値を変更します。 また、前提条件の一部として作成したユーザー フローを使用して、`policyName` を更新します。 たとえば、*b2c_1_susi* のようにします。
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="42-enable-cors"></a>4.2 CORS を有効にする

シングルページ アプリケーションに Node.js Web API の呼び出しを許可するには、Web API で [CORS](https://expressjs.com/en/resources/middleware/cors.html) を有効にする必要があります。 運用アプリケーションでは、どのドメインによって要求が行われているかに注意する必要があります。 この例では、どのドメインからの要求も許可します。

CORS を有効にするには、次のミドルウェアを使用します。 ダウンロードした Node.js の Web API コード サンプルでは、*index.js* ファイルに既に追加されています。

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>手順 5: SPA と Web API を実行する

これで、シングルページ アプリケーションの API へのスコープ付きアクセスをテストする準備ができました。 ローカル マシンで Node.js Web API とサンプル JavaScript シングルページ アプリケーションの両方を実行します。 次に、シングルページ アプリケーションにサインインし、 **[Call API]\(API の呼び出し\)** ボタンを選択して、保護されている API に対する要求を開始します。

### <a name="run-the-nodejs-web-api"></a>Node.js Web API を実行する

1. コンソール ウィンドウを開き、Node.js Web API サンプルを含むディレクトリに移動します。 次に例を示します。

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    node index.js
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>シングルページ アプリを実行する

1. 別のコンソール ウィンドウを開き、JavaScript SPA サンプルを含むディレクトリに移動します。 次に例を示します。

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    npm start
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 6420...
    ```

1. アプリケーションを表示するには、ブラウザーで `http://localhost:6420` に移動します。

    ![ブラウザーに表示されたシングルページ アプリケーションのサンプル アプリ](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. [前のチュートリアル](tutorial-single-page-app.md)で使用したメール アドレスとパスワードを使用してサインインします。 ログインが成功すると、`User 'Your Username' logged-in` というメッセージが表示されます。
1. **[Call API]** ボタンを選択します。 SPA では、要求内のアクセス トークンを保護された Web API に送信します。それによって、ログインしているユーザーの表示名が返されます。

    ![API によって返されるユーザー名の JSON 結果を示す、ブラウザーのシングルページ アプリケーション](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする 

運用アプリケーションでは、通常、アプリ登録のリダイレクト URI は、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com/signin-oidc` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ

* [コード サンプル](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)の詳細情報
* [Azure AD B2C を使用した独自の SPA アプリケーションでの認証オプション](enable-authentication-spa-app-options.md)について学習する
