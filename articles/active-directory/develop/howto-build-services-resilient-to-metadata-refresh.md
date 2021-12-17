---
title: '方法: Azure AD の OpenID Connect メタデータの更新に対して回復性のあるサービスを構築する | Azure'
titleSuffix: Microsoft identity platform
description: Web アプリまたは Web API に Azure AD の OpenID Connect メタデータの更新に対する回復性を確実に備える方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: jmprieur
ms.reviewer: marsma, shermanouko
ms.custom: aaddev
ms.openlocfilehash: 2b698f351198daeceddc6b254eb62ddb5f58a008
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988902"
---
# <a name="build-services-that-are-resilient-to-azure-ads-openid-connect-metadata-refresh"></a>Azure AD の OpenID Connect メタデータの更新に対して回復性のあるサービスを構築する

保護された Web API では、アクセス トークンを検証する必要があります。 Web アプリでも ID トークンの検証が行われます。 トークンの検証には複数の部分があり、トークンがアプリケーションに属しているかどうか、信頼された ID プロバイダー (IDP) によって発行されているかどうか、有効期間がまだ範囲内で、改ざんされていないかどうかが確認されます。 特別な検証もできます。 たとえば、アプリでは署名が検証され、署名キー (トークンに埋め込まれている場合) が信頼されていることと、トークンが再生されていないことが確認される必要があります。 署名キーがトークンに埋め込まれていない場合は、ID プロバイダー (検出またはメタデータ) から取得する必要があります。 実行時にキーを動的に取得する必要がある場合もあります。

Web アプリと Web API では、回復性を維持するために、古い OpenID Connect メタデータを更新する必要があります。 この記事は、回復性があるアプリを実現するためのガイドとして役立ちます。 これは、ASP.NET Core、ASP.NET クラシック、Microsoft.IdentityModel に適用されます。

## <a name="aspnet-core"></a>ASP.NET Core

最新バージョンの [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) を使用して、以下のガイドラインに従って手動で行います。

Startup.cs の `ConfigureServices` メソッドで、`JwtBearerOptions.RefreshOnIssuerKeyNotFound` が true に設定されていること、および最新の Microsoft.IdentityModel.* ライブラリを使用していることを確認します。 このプロパティは、既定で有効になっています。

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    …
    // shouldn’t be necessary as it’s true by default
    options.RefreshOnIssuerKeyNotFound = true;
    …
};
```

## <a name="aspnet-owin"></a>ASP.NET/ OWIN

ASP.NET での開発が停止しているため、ASP.NET Core に移行することを、Microsoft はお勧めします。

ASP.NET クラシックを使用している場合は、最新の [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) を使用してください。

OWIN には、`OpenIdConnectConfiguration` の 24 時間間隔の自動更新があります。 この更新は、24 時間の期間が経過した後に要求が受信された場合にのみトリガーされます。 わかっている限りでは、アプリケーションを再起動する以外に、この値を変更したり、更新を早期にトリガーしたりする方法はありません。

## <a name="microsoftidentitymodel"></a>Microsoft.IdentityModel

Azure 関数などでトークンをご自分で検証する場合は、最新バージョンの [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) を使用して、以下のコード スニペットに示されているメタデータ ガイダンスに従ってください。

```csharp
var configManager =
  new ConfigurationManager<OpenIdConnectConfiguration>(
    "http://someaddress.com",
    new OpenIdConnectConfigurationRetriever());

var config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
var validationParameters = new TokenValidationParameters()
{
  …
  IssuerSigningKeys = config.SigningKeys;
  …
}

var tokenHandler = new JsonWebTokenHandler();
result = Handler.ValidateToken(jwtToken, validationParameters);
if (result.Exception != null && result.Exception is SecurityTokenSignatureKeyNotFoundException)
{
  configManager.RequestRefresh();
  config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
  validationParameters = new TokenValidationParameters()
  {
    …
    IssuerSigningKeys = config.SigningKeys,
    …
  };

  // attempt to validate token again after refresh
  result = Handler.ValidateToken(jwtToken, validationParameters);
}
```

## <a name="next-steps"></a>次の手順

詳細については、[「保護された WEB API」 の「トークンの検証」](scenario-protected-web-api-app-configuration.md#token-validation)を参照してください
