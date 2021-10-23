---
title: Azure Active Directory B2C を使用して Web API オプションを呼び出す Web アプリケーションを有効にする
description: この記事では、いくつかの方法で Web API オプションを呼び出す Web アプリケーションの使用を有効にする方法について説明します。
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
ms.openlocfilehash: e6d43846307ddf97983a408c244ff3470bea2641
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040875"
---
# <a name="configure-authentication-options-in-a-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Web API を呼び出す Web アプリで認証オプションを構成する 

この記事では、Web API を呼び出す Web アプリケーションの Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスをカスタマイズおよび拡張する方法について説明します。 開始する前に、次の記事の内容をご確認ください。 
* [サンプルの Web アプリケーションで認証を構成する](configure-authentication-sample-web-app-with-api.md)
* [独自の Web アプリケーションで認証を有効にする](enable-authentication-web-app-with-api.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)] 

認証 URL でカスタム ドメインとテナント ID を使用するには、[カスタム ドメインを有効にする](custom-domain.md)方法に関するページのガイダンスに従ってください。 プロジェクト ルート フォルダーで、`appsettings.json` ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 

- `Instance` エントリをカスタム ドメインで更新します。
- `Domain` エントリを自分の[テナント ID](tenant-management.md#get-your-tenant-id) で更新します。 詳しくは、「[テナント ID を使用する](custom-domain.md#optional-use-tenant-id)」をご覧ください。

次の JSON コードは、変更の "*前*" のアプリの設定を示したものです。 

```json
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

次の JSON コードは、変更の "*後*" のアプリの設定を示したものです。 

```json
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
// Configuration to sign in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

上記のコードでは、*OnRedirectToIdentityProviderFunc* メソッドへの参照を含む OnRedirectToIdentityProvider イベントを追加しています。 `Startup.cs` クラスに、次のコード スニペットを追加します。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

コンテキスト パラメーターを使用して、コントローラーと *OnRedirectToIdentityProvider* 関数の間でパラメーターを渡すことができます。 


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 

1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。

1. *OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
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
1. *OnRedirectToIdentityProviderFunc* 関数で、*OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
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
1. *OnRedirectToIdentityProvider* 関数に次のコード行を追加します。

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
1. *OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
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
1. *OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
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

"*サインイン*"、"*サインアップ*"、または "*サインアウト*" のアクションをカスタマイズする場合は、独自のコントローラーを作成することをお勧めします。 独自のコントローラーを作成すると、コントローラーと認証ライブラリの間でパラメーターを渡すことができます。 `AccountController` は `Microsoft.Identity.Web.UI` NuGet パッケージの一部であり、サインインおよびサインアウト アクションを処理します。 その実装は、[Microsoft Identity Web ライブラリ](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)で確認できます。 

次のコード スニペットは、**SignIn** アクションでのカスタム `MyAccountController` を示しています。 このアクションでは、`campaign_id` という名前のパラメーターを認証ライブラリに渡します。

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
            properties.Items["campaign_id"] = "1234";
            return Challenge(properties, scheme);
        }

    }
}

```

`_LoginPartial.cshtml` ビューで、コントローラーへのサインイン リンクを変更します

```
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

`Startup.cs` の呼び出しの `OnRedirectToIdentityProvider` で、カスタム パラメーターを読み取ることができます。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

[ASP.NET Core での認可](/aspnet/core/security/authorization/introduction)では、[ロールベースの認可](/aspnet/core/security/authorization/roles)、[クレームベースの認可](/aspnet/core/security/authorization/claims)、または[ポリシーベースの認可](/aspnet/core/security/authorization/policies)を使用して、ユーザーが保護されたリソースへのアクセスを承認されているかどうかを確認できます。

*ConfigureServices* メソッドで、認可モデルを追加する *AddAuthorization* メソッドを追加します。 次の例では、`EmployeeOnly` という名前のポリシーを作成します。 このポリシーでは、クレームの `EmployeeNumber` が存在するかどうかが確認されます。 クレームの値は、1、2、3、4、5 のいずれかの ID である必要があります。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core での認可を制御するには、[AuthorizeAttribute](/aspnet/core/security/authorization/simple) とそのさまざまなパラメーターを使用します。 `[Authorize]` 属性の最も基本的な形式をコントローラー、アクション、または Razor ページに適用すると、そのコンポーネントの認証済みユーザーにアクセスが制限されます。

コントローラーにポリシーを適用するには、`[Authorize]` 属性とポリシー名を使用します。 次のコードでは、`Claims` アクションへのアクセスが、`EmployeeOnly` ポリシーによって認可されたユーザーに制限されます。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>次のステップ

詳細については、「[ASP.NET Core での認可の概要](/aspnet/core/security/authorization/introduction)」を参照してください。
