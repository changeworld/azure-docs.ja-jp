---
title: Azure Active Directory B2C を使用してサンプルの Angular SPA で認証を構成する
description: Azure Active Directory B2C を使用して、Angular SPA でユーザーをサインインおよびサインアップする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: acce5f3152f8e12bd93640892f9f495834f34b9f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007900"
---
# <a name="configure-authentication-in-a-sample-angular-single-page-application-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用してサンプルの Angular シングルページ アプリケーションで認証を構成する

この記事では、サンプルの Angular シングルページ アプリケーション (SPA) を使用して、Azure Active Directory B2C (Azure AD B2C) 認証を Angular アプリに追加する方法について説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は OAuth 2.0 を基盤とした認証プロトコルであり、ユーザーをアプリケーションに安全にサインインさせるために利用できます。 この Angular サンプルでは、[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) と [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) を使用します。 MSAL は、Angular SPA への認証と承認のサポートの追加を簡素化する、Microsoft 提供のライブラリです。

### <a name="sign-in-flow"></a>Sign-in flow

サインイン フローでは、次の手順が実行されます。

1. ユーザーがアプリを開き、 **[サインイン]** を選択します。 
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーは[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)し、[パスワードをリセット](add-password-reset-policy.md)するか、[ソーシャル アカウント](add-identity-provider.md)を使用してサインインします。
1. サインインに成功すると、Azure AD B2C からアプリに承認コードが返されます。 アプリによって次のアクションが実行されます。
   1. 承認コードを ID トークン、アクセス トークン、および更新トークンと交換する。
   1. ID トークン クレームを読み取る。
   1. 後で使用できるように、アクセス トークンと更新トークンをメモリ内キャッシュに格納する。 アクセス トークンを使用することで、ユーザーは Web API などの保護されたリソースを呼び出すことができます。 更新トークンは、新しいアクセス トークンを取得するために使用されます。

### <a name="app-registration"></a>アプリの登録

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録する必要があります。  

- *シングルページ アプリケーション* (Angular) の登録により、アプリで Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証された後にリダイレクトされるエンドポイントです。 アプリ登録プロセスは、アプリを一意に識別する *アプリケーション ID* (または *クライアント ID* として知られています) を生成します。 この記事では、例として **アプリ ID: 1** を使用します。

- *Web API* を登録すると、保護された Web API をアプリで呼び出すことができます。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別するアプリケーション ID が生成されます。 この記事では、例として **アプリ ID: 2** を使用します。 アプリ (**アプリ ID: 1**) のアクセス許可を Web API スコープ (**アプリ ID: 2**) に付与します。  

次の図は、アプリの登録とアプリのアーキテクチャを示しています。

![シングルページ アプリケーションと Web A P I、登録、およびトークンを示す図。](./media/configure-authentication-sample-angular-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>サインアウト フロー

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

この記事の手順に従う前に、コンピューターで以下が実行されていることを確認してください。

* [Visual Studio Code](https://code.visualstudio.com/)、または別のコード エディター。
* [Node.js ランタイム](https://nodejs.org/en/download/) と [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)。
* [Angular CLI](https://angular.io/cli)。

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-angular-spa-and-api"></a>手順 2: Angular SPA と API を登録する

この手順では、Angular SPA と Web API アプリの登録を作成します。 また、Web API のスコープを指定します。

### <a name="21-register-the-web-api-application"></a>2.1 Web API アプリケーションを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 スコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-angular-app"></a>2.3 Angular アプリを登録する

Angular アプリの登録を作成するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** にアプリケーションの名前を入力します。 たとえば、「**MyApp**」と入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Single-page application (SPA)]\(シングルページ アプリケーション (SPA)\)** を選択し、URL ボックスに「`http://localhost:4200`」と入力します。
1. **[アクセス許可]** で、 **[Grant admin consent to openid and offline access permissions]\(OpenID とオフラインのアクセス許可に管理者の同意を与える\)** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. **[アプリケーション (クライアント) ID]** の値を記録しておきます。これは、後の手順で Web アプリケーションを構成するときに使用します。
    ![Angular アプリケーション I D を取得する方法を示すスクリーンショット。](./media/configure-authentication-sample-angular-spa-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 アクセス許可を付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-angular-sample-code"></a>手順 3: Angular のサンプル コードを取得する

このサンプルでは、Angular シングルページ アプリケーションでユーザーのサインアップとサインインに Azure AD B2C を使用する方法を示します。 その後、アプリでアクセス トークンを取得して、保護された Web API を呼び出します。

  サンプルの [.zip ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/archive/refs/heads/main.zip)するか、次のコマンドを使用して [GitHub リポジトリ](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)からサンプルをクローンします。

  ```
  git clone https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial.git
  ```

### <a name="31-configure-the-angular-sample"></a>3.1 Angular サンプルを構成する

SPA サンプルを入手したら、Azure AD B2C と Web API の値でコードを更新します。 サンプル フォルダーの *src/app* フォルダーの下にある *auth-config.ts* ファイルを開きます。 キーを対応する値で更新します。  


|Section  |キー  |値  |
|---------|---------|---------|
| b2cPolicies | names |[手順 1](#step-1-configure-your-user-flow) で作成したユーザー フローまたはカスタム ポリシー。 |
| b2cPolicies | authorities | `your-tenant-name` を Azure AD B2C の[テナント名](tenant-management.md#get-your-tenant-name)に置き換えます。 たとえば、 `contoso.onmicrosoft.com`を使用します。 次に、ポリシー名を、[手順 1](#step-1-configure-your-user-flow) で作成したユーザー フローまたはカスタム ポリシーに置き換えます。 (例: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`)。 |
| b2cPolicies | authorityDomain|Azure AD B2C の[テナント名](tenant-management.md#get-your-tenant-name)。 (例: `contoso.onmicrosoft.com`)。 |
| 構成 | clientId | [手順 2.3](#23-register-the-angular-app) の Angular アプリケーション ID。 |
| protectedResources| endpoint| Web API の URL: `http://localhost:5000/api/todolist`。 |
| protectedResources| スコープ| [手順 2.2](#22-configure-scopes) で作成した Web API のスコープ (例: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`)。 |

完成した *src/app/auth-config.ts* のコードは、次のサンプルのようになります。

```typescript
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn,
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
    // More configuration here
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
```

## <a name="step-4-get-the-web-api-sample-code"></a>手順 4: Web API のサンプル コードを取得する

Web API を登録し、そのスコープを定義したので、Azure AD B2C テナントで動作するように Web API コードを構成します。

[\*.zip アーカイブをダウンロードする](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)か、GitHub からサンプルの Web API プロジェクトをクローンします。 また、次のコマンドを使用して、GitHub で [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) プロジェクトを直接参照することもできます。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-configure-the-web-api"></a>4.1 Web API を構成する

サンプル フォルダーの *config.json* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 Web API アプリではこの情報を使用して、Web アプリからベアラー トークンとして渡されるアクセス トークンが検証されます。 アプリ設定の次のプロパティを更新します。

|Section  |キー  |値  |
|---------|---------|---------|
|資格情報|tenantName| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分。 (例: `contoso`)。|
|資格情報|clientID| 手順 [2.1](#21-register-the-web-api-application) の Web API アプリケーション ID。 [前の図](#app-registration)では、これは **アプリ ID: 2** のアプリケーションです。|
|資格情報| 発行者| (省略可能) トークン発行者の `iss` クレームの値。 既定では、次の形式で Azure AD B2C からトークンが返されます: `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`。 `<your-tenant-name>` を、Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分に置き換えます。 `<your-tenant-ID>` を、[Azure AD B2C テナント ID](tenant-management.md#get-your-tenant-id) に置き換えます。 |
|policies|policyName|[手順 1](#step-1-configure-your-user-flow) で作成したユーザー フローまたはカスタム ポリシー。 アプリケーションで複数のユーザー フローまたはカスタム ポリシーを使用する場合は、1 つのみ指定します。 たとえば、サインアップまたはサインイン ユーザー フローを使用します。|
| resource| scope | [手順 2.5](#25-grant-permissions) の Web API アプリケーション登録のスコープ。 |

最終的な構成ファイルは、次の JSON のようになります。

```json
{
    "credentials": {
        "tenantName": "<your-tenant-namee>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    // More settings here
} 
```

## <a name="step-5-run-the-angular-spa-and-web-api"></a>手順 5: Angular SPA と Web API を実行する

これで、Angular の API へのスコープ付きアクセスをテストする準備ができました。 このステップでは、ローカル コンピューターで Web API とサンプル Angular の両方のアプリケーションを実行します。 その後、Angular アプリケーションにログインし、 **[TodoList]** ボタンを選択して、保護された API に対する要求を開始します。

### <a name="run-the-web-api"></a>Web API を実行する

1. コンソール ウィンドウを開き、Web API サンプルが含まれるディレクトリに移動します。 次に例を示します。

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

### <a name="run-the-angular-application"></a>Angular アプリケーションを実行する

1. 別のコンソール ウィンドウを開き、Angular サンプルが含まれるディレクトリに移動します。 次に例を示します。

    ```console
    cd ms-identity-javascript-angular-tutorial-main/3-Authorization-II/2-call-api-b2c/SPA
    ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    npm start
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 4200...
    ```

1. アプリケーションを表示するには、ブラウザーで `http://localhost:4200` に移動します。
1. **[ログイン]** を選択します。

    ![Angular サンプル アプリとログイン リンクを示すスクリーンショット。](./media/configure-authentication-sample-angular-spa-app/sample-app-sign-in.png)

1. サインアップまたはログイン プロセスを完了します。
1. ログインが成功すると、プロファイルが表示されます。 メニューの **[TodoList]** を選択します。

    ![Angular サンプル アプリとユーザー プロファイルおよび To Do リストの呼び出しを示すスクリーンショット。](./media/configure-authentication-sample-angular-spa-app/sample-app-result.png)

1. **[追加]** を選択して新しい項目を一覧に追加するか、アイコンを使用して項目を削除または編集します。

    ![Angular サンプル アプリの To Do リストの呼び出しを示すスクリーンショット。](./media/configure-authentication-sample-angular-spa-app/sample-app-calls-web-api.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする 

運用アプリケーションでは、通常、アプリ登録のリダイレクト URI は、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ

* [コード サンプルの詳細を確認する](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)
* [独自の Angular アプリケーションで認証を有効にする](enable-authentication-angular-spa-app.md)
* [Angular アプリケーションで認証オプションを構成する](enable-authentication-angular-spa-app-options.md)
* [ユーザー自身の Web API で認証を有効にする](enable-authentication-web-api.md)
