---
title: Azure Active Directory B2C を使用して Web アプリケーション オプションを有効にする
description: いくつかの方法を使用して、Web アプリケーション オプションの使用を有効にします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3335e035a2d36cc7830d8bc93db82a7d318d26b3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110482341"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c-options"></a>Azure Active Directory B2C のオプションを使用してサンプル Web アプリケーションで認証を構成する

この記事では、Web アプリケーションの Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスをカスタマイズおよび拡張する方法について説明します。 開始する前に、[サンプル Web アプリケーションでの認証の構成](configure-authentication-sample-web-app.md)に関する記事、または[独自の Web アプリケーションでの認証の有効化](enable-authentication-web-application.md)に関する記事の内容を理解しておいてください。

## <a name="use-a-custom-domain"></a>カスタム ドメインの使用

アプリケーションのリダイレクト URL で[カスタム ドメイン](custom-domain.md)を使用すると、よりシームレスなユーザー エクスペリエンスが提供されます。 ユーザーからすると、サインイン プロセスの間、Azure AD B2C の既定のドメインである .b2clogin.com にリダイレクトされることなく、現在のドメインに留まっているように見えます。

カスタム ドメインを使用するには、[カスタム ドメインの有効化](custom-domain.md)に関する記事のガイダンスに従います。 プロジェクト ルート フォルダーで、`appsettings.json` ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 `Instance` エントリをカスタム ドメインで更新します。

次の JSON は、変更前のアプリ設定を示しています。 

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  ...
}
```  

次の JSON は、変更後のアプリ設定を示しています。 

```JSon
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  ...
}
``` 

## <a name="use-your-tenant-id"></a>テナント ID を使用する

URL の B2C テナント名を自分のテナント ID の GUID に置き換えると、URL 内の "b2c" へのすべての参照を削除できます。  たとえば、`https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更できます。

テナント ID を使用するには、[カスタム ドメインの有効化](custom-domain.md#optional-use-tenant-id)に関する記事のガイダンスに従います。 プロジェクト ルート フォルダーで、`appsettings.json` ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 `Domain` エントリをカスタム ドメインで更新します。

次の JSON は、変更前のアプリ設定を示しています。 

```JSon
"AzureAdB2C": {
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

次の JSON は、変更後のアプリ設定を示しています。

```JSon
"AzureAdB2C": {
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>高度なシナリオをサポートする

Microsoft ID プラットフォーム API の `AddMicrosoftIdentityWebAppAuthentication` メソッドを使用すると、開発者は高度な認証シナリオ用のコードを追加したり、OpenIdConnect イベントをサブスクライブしたりできます。 たとえば、OnRedirectToIdentityProvider をサブスクライブすると、アプリから Azure AD B2C に送信される認証要求をカスタマイズできます。

高度なシナリオをサポートするには、`Startup.cs` を開き、`ConfigureServices` 関数で `AddMicrosoftIdentityWebAppAuthentication` を次のコード スニペットに置き換えます。 

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


## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインイン ユーザー体験中に、アプリが特定のユーザーをターゲットにする場合があります。 ユーザーをターゲットにする場合、アプリケーションでは、認可要求でユーザー サインイン名と共に `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力されるので、ユーザーはパスワードを入力するだけで済みます。 

サインイン名を事前入力するには、これらの手順に従います。

1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. *OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="redirect-sign-in-to-an-external-identity-provider"></a>サインインを外部 ID プロバイダーにリダイレクトする

Facebook、LinkedIn、Google などのソーシャル アカウントを含むようにアプリケーションのサインイン プロセスを構成した場合は、`domain_hint` パラメーターを指定できます。 このクエリ パラメーターは、サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 たとえば、アプリケーションで `domain_hint=facebook.com` を指定した場合、サインイン フローで Facebook のサインイン ページに直接移動します。 

サインインを外部 ID プロバイダーにリダイレクトするには、これらの手順に従います。

1. 「[高度なシナリオをサポートする](#support-advanced-scenarios)」の手順を完了します。
1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. *OnRedirectToIdentityProviderFunc* 関数で、*OnRedirectToIdentityProvider* 関数に次のコード行を追加します。
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="specify-the-ui-language"></a>UI 言語を指定する

Azure AD B2C の言語のカスタマイズを使用すると、ユーザー フローで、顧客のニーズに合わせてさまざまな言語に対応できます。 詳細については、[言語のカスタマイズ](language-customization.md)に関する記事を参照してください。

優先言語を設定するには、これらの手順に従います。

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

## <a name="pass-a-custom-query-string-parameter"></a>カスタム クエリ文字列パラメーターを渡す

カスタム ポリシーを使用すると、[ページ コンテンツを動的に変更する](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)場合などに、カスタム クエリ文字列パラメーターを渡すことができます。


カスタム クエリ文字列パラメーターを渡すには、これらの手順に従います。

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

## <a name="pass-id-token-hint"></a>ID トークン ヒントを渡す

Azure AD B2C により、証明書利用者アプリケーションは OAuth2 認可要求の一部として受信 JWT を送信できます。 JWT トークンは、証明書利用者アプリケーションまたは ID プロバイダーが発行でき、これによって、ユーザーまたは認可要求に関するヒントを渡すことができます。 Azure AD B2C は、署名、発行者名、トークン対象ユーザーを検証し、受信トークンから要求を抽出します。

認証要求に ID トークン ヒントを含めるには、これらの手順に従います。 

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

**サインイン**、**サインアップ**、または **サインアウト** アクションをカスタマイズする場合は、独自のコントローラーを作成することをお勧めします。 独自のコントローラーを使用すると、コントローラーと認証ライブラリの間でパラメーターを渡すことができます。 `AccountController` は `Microsoft.Identity.Web.UI` NuGet パッケージの一部であり、サインインおよびサインアウト アクションを処理します。 その実装は、[Microsoft Identity Web ライブラリ](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)で確認できます。 

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

`Startup.cs` 呼び出しの `OnRedirectToIdentityProvider` で、カスタム パラメーターを読み取ることができます。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>ロール ベースのアクセス制御

[ASP.NET Core での認可](/aspnet/core/security/authorization/introduction)では、[ロールベースの認可](/aspnet/core/security/authorization/roles)、[クレームベースの認可](/aspnet/core/security/authorization/claims)、または[ポリシーベースの認可](/aspnet/core/security/authorization/policies)を使用して、ユーザーが保護されたリソースへのアクセスを承認されているかどうかを確認できます。

*ConfigureServices* メソッドで、認可モデルを追加する *AddAuthorization* メソッドを追加します。 次の例では、`EmployeeOnly` という名前のポリシーを作成します。 このポリシーでは、クレームの `EmployeeNumber` が存在することを確認します。 クレームの値は、1、2、3、4、5 のいずれかの ID である必要があります。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core での認可は、[AuthorizeAttribute](/aspnet/core/security/authorization/simple) とそのさまざまなパラメーターで制御されます。 最も基本的な形式では、コントローラー、アクション、または Razor ページに `[Authorize]` 属性を適用すると、そのコンポーネントの認証済みユーザーにアクセスが制限されます。

ポリシーは、ポリシー名と共に `[Authorize]` 属性を使用することでコントローラーに適用されます。 次のコードでは、`Claims` アクションへのアクセスを、`EmployeeOnly` ポリシーによって承認されたユーザーに制限します。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>次のステップ

- 詳細情報: [ASP.NET Core での認可の概要](/aspnet/core/security/authorization/introduction)