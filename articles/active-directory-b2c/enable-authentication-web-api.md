---
title: Azure Active Directory B2C を使用して Web API で認証を有効にする
description: この記事では、Azure Active Directory B2C を使用して Web API を保護する方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 38a60dee9d4a5b7c0516b8e3278e848e0be26c2e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424441"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して独自の Web API で認証を有効にする

Web API へのアクセスを認可するには、Azure Active Directory B2C (Azure AD B2C) によって発行された有効なアクセス トークンが含まれる要求のみを処理します。 この記事では、Web API に対して Azure AD B2C を有効にする方法を示します。 この記事の手順を完了すると、有効なアクセス トークンを取得したユーザーのみが、Web API エンドポイントの呼び出しを認可されるようになります。  

## <a name="prerequisites"></a>前提条件

開始する前に、Web API を呼び出すアプリの認証を構成する方法について説明している、以下のいずれかの記事を読みます。 次に、この記事の手順に従って、サンプル Web API を独自の Web API に置き換えます。   

- [サンプルの ASP.NET Core アプリケーションで認証を構成する](configure-authentication-sample-web-app-with-api.md)
- [サンプルのシングルページ アプリケーション (SPA) で認証を構成する](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>概要

トークンベースの認証によって、Web API への要求に、有効なアクセス トークンが確実に含まれるようにします。 

アプリでは以下が実行されます。

1. Azure AD B2C を使用してユーザーを認証します。
1. Web API エンドポイントで必要とされるアクセス許可 (スコープ) を持つアクセス トークンを取得します。
1. HTTP 要求の認証ヘッダーで、次の形式を使用してベアラー トークンとしてのアクセス トークンを渡します。 

    ```http
    Authorization: Bearer <token>
    ```    

Web API では以下が実行されます。

1. HTTP 要求の認可ヘッダーからベアラー トークンを読み取ります。

1. トークンを検証します。 
1. トークン内のアクセス許可 (スコープ) を検証します。
1. トークン内にエンコードされている要求を読み取ります (省略可能)。
1. HTTP 要求に応答します。 

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録する必要があります。  

- "*Web、モバイル、または SPA アプリケーション*" の登録により、アプリで Azure AD B2C を使用してサインインできるようになります。 アプリの登録プロセスによって、アプリケーションを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます (たとえば "*アプリ ID: 1*")。

- *Web API* を登録すると、アプリは保護された Web API を呼び出せるようになります。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別する "*アプリケーション ID*" が生成されます (たとえば "*アプリ ID: 2*")。 アプリ (アプリ ID: 1) のアクセス許可を Web API スコープ (アプリ ID: 2) に付与します。 

次の図で、アプリケーションの登録とアプリケーション アーキテクチャについて説明しています。

![Web API を使用するアプリの、アプリケーション登録とアプリケーション アーキテクチャの図。](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>開発環境を準備する  

以降のセクションでは、新しい Web API プロジェクトを作成してゆきます。 プログラミング言語として ASP.NET Core または Node.js を選択してください。 次のいずれかを実行しているコンピューターがあることを確認してください。 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (最新バージョン)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/)、または別のコード エディター
* [Node.js ランタイム](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>手順 1: 保護された Web API を作成する

新しい Web API プロジェクトを作成します。 まず、使用するプログラミング言語として、**ASP.NET Core** または **Node.js** を選択します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

コマンド [`dotnet new`](/dotnet/core/tools/dotnet-new) を使用します。 `dotnet new` コマンドで、Web API プロジェクトのアセットが含まれる *TodoList* という名前の新しいフォルダーを作成します。 ディレクトリを開いてから、[Visual Studio Code](https://code.visualstudio.com/) を開きます。 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

"必要な資産をプロジェクトに追加する" よう求めるダイアログが表示されたら、 **[はい]** を選択します。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

[Node.js](https://nodejs.org/) の場合は [Express](https://expressjs.com/) を使用して Web API を作成します。 Web API を作成するには、以下を実行します。

1. *TodoList* という名前の新しいフォルダーを作成します。 
1. *TodoList* フォルダーに、*app.js* という名前のファイルを作成します。
1. コマンド シェルで `npm init -y` を実行します。 このコマンドを実行すると、Node.js プロジェクト用に既定の *package.json* ファイルが作成されます。
1. コマンド シェルで `npm install express` を実行します。 このコマンドで、Express フレームワークがインストールされます。

--- 

## <a name="step-2-install-the-dependencies"></a>手順 2: 依存関係をインストールする

認証ライブラリを Web API プロジェクトに追加します。 認証ライブラリでは、HTTP 認証ヘッダーの解析、トークンの検証、要求の抽出が行われます。 詳細については、このライブラリのドキュメントを確認してください。


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

認証ライブラリを追加するには、次のコマンドを実行してパッケージをインストールします。

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

認証ライブラリを追加するには、次のコマンドを実行してパッケージをインストールします。

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
[morgen パッケージ](https://www.npmjs.com/package/morgan)は、Node.js 用の HTTP 要求ロガー ミドルウェアです。

---

## <a name="step-3-initiate-the-authentication-library"></a>手順 3: 認証ライブラリを開始する

認証ライブラリを開始するために必要なコードを追加します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*Startup.cs* を開いてから、クラスの先頭に以下の `using` 宣言を追加します。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

`ConfigureServices(IServiceCollection services)` 関数を検索します。 次に、コードの `services.AddControllers();` 行の前に以下のコード スニペットを追加します。


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

`Configure` 関数を検索します。 次に、コードの `app.UseRouting();` 行の直後に以下のコード スニペットを追加します。


```csharp
app.UseAuthentication();
```

変更後、コードは次のスニペットのようになっているはずです。

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

*app.js* ファイルに以下の JavaScript コードを追加します。

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>手順 4: エンドポイントを追加する

Web API に 2 つのエンドポイントを追加します。

- 匿名の `/public` エンドポイント。 このエンドポイントからは、現在の日付と時刻が返されます。 これは、匿名呼び出しを使用して Web API をデバッグするために使用します。
- 保護された `/hello` エンドポイント。 このエンドポイントからは、アクセス トークン内の `name` 要求の値が返されます。

**匿名エンドポイントを追加するには:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*/Controllers* フォルダーに *PublicController.cs* ファイルを追加してから、それに以下のコード スニペットを追加します。

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

*app.js* ファイルに以下の JavaScript コードを追加します。


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**保護されたエンドポイントを追加するには:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*/Controllers* フォルダーに *HelloController.cs* ファイルを追加してから、それに以下のコードを追加します。

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

`HelloController` コントローラーは、認証されたユーザーにのみアクセスを制限する [AuthorizeAttribute](/aspnet/core/security/authorization/simple) で修飾されます。 

コントローラーは、`[RequiredScope("tasks.read")]` によっても装飾されます。 [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) を指定すると、Web API が適切なスコープ `tasks.read` を使用して呼び出されたことが確認されます。 

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

*app.js* ファイルに以下の JavaScript コードを追加します。 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

`/hello` エンドポイントでは、最初に `passport.authenticate()` 関数が呼び出されます。 この認証関数により、認証されたユーザーのみにアクセスが制限されます。 

この認証関数では、Web API が適切なスコープで呼び出されたことも確認します。 許可されるスコープは[構成ファイル](#step-6-configure-the-web-api)に記載されています。 

--- 

## <a name="step-5-configure-the-web-server"></a>手順 5: Web サーバーを構成する

開発環境で、受信 HTTP または HTTPS 要求のポート番号をリッスンするように Web API を設定します。 この例では、HTTP ポート 6000 と HTTPS ポート 6001 を使用します。 Web API のベース URI は、HTTP の場合は `http://localhost:6000`、HTTPS の場合は `https://localhost:6001` になります。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*appsettings.json* ファイルに次の JSON スニペットを追加します。 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      },
      "Https": {
         "Url": "https://localhost:6001"   
        }
    }
  }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

*app.js* ファイルに次の JavaScript コードを追加します。 [Node アプリケーションに対して HTTP および HTTPS エンドポイントを設定](https://github.com/expressjs/express/wiki/Migrating-from-2.x-to-3.x#application-function)できます。 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>手順 6: Web API アプリを構成する

構成ファイルに構成を追加します。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれます。 Web API アプリではこの情報を使用して、Web アプリからベアラー トークンとして渡されるアクセス トークンが検証されます。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

プロジェクトのルート フォルダーの下にある *appsettings.json* ファイルを開いてから、以下の設定を追加します。

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

*appsettings.json* ファイルで、以下のプロパティを更新します。 

|Section  |キー  |値  |
|---------|---------|---------|
|AzureAdB2C|インスタンス| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `https://contoso.b2clogin.com`)。|
|AzureAdB2C|Domain| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。|
|AzureAdB2C|ClientId| Web API アプリケーション ID。 [前の図](#app-registration-overview)で、これは *アプリ ID: 2* であるアプリケーションでした。 Web API アプリケーションの登録 ID を取得する方法については、「[前提条件](#prerequisites)」を参照してください。 |
|AzureAdB2C|SignUpSignInPolicyId|ユーザー フローまたはカスタム ポリシー。 ユーザー フローまたはポリシーを取得する方法については、「[前提条件](#prerequisites)」を参照してください。  |

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

プロジェクトのルート フォルダーに *config.json* ファイルを作成してから、それに以下の JSON スニペットを追加します。  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

*config.json* ファイルで、以下のプロパティを更新します。

|Section  |キー  |値  |
|---------|---------|---------|
| 資格情報 | tenantName | Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `contoso`)。|
| 資格情報 |clientID | Web API アプリケーション ID。 [前の図](#app-registration-overview)で、これは *アプリ ID: 2* であるアプリケーションでした。 Web API アプリケーションの登録 ID を取得する方法については、「[前提条件](#prerequisites)」を参照してください。 |
| 資格情報 | 発行者| トークン発行者の `iss` 要求の値。 既定では、次の形式で Azure AD B2C からトークンが返されます: `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`。 `<your-tenant-name>` を、Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分に置き換えます。 `<your-tenant-ID>` を、[Azure AD B2C テナント ID](tenant-management.md#get-your-tenant-id) に置き換えます。 |
| policies | policyName | ユーザー フローまたはカスタム ポリシー。 ユーザー フローまたはポリシーを取得する方法については、「[前提条件](#prerequisites)」を参照してください。|
| resource | scope | Web API アプリケーション登録のスコープ。 Web API のスコープを取得する方法については、「[前提条件](#prerequisites)」を参照してください。|

---

## <a name="step-7-run-and-test-the-web-api"></a>手順 7: Web API を実行してテストする

最後に、Azure AD B2C 環境の設定を使用して Web API を実行します。 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

コマンド シェルで、次のコマンドを実行して Web アプリを起動します。

```bush
 dotnet run
```

次の出力が表示されるはずです。これは、アプリが起動して実行中であり、要求を受信する準備ができていることを意味します。

```
Now listening on: http://localhost:6000
```

プログラムを停止するには、コマンド シェルで Ctrl + C キーを押します。 `node app.js` コマンドを使用するとアプリを再実行できます。

> [!TIP]
> または、`dotnet run` コマンドを実行するために、[Visual Studio Code デバッガー](https://code.visualstudio.com/docs/editor/debugging)を使用できます。 Visual Studio Code の組み込みデバッガーを使用すると、編集、コンパイル、デバッグのループを加速できます。

ブラウザーを開き、`http://localhost:6000/public` に移動します。 ブラウザー ウィンドウには、現在の日付と時刻と共に、次のテキストが表示されるはずです。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

コマンド シェルで、次のコマンドを実行して Web アプリを起動します。

```bush
node app.js
```

次の出力が表示されるはずです。これは、アプリが起動して実行中であり、要求を受信する準備ができていることを意味します。

```
Example app listening on port 6000!
```

プログラムを停止するには、コマンド シェルで Ctrl + C キーを押します。 `node app.js` コマンドを使用するとアプリを再実行できます。

> [!TIP]
> または、`node app.js` コマンドを実行するために、[Visual Studio Code デバッガー](https://code.visualstudio.com/docs/editor/debugging)を使用します。 Visual Studio Code の組み込みデバッガーを使用すると、編集、コンパイル、デバッグのループを加速できます。

ブラウザーを開き、`http://localhost:6000/public` に移動します。 ブラウザー ウィンドウには、現在の日付と時刻と共に、次のテキストが表示されるはずです。

---

## <a name="step-8-call-the-web-api-from-your-app"></a>手順 8: アプリから Web API を呼び出す

アクセス トークンを使用せずに、保護された Web API エンドポイントの呼び出しを試みます。 ブラウザーを開き、`http://localhost:6000/hello` に移動します。 API から、許可されていない HTTP のエラー メッセージが返されるので、Web API がベアラー トークンによって保護されていることを確認できます。

Web API を呼び出すために引き続きアプリを構成します。 ガイダンスについては、「[前提条件](#prerequisites)」セクションを参照してください。

Azure AD B2C を API と統合する場合のベスト プラクティスについては、このビデオをご覧ください。

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>次のステップ

完全な例については GitHub で入手してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* [Microsoft ID ライブラリ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService)を使用して Web API を取得します。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)
* [Passport.js ライブラリ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)を使用して Web API を取得します。
