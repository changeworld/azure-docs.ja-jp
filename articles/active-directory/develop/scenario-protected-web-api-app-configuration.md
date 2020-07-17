---
title: 保護された Web API アプリを構成する |Microsoft
titleSuffix: Microsoft identity platform
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882473"
---
# <a name="protected-web-api-code-configuration"></a>保護された Web API: コード構成

保護された Web API のコードを構成するには、次の点を理解しておく必要があります。

- API を保護対象として定義するもの
- ベアラー トークンの構成方法
- トークンの検証方法

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET と ASP.NET Core の API を保護対象として定義するものとは

Web アプリと同じように、ASP.NET と ASP.NET Core の Web API は、そのコントローラー アクションに **[Authorize]** 属性のプレフィックスがあるため、保護されています。 コントローラー アクションは、承認されている ID で API が呼び出された場合にのみ呼び出すことができます。

次の質問について考えてみましょう。

- Web API を呼び出せるのはアプリのみです。 API では、呼び出し元のアプリの ID をどのように認識しますか?
- ユーザーの代わりにアプリで API が呼び出される場合、ユーザーの ID は何ですか?

## <a name="bearer-token"></a>ベアラー トークン

アプリが呼び出されたときにヘッダーに設定されるベアラー トークンには、アプリ ID に関する情報が保持されます。 また、Web アプリがデーモン アプリからのサービス間の呼び出しを受け入れる場合を除き、ユーザーに関する情報も保持されます。

以下は、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用してトークンを取得した後、API を呼び出すクライアントを示す C# コードの例です。

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> クライアント アプリケーションにより、"*Web API の*" Microsoft ID プラットフォーム エンドポイントにベアラー トークンが要求されます。 Web API は、トークンを検証し、含まれている要求を表示する必要がある唯一のアプリケーションです。 クライアント アプリで、トークンの要求を検査してみることはできません
>
> 将来、Web API で、トークンの暗号化が要求される可能性があります。 この要件により、アクセス トークンを表示できるクライアント アプリのアクセスが禁止されます。

## <a name="jwtbearer-configuration"></a>JwtBearer の構成

このセクションでは、ベアラー トークンの構成方法について説明します。

### <a name="config-file"></a>config ファイル

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>コードの初期化

**[Authorize]** 属性を保持するコントローラー アクションでアプリが呼び出されると、ASP.NET と ASP.NET Core により、Authorization ヘッダーのベアラー トークンからアクセス トークンが抽出されます。 その後、アクセス トークンは JwtBearer ミドルウェアに転送され、Microsoft IdentityModel Extensions for .NET が呼び出されます。

ASP.NET Core では、このミドルウェアは Startup.cs ファイルで初期化されます。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

この命令によって、ミドルウェアが Web API に追加されます。

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 現在、ASP.NET Core テンプレートでは、ご自分の組織または任意の組織内のユーザーのサインインを行う、Azure Active Directory (Azure AD) Web API が作成されます。 個人アカウントを使用してユーザーをサインインさせることはありません。 ただし、次のコードを Startup.cs に追加することで、Microsoft ID プラットフォーム エンドポイントを使用するようにテンプレートを変更できます。

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

上記のコード スニペットは、[Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63) にある ASP.NET Core Web API の増分チュートリアルから引用されています。 Startup.cs から **AddProtectedWebApi** メソッドが呼び出されます。これはスニペットに示されている以上のことを行います。

## <a name="token-validation"></a>トークンの検証

前のスニペットでは、Web アプリの OpenID Connect ミドルウェアと同様に、JwtBearer ミドルウェアによって `TokenValidationParameters` の値に基づいてトークンが検証されます。 トークンは必要に応じて暗号化が解除され、要求が抽出され、署名が検証されます。 その後、ミドルウェアでは、このデータを調べてトークンを確認します。

- Audience:トークンが Web API のターゲットとなっていること。
- サブ:Web API の呼び出しが許可されているアプリに対して発行されたこと。
- 発行者:信頼できるセキュリティ トークン サービス (STS) によって発行されたこと。
- 有効期限:有効期間が範囲内であること。
- 署名:改ざんされていないこと。

特別な検証もできます。 たとえば、署名キー (トークンに埋め込まれている場合) が信頼されていること、およびトークンが再生されていないことを確認することができます。 最後に、一部のプロトコルでは、特定の検証が必要です。

### <a name="validators"></a>検証コントロール

確認手順は、検証コントロールでキャプチャされます。これらは [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) のオープン ソース ライブラリで提供されています。 検証コントロールは、ライブラリ ソース ファイル [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs) で定義されています。

次の表では、検証コントロールについて説明します。

| 検証コントロール | 説明 |
|---------|---------|
| **ValidateAudience** | トークンが、自分のトークンを確認するアプリケーション用であることを保証します。 |
| **ValidateIssuer** | トークンが信頼できる STS、つまり自分が信頼する人から発行されたことを保証します。 |
| **ValidateIssuerSigningKey** | トークンを確認するアプリケーションで、トークンの署名に使用されたキーが信頼されていることを保証します キーがトークンに埋め込まれている特殊なケースがあります。 ただし、このケースは通常は発生しません。 |
| **ValidateLifetime** | トークンが引き続きまたは既に有効であることを保証します。 検証コントロールにより、トークンの有効期間が **notbefore** 要求と **expires** 要求で指定された範囲内にあるかどうかが確認されます。 |
| **ValidateSignature** | トークンが改ざんされていないことを保証します。 |
| **ValidateTokenReplay** | トークンが再生されていないことを保証します 一部の 1 回限りの使用のプロトコルには特殊なケースがあります。 |

検証コントロールは、**TokenValidationParameters** クラスのプロパティに関連付けられています。 このプロパティは、ASP.NET と ASP.NET Core の構成から初期化されます。

ほとんどの場合、パラメーターを変更する必要はありません。 シングル テナントではないアプリは例外です。 これらの Web アプリでは、任意の組織から、または個人用 Microsoft アカウントからのユーザーを受け入れます。 この場合、発行者を検証する必要があります。

## <a name="token-validation-in-azure-functions"></a>Azure Functions でのトークンの検証

Azure Functions では、受信アクセス トークンを検証することもできます。 このような検証の例は、[Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)、[NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)、および [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) で見つけることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [コードでスコープとアプリのロールを検証する](scenario-protected-web-api-verification-scope-app-roles.md)
