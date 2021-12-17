---
title: パブリック クライアント アプリをインスタンス化する (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft Authentication Library (MSAL.NET) を使用して、構成オプションでパブリック クライアント アプリケーションをインスタンス化する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 57d7cba072e51483732d103d358910ba443a525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059416"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET を使用して、構成オプションでパブリック クライアント アプリケーションをインスタンス化する

この記事では、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用して[パブリック クライアント アプリケーション](msal-client-applications.md)を初期化する方法について説明します。  アプリケーションは、設定ファイルで定義されている構成オプションを使用してインスタンス化されます。

アプリケーションを初期化する前に、まず、そのアプリケーションを[登録](quickstart-register-app.md)して、Microsoft ID プラットフォームに統合できるようにする必要があります。 登録後に、次の情報が必要な場合があります (Azure portal で検索できます)。

- クライアント ID (GUID を表す文字列)
- ID プロバイダーの URL (名前付きインスタンス) とアプリケーションのサインイン対象ユーザー。 これら 2 つのパラメーターは機関と総称されます。
- 組織専用の基幹業務アプリケーション (および名前付きシングル テナント アプリケーション) を作成している場合はテナント ID。
- Web アプリ、および場合によってはパブリック クライアント アプリの場合 (特に、アプリでブローカーを使用する必要がある場合)、ID プロバイダーがセキュリティ トークンを使用してアプリケーションに連絡する redirectUri も設定します。

## <a name="default-reply-uri"></a>既定の応答 URI。

MSAL.NET 4.1 以上では、`public PublicClientApplicationBuilder WithDefaultRedirectUri()` メソッドを使用して既定のリダイレクト URI (応答 URI) を設定できるようになりました。 このメソッドは、パブリッククライアントアプリケーションのリダイレクト URI プロパティを、推奨される既定値に設定します。

このメソッドの動作は、その時点で使用しているプラットフォームによって異なります。 特定のプラットフォームに設定されているリダイレクト URI について説明した表を次に示します。

プラットフォーム  | リダイレクト URI  
---------  | --------------
デスクトップ アプリ (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` の値
.NET Core | `http://localhost`

UWP プラットフォームの場合、`WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` の結果に値を設定して、ブラウザーでの SSO を有効にすることで、エクスペリエンスが強化されます。 

.NET Core の場合、MSAL.Net では、値がローカル ホストに設定され、ユーザーは対話型認証にシステム ブラウザーを使用できます。

> [!NOTE]
> デスクトップシナリオの埋め込みブラウザーでは、使用されるリダイレクト URI がMSALによってインターセプトされ、認証コードが返されたことが id プロバイダーから返されたことを検出します。 そのため、このURIは、そのURIに実際にリダイレクトされることなく、任意のクラウドで使用できます。 つまり、どのクラウドでも `https://login.microsoftonline.com/common/oauth2/nativeclient` を使用でき、使用する必要があります。 必要に応じて、MSALとアプリの登録でリダイレクトURIを正しく構成していれば、他のURIを使用することもできます。 アプリケーションの登録で既定の URI を指定すると、MSALのセットアップの量が少なくなります。


.NET Core コンソール アプリケーションでは次の *appsettings.json* 構成ファイルを使用することができました。

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

次のコードは、.NET 構成フレームワークを使用してこのファイルを読み取ります。

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

次のコードは、設定ファイルから構成を使用して、アプリケーションを作成します。

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```
