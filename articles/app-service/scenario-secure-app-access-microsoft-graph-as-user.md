---
title: チュートリアル - Web アプリからユーザーとして Microsoft Graph にアクセスする | Azure
description: このチュートリアルでは、サインインしているユーザーに代わって Microsoft Graph のデータにアクセスする方法について説明します。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: b20ae4e6cec7ad3ce710c6e9670ff580b30f2638
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462312"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>チュートリアル:セキュリティで保護されたアプリからユーザーとして Microsoft Graph にアクセスする

Azure App Service で実行されている Web アプリから Microsoft Graph にアクセスする方法について説明します。

:::image type="content" alt-text="Microsoft Graph へのアクセスを示す図" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Web アプリから Microsoft Graph へのアクセスを追加し、サインインしているユーザーとしてなんらかのアクションを実行するとします。 このセクションでは、委任されたアクセス許可を Web アプリに付与し、サインインしているユーザーのプロファイル情報を Azure Active Directory (Azure AD) から取得する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 委任されたアクセス許可を Web アプリに付与する。
> * サインインしているユーザーに代わって Web アプリから Microsoft Graph を呼び出す。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [App Service の認証および承認モジュールが有効になっている](scenario-secure-app-authentication-app-service.md) Azure App Service で実行されている Web アプリケーション。

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Microsoft Graph を呼び出すためのアクセスをフロントエンドに許可する

Web アプリで認証と承認を有効にしたので、その Web アプリは Microsoft ID プラットフォームに登録され、Azure AD アプリケーションでサポートされます。 この手順では、ユーザーに代わって Microsoft Graph にアクセスするためのアクセス許可を Web アプリに付与します (技術的には、ユーザーに代わって Microsoft Graph の Azure AD アプリケーションにアクセスするためのアクセス許可を Web アプリの Azure AD アプリケーションに付与します)。

[[Azure portal]](https://portal.azure.com) メニューで **[Azure Active Directory]** を選択するか、任意のページから **[Azure Active Directory]** を検索して選択します。

**[アプリの登録]**  >  **[所有しているアプリケーション]**  >  **[View all applications in this directory]\(このディレクトリのすべてのアプリケーションを表示\)** の順に選択します。 対象の Web アプリ名を選択し、 **[API のアクセス許可]** を選択します。

**[アクセス許可の追加]** を選択し、Microsoft API と Microsoft Graph を選択します。

**[委任されたアクセス許可]** を選択し、一覧から **[User.Read]** を選択します。 **[アクセス許可の追加]** を選択します.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>使用可能なアクセス トークンを返すように App Service を構成する

これで、サインインしているユーザーとして Microsoft Graph にアクセスするために必要なアクセス許可が Web アプリに付与されました。 この手順では、Microsoft Graph にアクセスする場合に使用可能なアクセス トークンが提供されるように、App Service の認証および承認を構成します。 この手順では、ダウンストリーム サービス (Microsoft Graph) の User.Read スコープを追加する必要があります: `https://graph.microsoft.com/User.Read`。

> [!IMPORTANT]
> 使用可能なアクセス トークンを返すように App Service を構成していない場合、コードで Microsoft Graph API を呼び出したときに ```CompactToken parsing failed with error code: 80049217``` エラーが発生します。

# <a name="azure-resource-explorer"></a>[Azure Resource Explorer](#tab/azure-resource-explorer)
[Azure Resource Explorer](https://resources.azure.com/) に移動し、リソース ツリーを使用して対象の Web アプリを見つけます。 リソース URL は、`https://resources.azure.com/subscriptions/subscriptionId/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914` のようになります。

リソース ツリーで対象の Web アプリが選択された状態で、Azure Resource Explorer が開きます。 ページの上部にある **[読み取り/書き込み]** を選択して、Azure リソースの編集を有効にします。

左側のブラウザーで、**config** > **authsettingsV2** にドリルダウンします。

**[authsettingsV2]** ビューで、 **[編集]** を選択します。 **identityProviders** -> **azureActiveDirectory** の **[ログイン]** セクションを見つけ、次の **loginParameters** 設定 (`"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`) を追加します。

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

**[PUT]** を選択して設定を保存します。 この設定が有効になるまでに数分かかる場合があります。 これで、適切なアクセス トークンを使用して Microsoft Graph にアクセスするように Web アプリが構成されました。 これを行わないと、Microsoft Graph から、コンパクトなトークンの形式が正しくないことを示すエラーが返されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して App Service Web App REST API を呼び出し、構成設定の[取得](/rest/api/appservice/web-apps/get-auth-settings)と[更新](/rest/api/appservice/web-apps/update-auth-settings)を行い、Web アプリから Microsoft Graph を呼び出せるようにします。 コマンド ウィンドウを開き、Azure CLI にログインします。

```azurecli
az login
```

既存の "config/authsettingsv2" 設定を取得し、ローカル *authsettings.json* ファイルに保存します。

```azurecli
az rest --method GET --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2/list?api-version=2020-06-01' > authsettings.json
```

好みのテキスト エディターを使用して、authsettings.json ファイルを開きます。 **identityProviders** -> **azureActiveDirectory** の **[ログイン]** セクションを見つけ、次の **loginParameters** 設定 (`"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`) を追加します。

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

変更を *authsettings.json* ファイルに保存し、ローカル設定を Web アプリにアップロードします。

```azurecli
az rest --method PUT --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2?api-version=2020-06-01' --body @./authsettings.json
```
---

## <a name="call-microsoft-graph"></a>Microsoft Graph の呼び出し

Web アプリに必要なアクセス許可が付与され、さらに Microsoft Graph のクライアント ID がログイン パラメーターに追加されました。

# <a name="c"></a>[C#](#tab/programming-language-csharp)
Web アプリは、[Microsoft.Identity.Web ライブラリ](https://github.com/AzureAD/microsoft-identity-web/)を使用して、Microsoft Graph による認証のためにアクセス トークンを取得します。 バージョン 1.2.0 以降の Microsoft.Identity.Web ライブラリは、App Service 認証および承認モジュールと統合され、一緒に実行できます。 Microsoft.Identity.Web は、この Web アプリが App Service でホストされていることを検出すると、App Service の認証および承認モジュールからアクセス トークンを取得します。 その後、このアクセス トークンは、Microsoft Graph API を使用して、認証された要求に渡されます。

このコードをサンプル アプリケーションの一部として見る場合は、[GitHub 上のサンプル](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf)を参照してください。

> [!NOTE]
> Web アプリで基本認証および承認を使用する場合、または Microsoft Graph を使用して要求を認証する場合には、Microsoft.Identity.Web ライブラリは不要です。 App Service の認証および承認モジュールだけを有効にした状態で、[ダウンストリームの API を安全に呼び出す](tutorial-auth-aad.md#call-api-securely-from-server-code)ことができます。
> 
> ただし、App Service の認証および承認は、さらに多くの基本認証シナリオ向けに設計されています。 より複雑なシナリオ (カスタム要求の処理など) では、Microsoft.Identity.Web ライブラリまたは [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) が必要です。 最初は設定および構成作業がもう少しありますが、Microsoft.Identity.Web ライブラリは App Service の認証および承認モジュールと並行して実行できます。 後で Web アプリでより複雑なシナリオへの対応が必要になったときに、App Service の認証および承認モジュールを無効にすることができ、Microsoft.Identity.Web は既にアプリの一部になっています。

### <a name="install-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

.NET Core コマンド ライン インターフェイスまたは Visual Studio のパッケージ マネージャー コンソールを使用して、[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) および [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet パッケージをプロジェクトにインストールします。

#### <a name="net-core-command-line"></a>.NET Core コマンド ライン

コマンド ラインを開き、プロジェクト ファイルが含まれているディレクトリに切り替えます。

インストール コマンドを実行します。

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

#### <a name="package-manager-console"></a>パッケージ マネージャー コンソール

Visual Studio でプロジェクトまたはソリューションを開き、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** コマンドを使用してコンソールを開きます。

インストール コマンドを実行します。
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

### <a name="startupcs"></a>Startup.cs

*Startup.cs* ファイルの ```AddMicrosoftIdentityWebApp``` メソッドにより、Microsoft.Identity.Web が対象の Web アプリに追加されます。 ```AddMicrosoftGraph``` メソッドにより、Microsoft Graph のサポートが追加されます。

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* では、Microsoft.Identity.Web ライブラリの構成を指定します。 [Azure portal](https://portal.azure.com) で、ポータル メニューから **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。 App Service の認証および承認モジュールを有効にしたときに作成されたアプリの登録を選択します (アプリの登録には、Web アプリと同じ名前が付けられています)。テナント ID とクライアント ID は、アプリの登録の概要ページで確認できます。 ドメイン名は、テナントの Azure AD の概要ページで確認できます。

*Graph* では、Microsoft Graph エンドポイントと、アプリに必要な初期のスコープを指定します。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

次の例は、サインインしているユーザーとして Microsoft Graph を呼び出し、ユーザー情報を取得する方法を示しています。 ```GraphServiceClient``` オブジェクトがコントローラーに挿入され、Microsoft.Identity.Web ライブラリによって認証が構成されています。

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/programming-language-nodejs)

Web アプリでは、受信した要求のヘッダーからユーザーのアクセス トークンを取得して Microsoft Graph クライアントに渡し、認証された要求を `/me` エンドポイントに行います。

このコードをサンプル アプリケーションの一部として見る場合は、[GitHub のサンプル](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf)で *graphController.js* をご覧ください。

```nodejs
const graphHelper = require('../utils/graphHelper');

// Some code omitted for brevity.

exports.getProfilePage = async(req, res, next) => {

    try {
        const graphClient = graphHelper.getAuthenticatedClient(req.session.protectedResources["graphAPI"].accessToken);

        const profile = await graphClient
            .api('/me')
            .get();

        res.render('profile', { isAuthenticated: req.session.isAuthenticated, profile: profile, appServiceName: appServiceName });   
    } catch (error) {
        next(error);
    }
}
```

Microsoft Graph に対してクエリを実行するには、[Microsoft Graph JavaScript SDK](https://github.com/microsoftgraph/msgraph-sdk-javascript) を使用します。 このコードは [utils/graphHelper.js](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/blob/main/2-WebApp-graphapi-on-behalf/utils/graphHelper.js) にあります。

```nodejs
const graph = require('@microsoft/microsoft-graph-client');

// Some code omitted for brevity.

getAuthenticatedClient = (accessToken) => {
    // Initialize Graph client
    const client = graph.Client.init({
        // Use the provided access token to authenticate requests
        authProvider: (done) => {
            done(null, accessToken);
        }
    });

    return client;
}
```
---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了し、Web アプリや関連するリソースが不要になった場合は、[作成したリソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * 委任されたアクセス許可を Web アプリに付与する。
> * サインインしているユーザーに代わって Web アプリから Microsoft Graph を呼び出す。

> [!div class="nextstepaction"]
> [アプリ サービスからアプリとして Microsoft Graph にアクセスする](scenario-secure-app-access-microsoft-graph-as-app.md)
