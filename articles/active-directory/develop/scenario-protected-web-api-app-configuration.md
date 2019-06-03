---
title: 保護された Web API - アプリ コードの構成 | Azure
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080113"
---
# <a name="protected-web-api---code-configuration"></a>保護された Web API - コードの構成

保護された Web API 用にコードを正しく構成するには、API を保護するもの、ベアラー トークンを構成するために必要なもの、およびトークンを検証する方法を理解する必要があります。

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>ASP.NET/ASP.NET Core API を保護するものとは

Web アプリと同じように、ASP.NET/ASP.NET Core Web API は、そのコントローラー アクションに `[Authorize]` 属性のプレフィックスがあるため、"保護" されています。 そのため、コントローラー アクションは、承認されている ID で API が呼び出された場合にのみ呼び出すことができます。

次の質問について考えてみましょう。

- Web API では呼び出し元のアプリケーション (アプリケーションだけが Web API を呼び出すことができる) の ID がどのように認識されていますか?
- ユーザーの代わりにアプリケーションによって Web API が呼び出された場合のユーザーの ID は何になりますか?

## <a name="bearer-token"></a>ベアラー トークン

アプリケーションの ID とユーザー情報は、(Web アプリが、デーモン アプリケーションからサービス間呼び出しを受け付ける場合を除き) アプリケーションが呼び出されるときにヘッダーで設定されるベアラー トークンに保持されます。

MSAL.NET でトークンを取得した後、API が呼び出されるクライアントが示された C# コード サンプルを次に示します。

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> **Web API の** Microsoft ID プラットフォーム エンドポイントへのベアラー トークンがクライアント アプリケーションによって要求されました。 Web API は、トークンを検証し、含まれている要求を調べる必要がある唯一のアプリケーションです。 クライアント アプリケーションによりトークン内の要求が調べられることはありません (将来的には、トークンが暗号化され、これによりアクセス トークンが解読される可能性があるクライアント アプリケーションを中断する要求が、Web API によって決定されるようになる可能性があります)。

## <a name="jwtbearer-configuration"></a>JwtBearer の構成

このセクションでは、構成する必要があるベアラー トークンについて説明します。

### <a name="config-file"></a>config ファイル

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

`[Authorize]` 属性を保持しているコントローラー アクションでアプリケーションが呼び出されると、ASP.NET/ASP.NET Core では呼び出し要求の Authorization ヘッダー内のベアラー トークンが参照され、アクセス トークンが抽出されます。これはその後、Microsoft Identity Model Extensions for .NET が呼び出される JwtBearer ミドルウェアに転送されます。

ASP.NET Core では、このミドルウェアは `Startup.cs` ファイルで初期化されます。

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

次の命令によって、ミドルウェアが Web API に追加されます。

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 この時点で、ASP.NET Core テンプレートによって Azure AD v1.0 Web API が作成されます。 ただしこれらは、`Startup.cs` ファイル内に次のコードを追加することで、Microsoft ID プラットフォーム エンドポイントを使用するように簡単に変更できます。

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>トークンの検証

JwtBearer ミドルウェアは、Web アプリでの OpenID Connect ミドルウェアと同様に、トークンを検証するように `TokenValidationParameters` によって指示されます。 トークンが暗号化解除され (必要な場合)、要求が抽出され、署名が検証されます。 その後、次のデータを確認してトークンが検証されます。

- Web API の対象となっていること (対象ユーザー)
- Web API の呼び出しが許可されているアプリケーションに対して発行されたこと (サブ)
- 信頼できる Security Token Server (STS) によって発行されたこと (発行者)
- トークンの有効期間が範囲内であること (有効期限)
- 改ざんされていないこと (署名)

特別な検証もできます。 たとえば、署名キー (トークンに埋め込まれている場合) が信頼されていること、およびトークンが再生されていないことを検証することができます。 最後に、一部のプロトコルでは、特定の検証が必要です。

### <a name="validators"></a>検証コントロール

検証手順は、検証コントロールによってキャプチャされます。これらはすべて [Microsoft Identity Model Extension for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) のオープン ソース ライブラリ内の 1 つのソース ファイル内にあります。[Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

検証コントロールについて、次の表で説明します。

| 検証コントロール | 説明 |
|---------|---------|
| `ValidateAudience` | トークンが (自分の) トークンを検証するアプリケーション用であることを確保します。 |
| `ValidateIssuer` | 信頼できる STS によって (信頼する人から) 発行されたトークンであることを確保します。 |
| `ValidateIssuerSigningKey` | トークンを検証するアプリケーションで、トークンの署名に使用されたキーが確実に信頼されるようにします (キーがトークンに埋め込まれた特殊な場合で、通常は必要ありません)。 |
| `ValidateLifetime` | トークンが引き続き有効である (または既に有効になっている) ことを確保します。 トークンの有効期間 (`notbefore`、`expires`要求) が範囲内であることを確認して行います。 |
| `ValidateSignature` | トークンが改ざんされていないことを確保します。 |
| `ValidateTokenReplay` | トークンが再生されていないことを確保します (一部の 1 回限りの使用のプロトコルの特殊なケース)。 |

検証コントロールはすべて `TokenValidationParameters` クラスのプロパティと関連付けられ、ASP.NET/ASP.NET Core 構成から初期化されます。 ほとんどの場合、パラメーターを変更する必要はありません。 ただし、1 つのテナントではないアプリケーション (つまり、任意の組織や個人の Microsoft アカウントからユーザーを受け入れる Web アプリ) だけは例外で、この場合は、発行者を検証する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境に移行する](scenario-protected-web-api-production.md)
