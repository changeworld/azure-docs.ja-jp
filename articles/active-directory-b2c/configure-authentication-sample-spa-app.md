---
title: Azure Active Directory B2C を使用してサンプルのシングルページ アプリケーションで認証を構成する
description: この記事では、Azure Active Directory B2C を使用してシングルページ アプリケーションでユーザーをサインインおよびサインアップする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 620afdfbcc62c3ce8e07c1572dd4ec20634ebd64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007883"
---
# <a name="configure-authentication-in-a-sample-single-page-application-by-using-azure-ad-b2c"></a>Azure AD B2C を使用してサンプルのシングルページ アプリケーションで認証を構成する

この記事では、サンプルの JavaScript シングルページ アプリケーション (SPA) を使用して、Azure Active Directory B2C (Azure AD B2C) 認証を SPA に追加する方法について説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 これを使用して、ユーザーをアプリケーションに安全にサインインさせることができます。 このシングルページ アプリケーションのサンプルでは、[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) と OIDC PKCE フローを使用します。 Microsoft が提供する MSAL.js ライブラリを使用すると、認証と認可のサポートを SPA アプリに簡単に追加できます。

### <a name="sign-in-flow"></a>Sign-in flow

サインイン フローでは、次の手順が実行されます。

1. ユーザーが Web アプリにアクセスして **[サインイン]** を選択します。
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)し、[パスワードをリセット](add-password-reset-policy.md)します。 [ソーシャル アカウント](add-identity-provider.md)を使用してサインインすることもできます。
1. ユーザーがサインインすると、Azure AD B2C からアプリに認可コードが返されます。
1. シングルページ アプリケーションで、ID トークンが検証され、要求が読み取られ、保護されたリソースと API をユーザーが呼び出すことが許可されます。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- **Web アプリケーション** の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、Azure AD B2C での認証が完了した後にユーザーが Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。

- **Web API** の登録により、アプリではセキュリティで保護された Web API を呼び出すことができます。 登録には、Web API の "*スコープ*" が含まれます。 スコープを使用することで、Web API などの保護されたリソースへのアクセス許可を管理できます。 Web アプリケーションのアクセス許可を Web API のスコープに付与します。 アクセス トークンが要求されたら、アプリで必要なアクセス許可を要求の scope パラメーターに指定します。  

アプリのアーキテクチャと登録について、次の図に示します。

![Web API 呼び出しの登録とトークンが使用されている Web アプリの図。](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

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

この手順では、SPA と Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="step-21-register-the-web-api-application"></a>手順 2.1: Web API アプリケーションを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-scopes"></a>手順 2.2: スコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-23-register-the-spa"></a>手順 2.3: SPA を登録する

SPA 登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure AD B2C** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します (*MyApp* など)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Single-page application (SPA)]\(シングルページ アプリケーション (SPA)\)** を選択し、URL ボックスに「`http://localhost:6420`」と入力します。
1. **[アクセス許可]** で、 **[Grant admin consent to openid and offline access permissions]\(OpenID とオフラインのアクセス許可に管理者の同意を与える\)** チェック ボックスをオンにします。
1. **[登録]** を選択します。

### <a name="step-24-enable-the-implicit-grant-flow"></a>手順 2.4: 暗黙的な許可のフローを有効にする

次に、暗黙的な許可のフローを有効にします。

1. **[管理]** で、 **[認証]** を選択します。

1. **[新しいエクスペリエンスを試す]** (表示されている場合) を選択します。

1. **[Implicit grant]\(暗黙的な許可\)** で、 **[ID トークン]** チェック ボックスをオンにします。

1. **[保存]** を選択します。

   **アプリケーション (クライアント) ID** を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web アプリケーション ID を記録するための Web アプリの [概要] ページのスクリーンショット。](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-25-grant-permissions"></a>手順 2.5: アクセス許可を付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>手順 3: SPA のサンプル コードを取得する

このサンプルでは、シングルページ アプリケーションでユーザーのサインアップとサインインに Azure AD B2C を使用する方法を示します。 その後、アプリではアクセス トークンを取得して、保護された Web API を呼び出します。 

SPA サンプル コードは、次のいずれかの方法で入手できます。 

* [zip ファイルをダウンロードします](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)。 
* 次のコマンドを実行して、GitHub からサンプルを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

### <a name="step-31-update-the-spa-sample"></a>手順 3.1: SPA のサンプルを更新する

SPA サンプルを入手したら、Azure AD B2C と Web API の値でコードを更新します。 サンプル フォルダー内の `App` フォルダーにある、次の表の JavaScript ファイルを開き、それぞれの対応する値で更新します。  


|ファイル  |Key  |値  |
|---------|---------|---------|
|authConfig.js|clientId| [手順 2.3](#step-23-register-the-spa) の SPA ID。|
|policies.js| names| ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|
|policies.js|authorities|Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。 次に、ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシーに置き換えます (例: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`)。|
|policies.js|authorityDomain|Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。|
|apiConfig.js|b2cScopes|[手順 2.2](#step-22-configure-scopes) で作成した Web API スコープ (例: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`)。|
|apiConfig.js|webApi|Web API の URL (`http://localhost:5000/tasks`)。|
| | | |

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

Web API を登録し、そのスコープを定義したので、Azure AD B2C テナントで動作するように Web API コードを構成します。 

Web API のサンプル コードは、次のいずれかの方法で入手できます。

* [\*.zip アーカイブをダウンロードします](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)。

* 次のコマンドを実行して、GitHub からサンプル Web API プロジェクトを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
    ```

* また、GitHub で [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) プロジェクトに直接アクセスすることもできます。

### <a name="step-41-update-the-web-api"></a>手順 4.1: Web API を更新する

1. コード エディターで *config.json* ファイルを開きます。
1. 前に作成したアプリケーション登録で変数の値を変更します。 前提条件の一部として作成したユーザー フローを使用して、`policyName` を更新します (例: *b2c_1_susi*)。
    
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

### <a name="step-42-enable-cors"></a>手順 4.2: CORS を有効にする

シングルページ アプリケーションに Node.js Web API の呼び出しを許可するには、Web API で [クロスオリジン リソース共有 (CORS)](https://expressjs.com/en/resources/middleware/cors.html) を有効にする必要があります。 運用アプリケーションでは、どのドメインによって要求が行われているかに注意してください。 この例では、どのドメインからの要求も許可します。

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

1. コンソール ウィンドウを開き、Node.js Web API サンプルが含まれるディレクトリに移動します。 次に例を示します。

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

1. 別のコンソール ウィンドウを開き、JavaScript SPA サンプルが含まれるディレクトリに移動します。 次に例を示します。

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

1. アプリケーションを表示するには、ブラウザーで `http://localhost:6420` にアクセスします。

    ![ブラウザー ウィンドウに表示された SPA サンプル アプリのスクリーンショット。](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. サインインアップまたはサインイン プロセスを完了します。 正常にログインすると、"User \<your username> logged in" というメッセージが表示されます。
1. **[Call API]** ボタンを選択します。 SPA では、要求内のアクセス トークンを保護された Web API に送信します。それによって、ログインしているユーザーの表示名が返されます。

    ![API から返されたユーザー名 JSON の結果を示す、ブラウザー ウィンドウ内の SPA のスクリーンショット。](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする 

運用アプリケーションでは、アプリ登録のリダイレクト URI は通常、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com/signin-oidc` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ

この記事で説明した概念の詳細については、以下を参照してください。
* [コード サンプルの詳細情報](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)
* [独自の SPA で認証を有効にする](enable-authentication-spa-app.md)
* [SPA で認証オプションを構成する](enable-authentication-spa-app-options.md)
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)
