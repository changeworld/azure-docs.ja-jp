<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
	description="Azure Active Directory B2C を使用して Web API を呼び出す Web アプリケーションを構築する方法。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure AD B2C: .NET Web アプリから Web API を呼び出す

Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順で Web アプリや Web API に追加できます。この記事では、ベアラー トークンを使用して Web API を呼び出す .NET Model-View-Controller (MVC) の "To-Do List" Web アプリを作成する方法について説明します。

この記事では、Azure AD B2C でサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。まだ Azure AD B2C の基本を理解していない場合は、[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)で学習してください。

## Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md)してください。

## アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。ここでは、Web アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。次を行ってください。

- アプリケーションに **Web アプリまたは Web API** を含めます。
- **[応答 URL]** に「`https://localhost:44316/`」と入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリに割り当てられた**アプリケーション ID** をコピーしておきます。この情報も後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。この Web アプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として **[表示名]** と **[オブジェクト ID]** を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をコピーしておきます。名前には、`b2c_1_` というプレフィックスが付加されています。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成したら、アプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)から始めてください。

## コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet) で管理されています。手順に従ってサンプルを構築するには、[スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)します。スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。

## タスク Web アプリの構成

`TaskWebApp` が Azure AD B2C と通信できるようにするために、一般的なパラメーターをいくつか指定する必要があります。`TaskWebApp` プロジェクトで、プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を次の内容に置き換えます。`AadInstance`、`RedirectUri`、`TaskServiceUrl` の各値はそのまま使用できます。

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)] <appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" /> <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" /> <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="b2c\_1_sign\_up" /> <add key="ida:SignInPolicyId" value="b2c_1_sign\_in" /> <add key="ida:UserProfilePolicyId" value="b2c_1\_edit\_profile" /> <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" /> </appSettings>

## アクセス トークンの取得とタスク API の呼び出し

このセクションでは、Azure AD B2C で保護されている Web API にアクセスするため、Azure AD B2C でサインイン中に受信したトークンを使用する方法について説明します。

この記事では、API のセキュリティ保護方法の詳細は説明しません。Azure AD B2C を使用して、Web API で要求を安全に認証する方法については、[Web API の概要についての記事](active-directory-b2c-devquickstarts-api-dotnet.md)を参照してください。

### サインイン トークンを保存する

まずは、いずれかのポリシーを使用してユーザーを認証し、Azure AD B2C からサインイン トークンを受信します。ポリシーの実行方法がよくわからない場合は、[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)で Azure AD B2C の基本を確認してください。

ファイル `App_Start\Startup.Auth.cs` を開きます。`OpenIdConnectAuthenticationOptions` への重要な変更箇所が 1 点あり、`SaveSignInToken = true` を設定する必要があります。

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### コントローラーでトークンを取得する

`TasksController` は Web API と通信する役割を担い、HTTP 要求を API に送信してタスクの読み取り、作成、および削除を行います。API は Azure AD B2C によって保護されているため、まずは前の手順で保存したトークンを取得する必要があります。

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
	...
}
```

`BootstrapContext` には、B2C ポリシーの 1 つを実行して取得したサインイン トークンが含まれます。

### Web API からのタスク読み取り

トークンを取得したら、`Authorization` ヘッダーの HTTP `GET` 要求にトークンをアタッチして、`TaskService` を安全に呼び出すことができます。

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### Web API でのタスクの作成および削除

`POST` 要求と `DELETE` 要求を Web API に送信する場合と同じパターンに従い、`BootstrapContext` を使用してサインイン トークンを取得します。作成アクションは既に実装されています。`TasksController.cs` で削除アクションを完成させてみてください。

## サンプル アプリを実行する

最後に、アプリをビルドして実行します。サインアップおよびサインインを行い、サインインしているユーザーのタスクを作成します。サインアウトし、別のユーザーとしてサインインします。そのユーザーのタスクを作成します。API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したトークンからユーザー ID を抽出したためです。

参照用に、完成したサンプルが[ここに .zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)。GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->