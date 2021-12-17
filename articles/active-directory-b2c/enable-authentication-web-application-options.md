---
title: Azure Active Directory B2C を使用して Web アプリ認証オプションを有効にする
description: この記事では、Web アプリの認証オプションを有効にするいくつかの方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 097422d3d3156f42b55374593aaced8514ae2d09
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040856"
---
# <a name="enable-authentication-options-in-a-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Web アプリの認証オプションを有効にする 

この記事では、Web アプリケーションの Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスの有効化、カスタマイズ、および拡張を行う方法について説明します。 

開始する前に、次の記事をよく理解しておくことが重要です。 
* [サンプル Web アプリで認証を構成する](configure-authentication-sample-web-app.md)
* [独自の Web アプリで認証を有効にする](enable-authentication-web-application.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でカスタム ドメインとテナント ID を使用するには、[カスタム ドメインを有効にする](custom-domain.md)方法に関するページのガイダンスに従ってください。 プロジェクトのルート フォルダーで、*appsettings.json* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。

*appsettings.json* ファイルで、次の操作を行います。

- `Instance` エントリをカスタム ドメインで更新します。
- `Domain` エントリを自分の[テナント ID](tenant-management.md#get-your-tenant-id) で更新します。 詳しくは、「[テナント ID を使用する](custom-domain.md#optional-use-tenant-id)」をご覧ください。

次の JSON は、変更前のアプリ設定を示しています。 

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

次の JSON は、変更後のアプリ設定を示しています。 

```JSON
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>高度なシナリオをサポートする

Microsoft ID プラットフォーム API の `AddMicrosoftIdentityWebAppAuthentication` メソッドを使用すると、開発者は高度な認証シナリオ用のコードを追加したり、OpenIdConnect イベントをサブスクライブしたりできます。 たとえば、OnRedirectToIdentityProvider をサブスクライブすると、アプリから Azure AD B2C に送信される認証要求をカスタマイズできます。

高度なシナリオをサポートするには、*Startup.cs* ファイルを開き、`ConfigureServices` 関数で `AddMicrosoftIdentityWebAppAuthentication` を次のコード スニペットに置き換えます。 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

上のコードでは、`OnRedirectToIdentityProviderFunc` メソッドへの参照を含む OnRedirectToIdentityProvider イベントを追加しています。 `Startup.cs` クラスに、次のコード スニペットを追加します。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

コンテキスト パラメーターを使用して、コントローラーと `OnRedirectToIdentityProvider` 関数の間でパラメーターを渡すことができます。 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. `OnRedirectToIdentityProvider` 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. `OnRedirectToIdentityProviderFunc` 関数で、`OnRedirectToIdentityProvider` 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。
1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. `OnRedirectToIdentityProvider` 関数に次のコード行を追加します。

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. `OnRedirectToIdentityProvider` 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. `OnRedirectToIdentityProvider` 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>アカウント コントローラー

*SignIn*、*SignUp*、または *SignOut* アクションをカスタマイズする場合は、独自のコントローラーを作成することをお勧めします。 独自のコントローラーを使用すると、コントローラーと認証ライブラリの間でパラメーターを渡すことができます。 `AccountController` は、サインインおよびサインアウト アクションを処理する `Microsoft.Identity.Web.UI` NuGet パッケージの一部です。 その実装は、[Microsoft Identity Web ライブラリ](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)で確認できます。 

### <a name="add-the-account-controller"></a>アカウント コントローラーを追加する

Visual Studio プロジェクトで、*Controllers* フォルダーを右クリックし、新しい **コントローラー** を追加します。 **[MVC - Empty Controller]\(MVC - 空のコントローラー\)** を選択し、**MyAccountController.cs** という名前を指定します。

次のコード スニペットは、*SignIn* アクションでのカスタム `MyAccountController` を示しています。

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            return Challenge(properties, scheme);
        }

    }
}
```

*_LoginPartial.cshtml* ビューで、コントローラーへのサインイン リンクを変更します。

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>Azure AD B2C ポリシー ID を渡す

次のコード スニペットは、**SignIn** と **SignUp** アクションでのカスタム `MyAccountController` を示しています。 このアクションでは、`policy` という名前のパラメーターを認証ライブラリに渡します。 これにより、特定のアクションに対して正しい Azure AD B2C ポリシー ID を指定できます。

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

*_LoginPartial.cshtml* ビューで、サインアップやプロファイルの編集など、他のすべての認証リンクに対して `asp-controller` 値を `MyAccountController` に変更します。

### <a name="pass-custom-parameters"></a>カスタム パラメーターを渡す

次のコード スニペットは、**SignIn** アクションでのカスタム `MyAccountController` を示しています。 このアクションでは、`campaign_id` という名前のパラメーターを認証ライブラリに渡します。

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了してから、`OnRedirectToIdentityProvider` メソッドでカスタム パラメーターを読み取ります。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>ログアウトのリダイレクトをセキュリティで保護する

ログアウト後、ユーザーは、アプリケーションに対して指定されている応答 URL に関係なく、`post_logout_redirect_uri` パラメーターに指定された URI にリダイレクトされます。 ただし、有効な `id_token_hint` が渡され、 [[ログアウト要求に ID トークンが必要]](session-behavior.md#secure-your-logout-redirect) が有効になっている場合、Azure AD B2C では、リダイレクトの実行前に、`post_logout_redirect_uri` の値がいずれかのアプリケーションの構成済みのリダイレクト URI と一致するかどうかが検証されます。 一致する応答 URL がアプリケーションで構成されていない場合は、エラー メッセージが表示され、ユーザーはリダイレクトされません。

セキュリティで保護されたログアウト リダイレクトを自分のアプリケーションでサポートするには、まず、[アカウント コントローラー](enable-authentication-web-application-options.md#add-the-account-controller)と[高度なシナリオのサポート](#support-advanced-scenarios)に関するセクションの手順に従います。 次に、以下の手順に従ってください。

1. `MyAccountController.cs` コントローラーで、次のコード スニペットを使用して **SignOut** アクションを追加します。

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. **Startup.cs** クラスで、`id_token_hint` 値を解析し、その値を認証要求に追加します。 次のコード スニペットは、`id_token_hint` 値を認証要求に渡す方法を示しています。

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. `ConfigureServices` 関数で、**コントローラー** が `id_token` 値にアクセスできるように `SaveTokens` オプションを追加します。 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. **appsettings.json** 構成ファイルで、ログアウト リダイレクト URI パスを `SignedOutCallbackPath` キーに追加します。

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

上記の例で、ログアウト要求に渡される **post_logout_redirect_uri** は、`https://your-app.com/signout/<your-sign-up-in-policy>` の形式になります。 この URL をアプリケーション登録の応答 URL に追加する必要があります。

## <a name="role-based-access-control"></a>ロール ベースのアクセス制御

[ASP.NET Core での承認](/aspnet/core/security/authorization/introduction)では、次のいずれかの方法を使用して、保護されたリソースへのアクセスがユーザーに許可されているかどうかを確認できます。 
* [ロール ベースの承認](/aspnet/core/security/authorization/roles) 
* [Claims-Based Authorization](/aspnet/core/security/authorization/claims) 
* [ポリシー ベースの承認](/aspnet/core/security/authorization/policies)

`ConfigureServices` メソッドで、承認モデルを追加する `AddAuthorization` メソッドを追加します。 次の例では、`EmployeeOnly` という名前のポリシーを作成します。 このポリシーでは、`EmployeeNumber` 要求が存在することを確認するためのチェックを実行します。 要求の値は、1、2、3、4、5 のいずれかの ID である必要があります。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core での承認を制御するには、[AuthorizeAttribute](/aspnet/core/security/authorization/simple) とそのさまざまなパラメーターを使用します。 最も基本的な形式では、コントローラー、アクション、または Razor ページに `Authorize` 属性を適用すると、そのコンポーネントの認証済みユーザーにアクセスが制限されます。

コントローラーにポリシーを適用するには、`Authorize` 属性とポリシー名を使用します。 次のコードでは、`Claims` アクションへのアクセスが、`EmployeeOnly` ポリシーによって認可されたユーザーに制限されます。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>次の手順

- 承認の詳細については、「[ASP.NET Core での承認の概要](/aspnet/core/security/authorization/introduction)」を参照してください。
