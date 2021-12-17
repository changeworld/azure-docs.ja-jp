---
title: Azure Active Directory B2C を使用して Web API を呼び出すサンプルの Web アプリケーションで認証を構成する
description: この記事では、Web API を呼び出す ASP.NET Web アプリケーションで、Azure Active Directory B2C を使用して、ユーザーをサインインおよびサインアップさせる方法について説明します。
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
ms.openlocfilehash: f2f0f00bfc3b5f551d60817e3ea52924c78d98d8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007789"
---
# <a name="configure-authentication-in-a-sample-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Web API を呼び出すサンプルの Web アプリで認証を構成する

この記事では、Web API を呼び出すサンプルの ASP.NET Web アプリケーションを使用して、Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

> [!IMPORTANT]
> この記事で参照されているサンプルの ASP.NET Web アプリは、ベアラー トークンを使用して Web API を呼び出すために使用されます。 Web API を呼び出さない Web アプリケーションについては、「[Azure AD B2C を使用してサンプル Web アプリで認証を構成する](configure-authentication-sample-web-app.md)」を参照してください。  

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 OIDC を使用して、ユーザーをアプリケーションに安全にサインインさせることができます。 この Web アプリのサンプルでは、[Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web) が使用されます。 Microsoft Identity Web は、セキュリティ保護された Web API を呼び出すことができる Web アプリへの認証と認可のサポートの追加を簡略化する ASP.NET Core ライブラリのセットです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーが Web アプリにアクセスして **[サインイン]** を選択します。
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)し、[パスワードをリセット](add-password-reset-policy.md)します。 [ソーシャル アカウント](add-identity-provider.md)を使用してサインインすることもできます。
1. ユーザーがサインインすると、Azure AD B2C からアプリに認可コードが返されます。
1. その後、アプリでは次が実行されます。
 
   a. 認可コードが、ID トークン、アクセス トークン、更新トークンと交換されます。  
   b. ID トークン クレームが読み取られて、アプリケーション認可 Cookie が永続化されます。  
   c. 後で使用できるように、更新トークンがメモリ内キャッシュに格納されます。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- *Web アプリケーション* の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、Azure AD B2C での認証が完了した後にユーザーが Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。 また、トークンを安全に取得するためにアプリによって使用される、"*クライアント シークレット*" を作成します。

- *Web API* の登録により、アプリではセキュリティで保護された Web API を呼び出すことができます。 登録には、Web API の "*スコープ*" が含まれます。 スコープを使用することで、Web API などの保護されたリソースへのアクセス許可を管理できます。 Web アプリケーションのアクセス許可を Web API のスコープに付与します。 アクセス トークンが要求されたら、アプリで必要なアクセス許可を要求の scope パラメーターに指定します。  

アプリのアーキテクチャと登録について、次の図に示します。

![Web API 呼び出しの登録とトークンが使用されている Web アプリの図。](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

次のいずれかを実行しているコンピューター: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code (最新バージョン)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>手順 2: Web アプリケーションを登録する

この手順では、Web アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="step-21-register-the-web-api-app"></a>手順 2.1: Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>手順 2.2: Web API アプリのスコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-web-app"></a>手順 2.3: Web アプリを登録する

Web アプリの登録を作成するには、次の手順を実行します。

1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** で、アプリケーションの名前を入力します (*webapp1* など)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Web]** を選択し、URL ボックスに「`https://localhost:5000/signin-oidc`」と入力します。
1. **[アクセス許可]** で、 **[Grant admin consent to openid and offline access permissions]\(OpenID とオフラインのアクセス許可に管理者の同意を与える\)** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web アプリケーション ID を記録するための Web アプリの [概要] ページのスクリーンショット。](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-create-a-web-app-client-secret"></a>手順 2.4: Web アプリのクライアント シークレットを作成する

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="step-25-grant-the-web-app-permissions-for-the-web-api"></a>手順 2.5: Web API に対するアクセス許可を Web アプリに付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>手順 3: Web アプリのサンプルを取得する

[zip ファイルをダウンロードする](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)か、次の Bash コマンドを実行して、GitHub からサンプル Web アプリケーションをクローンします。 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

パスの合計文字長が 260 以下のフォルダーにサンプル ファイルを展開します。

## <a name="step-4-configure-the-sample-web-api"></a>手順 4: サンプル Web API を構成する

サンプル フォルダー内の *4-WebApp-your-API/4-2-B2C/TodoListService* フォルダーにある **TodoListService.csproj** プロジェクトを Visual Studio または Visual Studio Code で開きます。 

プロジェクトのルート フォルダーで、*appsettings.json* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 Web API アプリではこの情報を使用して、Web アプリからベアラー トークンとして渡されるアクセス トークンが検証されます。 アプリ設定の次のプロパティを更新します。

| Section | キー | 値 |
| --- | --- | --- |
|AzureAdB2C|インスタンス| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分。 たとえば、「 `https://contoso.b2clogin.com` 」のように入力します。|
|AzureAdB2C|Domain| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name)。 たとえば、「 `contoso.onmicrosoft.com` 」のように入力します。|
|AzureAdB2C|ClientId| [手順 2.1](#step-21-register-the-web-api-app) の Web API アプリケーション ID。|
|AzureAdB2C|SignUpSignInPolicyId|ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|
| | | |

最終的な構成ファイルは、次の JSON ファイルのようになります。

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

### <a name="step-41-set-the-permission-policy"></a>手順 4.1: アクセス許可ポリシーを設定する

Web API により、ユーザーがベアラー トークンを使用して認証されたこと、および構成された承認済みのスコープがベアラー トークンに設定されていることが検証されます。 ベアラー トークンにこれらの受け入れられたスコープが設定されていない場合、Web API により HTTP 状態コード 403 (禁止) が返され、トークンで想定されているスコープを示すメッセージが応答本文に書き込まれます。 

受け入れられたスコープを構成するには、`Controller/TodoListController.cs` クラスを開き、スコープ名を完全な URI なしで設定します。

```csharp
[RequiredScope("tasks.read")]
```

### <a name="step-42-run-the-sample-web-api-app"></a>手順 4.2: サンプル Web API アプリを実行する

Web アプリで Web API サンプルが呼び出されるのを許可するには、次の手順に従って Web API を実行します。

1. 要求されている場合は、依存関係を復元します。
1. プロジェクトをビルドして実行します。
1. プロジェクトがビルドされると、Visual Studio または Visual Studio Code により、アドレス https://localhost:44332 を使用してブラウザーで Web API が開始されます。

## <a name="step-5-configure-the-sample-web-app"></a>手順 5: サンプル Web アプリを構成する

サンプル フォルダー内の `4-WebApp-your-API/4-2-B2C/Client` フォルダーにある **TodoListClient.csproj** プロジェクトを Visual Studio または Visual Studio Code で開きます。 

プロジェクト ルート フォルダーで、`appsettings.json` ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 Web アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して、それらを検証します。 アプリ設定の次のプロパティを更新します。

| Section | キー | 値 |
| --- | --- | --- |
| AzureAdB2C | インスタンス | Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `https://contoso.b2clogin.com`)。|
|AzureAdB2C|Domain| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。|
|AzureAdB2C|ClientId| [手順 2.3](#step-23-register-the-web-app) の Web アプリケーション ID。|
|AzureAdB2C | ClientSecret | [手順 2.4](#step-24-create-a-web-app-client-secret) の Web アプリケーション シークレット。 | 
|AzureAdB2C|SignUpSignInPolicyId|ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|
| TodoList | TodoListScope | [手順 2.5](#step-25-grant-the-web-app-permissions-for-the-web-api) で作成した Web API スコープ。|
| TodoList | TodoListBaseAddress | Web API のベース URI (例: `https://localhost:44332`)。 |
| | | |

最終的な構成ファイルは、次の JSON のようになります。

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>手順 6: サンプル Web アプリを実行する

1. プロジェクトをビルドして実行します。
1. [http://.azurewebsites.net/admin](`https://localhost:5000`) を参照します。 
1. サインインアップまたはサインイン プロセスを完了します。

認証が成功すると、ナビゲーション バーに表示名が表示されます。 Azure AD B2C トークンによってアプリに返されるクレームを表示するには、 **[TodoList]** を選択します。

![Web アプリ トークン クレームのスクリーンショット。](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>アプリケーションをデプロイする

運用アプリケーションでは、通常、アプリ登録のリダイレクト URI は、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com/signin-oidc` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

### <a name="token-cache-for-a-web-app"></a>Web アプリのトークン キャッシュ

Web アプリのサンプルでは、メモリ内のトークン キャッシュのシリアル化が使用されています。 この実装は、サンプルにおいて非常に有用です。 また、Web アプリの再起動時にトークン キャッシュが失われても構わない場合は、実稼働アプリケーションにも適しています。 

運用環境では、分散メモリ キャッシュを使用することをお勧めします。 たとえば、Redis Cache、NCache、SQL Server キャッシュなどです。 分散メモリ キャッシュの実装の詳細については、「[トークン キャッシュのシリアル化](../active-directory/develop/msal-net-token-cache-serialization.md)」を参照してください。


## <a name="next-steps"></a>次の手順

* [コード サンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)についてさらに確認する。
* [Azure AD B2C を使用して独自の Web アプリケーションで認証を有効にする](enable-authentication-web-application.md)方法について確認する。
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)方法について確認する。
