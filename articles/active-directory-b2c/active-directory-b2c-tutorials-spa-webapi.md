---
title: チュートリアル - Azure Active Directory B2C を使用してシングルページ アプリから ASP.NET Core Web API へのアクセスを許可する | Microsoft Docs
description: Active Directory B2C を使用して .NET Core Web API を保護し、シングルページ アプリからそれを呼び出す方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: a2fbdebfc800c33a99b19b366209aeabb03fe115
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590835"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用してシングルページ アプリから ASP.NET Core Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory (Azure AD) B2C で保護された ASP.NET Core Web API リソースをシングルページ アプリから呼び出す方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントに Web API を登録する
> * Web API のスコープを定義および構成する
> * Web API に対するアクセス許可をアプリに付与する
> * Azure AD B2C を使用して Web API を保護するようにサンプル コードを更新する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [シングルページ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-spa.md)を完了する。
* **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 以降
* [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Web API の登録

Web API リソースは、Azure Active Directory からの[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)を表す[クライアント アプリケーション](../active-directory/develop/developer-glossary.md#client-application)で[保護されたリソース要求](../active-directory/develop/developer-glossary.md#resource-server)を受け取って処理できるように、テナントに登録しておく必要があります。 登録により、テナント内に[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/developer-glossary.md#application-object)が確立されます。 

Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal のサービス一覧から **[Azure AD B2C]** を選択します。

2. B2C の設定で、**[アプリケーション]** をクリックし、**[追加]** をクリックします。

    テナントにサンプル Web API を登録するには、以下の設定を使用します。
    
    ![新しい API の追加](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | Hello Core API | 開発者が Web API を把握できる**名前**を入力します。 |
    | **Web アプリ/Web API を含める** | [はい] | Web API の場合は **[はい]** を選択します。 |
    | **暗黙的フローを許可する** | [はい] | API では [OpenID Connect サインイン](active-directory-b2c-reference-oidc.md)が使用されるため、**[はい]** を選択します。 |
    | **応答 URL** | `http://localhost:44332` | 応答 URL は、API が要求したトークンを Azure AD B2C が返すエンドポイントです。 このチュートリアルでは、サンプル Web API はローカル (localhost) で実行され、ポート 5000 でリッスンします。 |
    | **アプリケーション ID/URI** | HelloCoreAPI | この URI は、テナント内の API を一意に識別します。 これにより、テナントごとに複数の API を登録することができます。 [スコープ](../active-directory/develop/developer-glossary.md#scopes)は、保護された API リソースへのアクセスを統制し、App ID URI ごとに定義されます。 |
    | **ネイティブ クライアント** | いいえ  | これはネイティブ クライアントではなく Web API のため、[いいえ] を選択します。 |
    
3. **[作成]** をクリックして API を登録します。

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

![Web API のプロパティ](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID は API を一意に識別するため、このチュートリアルの後半で API を構成する際に必要になります。

Azure AD B2C に Web API を登録すると、信頼関係が定義されます。 API が B2C に登録されるため、API は他のアプリケーションから受け取る B2C アクセス トークンを信頼できるようになりました。

## <a name="define-and-configure-scopes"></a>スコープの定義と構成

[スコープ](../active-directory/develop/developer-glossary.md#scopes)を使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りアクセス許可を定義します。

### <a name="define-scopes-for-the-web-api"></a>Web API のスコープの定義

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

**[公開済みスコープ (プレビュー)]** をクリックします。

API のスコープを構成するには、次のエントリを追加します。 

![Web API で定義されているスコープ](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Setting      | 推奨値  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **スコープ** | demo.read | デモ API への読み取りアクセス |

**[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリに Web API へのアクセス許可を付与することができます。

### <a name="grant-app-permissions-to-web-api"></a>Web API へのアクセス許可をアプリに付与する

保護された Web API をアプリから呼び出すには、その API へのアクセス許可をアプリに付与する必要があります。 このチュートリアルでは、[シングルページ アプリケーション (JavaScript) における Azure Active Directory B2C を使用したユーザーの認証](active-directory-b2c-tutorials-spa.md)に関するページで作成されたシングルページ アプリを使用します。

1. Azure Portal で、サービスの一覧から **[Azure AD B2C]** を選択し、**[アプリケーション]** をクリックして登録済みアプリの一覧を表示します。

2. アプリの一覧から **[My sample single page app]** を選択し、**[API アクセス (プレビュー)]**、**[追加]** の順にクリックします。

3. **[API の選択]** ボックスの一覧で、登録した Web API **[Hello Core API]** を選択します。

4. **[スコープの選択]** ボックスの一覧で、Web API の登録で定義したスコープを選択します。

    ![アプリのスコープの選択](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Click **OK**.

**My sample single page app** は、保護された **Hello Core API** を呼び出すために登録されています。 ユーザーは、この WPF デスクトップ アプリを使用するために Azure AD B2C で[認証](../active-directory/develop/developer-glossary.md#authentication)を行います。 このデスクトップ アプリは、保護された Web API にアクセスするために、Azure AD B2C から[承認付与](../active-directory/develop/developer-glossary.md#authorization-grant)を取得します。

## <a name="update-code"></a>コードの更新

Web API が登録され、スコープを定義したら、Azure AD B2C テナントを使用するように Web API コードを構成する必要があります。 このチュートリアルでは、GitHub からダウンロードできるサンプル .NET Core Web アプリを構成します。 

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API を構成する

1. Visual Studio で **B2C-WebAPI.sln** ソリューションを開きます。

2. **appsettings.json** ファイルを開きます。 テナントを使用するように Web API を構成するには、次の値を更新します。

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>CORS を有効にする

シングルページ アプリに ASP.NET Core Web API の呼び出しを許可するには、[CORS](https://docs.microsoft.com/aspnet/core/security/cors) を有効にする必要があります。

1. **Startup.cs** の `ConfigureServices()` メソッドに CORS を追加します。

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. **Startup.cs** の `Configure()` メソッドで CORS を構成します。

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. **[プロパティ]** の **launchSettings.json** ファイルを開き、*applicationURL* 設定を探して、次のセクションで使用する値をメモします。

### <a name="configure-the-single-page-app"></a>シングルページ アプリを構成する

シングルページ アプリでは、ユーザーのサインアップ、サインイン、保護された ASP.NET Core Web API を呼び出しに Azure AD B2C を使用します。 NET Core Web API を呼び出すようにシングルページ アプリを更新する必要があります。
アプリの設定を変更するには、次の手順に従います。

1. Node.js シングルページ アプリのサンプルの `index.html` ファイルを開きます。
2. Azure AD B2C テナントの登録情報でサンプルを構成します。 次のコードで、テナント名を **b2cScopes** に追加し、**webApi** の値を、先ほどメモした *applicationURL* の値に変更します。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:64791/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>SPA アプリと Web API の実行

Node.js シングルページ アプリと .NET Core Web API の両方を実行する必要があります。

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core Web API を実行する 

Visual Studio で **F5** キーを押して **B2C-WebAPI.sln** ソリューションをデバッグします。

プロジェクトが起動すると、既定のブラウザーには、Web API を要求に使用できることを示す Web ページが表示されます。

### <a name="run-the-single-page-app"></a>シングルページ アプリを実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`。
3. 次のコマンドを実行します。
    ```
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、アプリがホストされている場所のポート番号が表示されます。
    
    ```
    Listening on port 6420...
    ```

4. ブラウザーを使用してアドレス `http://localhost:6420` に移動し、アプリを表示します。
5. [シングルページ アプリケーション (JavaScript) における Azure Active Directory B2C を使用したユーザーの認証](active-directory-b2c-tutorials-spa.md) に関するページで使用したメール アドレスとパスワードでサインインします。
6. **[Call API]** ボタンをクリックします。

ユーザー アカウントを使用してサインアップまたはサインインすると、このサンプルは、保護された Web API を呼び出して結果を返します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

この記事では、Azure AD B2C でスコープを登録および定義することで Web API を保護する方法について説明しました。 詳細については、利用可能な Azure AD B2C コード サンプルを参照してください。

> [!div class="nextstepaction"]
> [Azure AD B2C のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
