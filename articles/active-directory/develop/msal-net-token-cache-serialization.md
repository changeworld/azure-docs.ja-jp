---
title: トークン キャッシュのシリアル化 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用したトークン キャッシュのシリアル化およびカスタム シリアル化について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: f92df3818a6da63a57d4e0d26f4e145ae4d5c46f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123185707"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET でのトークン キャッシュのシリアル化

Microsoft Authentication Library (MSAL) によって[取得されたトークン](msal-acquire-cache-tokens.md)は、キャッシュに格納されます。 パブリック クライアント アプリケーション (デスクトップまたはモバイル アプリ) では、別の方法でトークンを取得する前に、キャッシュからトークンを取得する必要があります。 機密クライアント アプリケーションの取得方法によって、キャッシュ自体が管理されます。 この記事では、MSAL.NET でのトークン キャッシュの既定のシリアル化とカスタムのシリアル化について説明します。

## <a name="quick-summary"></a>簡単な概要

次のようにすることが推奨されます。
- Web アプリと Web API では、["Microsoft.Identity.Web" のトークン キャッシュ シリアライザー](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)を使用します。 それらにより、トークンを格納するための分散型データベースまたはキャッシュ システムも提供されます。
  - ASP.NET Core の [Web アプリ](scenario-web-app-call-api-overview.md)と [Web API](scenario-web-api-call-api-overview.md) では、ASP.NET Core での上位レベル API として Microsoft.Identity.Web を使用します。
  - ASP.NET クラシック、.NET Core、.NET Framework では、Microsoft.Identity.Web で提供されている [MSAL 用のトークン キャッシュ シリアル化アダプター]()で、MSAL.NET を直接使用します。 
- (ファイル システムを使用してトークンを格納できる) デスクトップ アプリケーションでは、[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache) と MSAL.Net を使用します。
- モバイル アプリケーション (Xamarin.iOS、Xamarin.Android、ユニバーサル Windows プラットフォーム) では、キャッシュは MSAL.NET によって自動的に処理されるので、何も行いません。これらのプラットフォームにはセキュリティ保護されたストレージがあります。

## <a name="aspnet-core-web-apps-and-web-apis"></a>[ASP.NET Core Web アプリと Web API](#tab/aspnetcore)

[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) ライブラリでは、トークン キャッシュのシリアル化を含む NuGet パッケージ [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) が提供されています。

| 拡張メソッド | 説明  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | メモリ内トークン キャッシュのシリアル化。 この実装は、サンプルにおいて非常に有用です。 また、Web アプリの再起動時にトークン キャッシュが失われても構わない場合は、実稼働アプリケーションにも適しています。 `AddInMemoryTokenCaches` は `MsalMemoryTokenCacheOptions` 型の省略可能なパラメーターを受け取ります。これにより、キャッシュ エントリが使用されていない場合に限り、期限切れになるまでの期間を指定できます。
| `AddSessionTokenCaches` | トークン キャッシュは、ユーザー セッションにバインドされています。 このオプションは、ID トークンに多くの要求が含まれている場合は、Cookie が大きくなりすぎるため適していません。
| `AddDistributedTokenCaches` | トークン キャッシュは ASP.NET Core `IDistributedCache` 実装に対するアダプターであるため、分散メモリ キャッシュ、Redis キャッシュ、分散 NCache、または SQL Server キャッシュから選択できます。 `IDistributedCache` の実装の詳細については、「[分散メモリ キャッシュ](/aspnet/core/performance/caching/distributed)」を参照してください。


ASP.NET Core アプリケーション内の [Startup](/aspnet/core/fundamentals/startup) クラスの [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) メソッドでメモリ内キャッシュを使用するコードの例を次に示します。

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

キャッシュの観点からは、このコードは ASP.NET Core Web API に似ています


可能な分散キャッシュの例を次に示します。

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

その使用法は、フェーズ [2-2 トークン キャッシュ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)の [ASP.NET Core Web アプリのチュートリアル](/aspnet/core/tutorials/first-mvc-app/)で説明しています。

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[ASP.NET Core 以外の Web アプリと Web API](#tab/aspnet)

MSAL.NET を使用する場合でも、Microsoft.Identity.Web に含まれるトークン キャッシュ シリアライザーを利用できます 

### <a name="referencing-the-nuget-package"></a>NuGet パッケージの参照

MSAL.NET に加えて、[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet パッケージをプロジェクトに追加します

### <a name="configuring-the-token-cache"></a>トークン キャッシュの構成

次のコードでは、メモリ内の十分にパーティション分割されたトークン キャッシュをアプリに追加する方法を示します。

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

 private static IConfidentialClientApplication app;

public static async Task<IConfidentialClientApplication> BuildConfidentialClientApplication(
  string clientId,
  CertificateDescription certDescription,
  string tenant)
 {
  if (app== null)
  {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(certDescription.Certificate)
       .WithLegacyCacheCompatibility(false)
       .WithTenantId(tenant)
       .Build();

     // Add an in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();
   }
   return app;
  }
```

### <a name="available-caching-technologies"></a>使用可能なキャッシュ テクノロジ

`app.AddInMemoryTokenCache();` の代わりに、.NET で提供されている分散型トークン キャッシュなど、さまざまなキャッシュ テクノロジを使用することができます。

#### <a name="in-memory-token-cache"></a>メモリ内トークン キャッシュ

メモリ内トークン キャッシュのシリアル化は、サンプルでは非常に便利です。 また、Web アプリの再起動時にトークン キャッシュが失われても構わない場合は、実稼働アプリケーションにも適しています。

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>分散キャッシュ

`app.AddDistributedTokenCache` を使用している場合、トークン キャッシュは .NET `IDistributedCache` 実装に対するアダプターであるため、分散メモリ キャッシュ、Redis キャッシュ、CosmosDb、または SQL Server キャッシュから選択できます。 `IDistributedCache` の実装の詳細については、「[分散メモリ キャッシュ](/aspnet/core/performance/caching/distributed)」を参照してください。

##### <a name="distributed-in-memory-token-cache"></a>分散メモリ内トークン キャッシュ

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>[データベースのインポート]

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Redis Cache

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

トークンの取得に redis キャッシュのタイムアウトと同じくらいの時間がかかることがある場合は、「[キャッシュ同期の無効化](#disabling-cache-synchronization)」も参照してください。 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>レガシ トークン キャッシュの無効化

MSAL には、特にレガシ ADAL キャッシュと対話する機能を有効にするための内部コードがいくつかあります。 MSAL と ADAL がサイド バイ サイドで使用されていない (したがって、レガシ キャッシュが使用されていない) 場合、関連するレガシ キャッシュ コードは必要ありません。 MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) により、レガシ ADAL キャッシュ コードを無効にして、キャッシュ使用パフォーマンスを向上させる機能が追加されます。 レガシ キャッシュを無効にする前と後のパフォーマンスの比較については、pull request [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309) を参照してください。 次のようにアプリケーション ビルダーで `.WithLegacyCacheCompatibility(false)` を呼び出します。

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>キャッシュ同期の無効化

MSAL では、既定でキャッシュの読み取りとキャッシュの書き込みの間に、機密クライアント アプリケーション レベルでキャッシュ アクセスがロックされます。 このロックは、キャッシュ シリアライザーがタイムアウトするまでの時間が長い場合に問題となることがあり、Redis キャッシュがこれに該当します。 `WithCacheSynchronization` フラグを false に設定してキャッシュの楽観的ロック戦略を有効にすることで、特に ConfidentialClientApplication オブジェクトが要求間で再利用されるときに、パフォーマンスが向上します。 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```

### <a name="monitor-cache-hit-ratios-and-cache-performance"></a>キャッシュ ヒット率とキャッシュ パフォーマンスの監視

MSAL は、[AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata.md) オブジェクトの一部として、重要なメトリックを公開します。 

| メトリック       | 意味     | アラームをトリガーするタイミング    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | MSAL で費やした合計時間 (ネットワーク呼び出しとキャッシュを含む)   | 全体の待機時間が長い場合のアラーム (1 秒以上)。 値はトークン ソースによって異なります。 キャッシュから: 1 回のキャッシュ アクセス。 AAD から: 2 回のキャッシュ アクセス + 1 回の HTTP 呼び出し。 1 回目 (プロセスごと) の呼び出しでは、1 回分の HTTP 呼び出しが追加で発生するため、時間がかかります。 |
|  `DurationInCacheInMs` | アプリ開発者によってカスタマイズされた、トークン キャッシュの読み込みまたは保存に要した時間 (Redis に保存するなど)。| スパイク時のアラーム。 |
|  `DurationInHttpInMs`| AAD への HTTP 呼び出しに要した時間。  | スパイク時のアラーム。|
|  `TokenSource` | トークンのソースを示します。 キャッシュからのトークンの取得が格段に速くなります (例: ～ 700 ms に対して ～ 100 ms)。 キャッシュ ヒット率の監視とアラーム生成に使用できます。 | `DurationTotalInMs` と使用します |

### <a name="samples"></a>サンプル

- .NET Framework および .NET Core アプリケーションでのトークン キャッシュ シリアライザーの使用方法については、こちらのサンプル [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) を参照してください 
- 次のサンプルは、同じ手法を使用する ASP.NET Web アプリです: https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect ([WebApp/Utils/MsalAppBuilder.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) を参照)

## <a name="desktop-apps"></a>[デスクトップ アプリ](#tab/desktop)

デスクトップ アプリケーションでは、クロス プラットフォーム トークン キャッシュを使用することをお勧めします。

#### <a name="cross-platform-token-cache-msal-only"></a>クロス プラットフォーム トークン キャッシュ (MSAL のみ)

MSAL.NET では、Microsoft.Identity.Client.Extensions.Msal という名前の別のライブラリでクロス プラットフォーム トークン キャッシュが提供されています。そのソース コードは、 https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet から入手できます。

##### <a name="referencing-the-nuget-package"></a>NuGet パッケージの参照

[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) NuGet パッケージを、プロジェクトに追加します。

##### <a name="configuring-the-token-cache"></a>トークン キャッシュの構成

詳細については、https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache を参照してください。 クロス プラットフォーム トークン キャッシュを使用する例を次に示します。

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

## <a name="mobile-apps"></a>[モバイル アプリ](#tab/mobile)

MSAL.NET では、既定でメモリ内のトークン キャッシュが提供されています。 プラットフォームの一部としてセキュリティで保護されたストレージをユーザーが使用できる次のプラットフォーム用に、既定でシリアル化が提供されています: ユニバーサル Windows プラットフォーム (UWP)、Xamarin.iOS、Xamarin.Android。

## <a name="write-your-own-cache"></a>[独自のキャッシュを作成する](#tab/custom)

独自のトークン キャッシュ シリアライザーを記述する場合は、MSAL.NET によって、.NET Framework および .NET Core サブプラットフォームでのカスタム トークン キャッシュ シリアル化が提供されています。 キャッシュへのアクセスが行われた場合にイベントが発生し、アプリでは、キャッシュをシリアル化するか逆シリアル化するかを選択できます。 ユーザーを処理する機密クライアント アプリケーション (ユーザーにサインインして Web API を呼び出す Web アプリと、ダウンストリームの Web API を呼び出す Web API) 上には、多くのユーザーがいる場合があり、ユーザーは並列で処理されます。 セキュリティとパフォーマンス上の理由から、ユーザーごとに 1 つのキャッシュをシリアル化することをお勧めします。 シリアル化イベントでは、処理されたユーザーの ID に基づいてキャッシュ キーが計算され、そのユーザーのトークン キャッシュがシリアル化/逆シリアル化されます。

モバイル プラットフォーム (UWP、Xamarin.iOS、Xamarin.Android) ではカスタムのシリアル化を使用できません。 MSAL では、これらのプラットフォームに対して安全性とパフォーマンスに優れたシリアル化メカニズムを既に定義しています。 ただし、.NET デスクトップおよび .NET Core アプリケーションのアーキテクチャにはさまざまなものがあり、MSAL では汎用的なシリアル化メカニズムを実装できません。 たとえば、Web サイトでは Redis Cache にトークンが格納され、デスクトップ アプリでは暗号化ファイルにトークンが格納されたりする場合があります。 したがって、追加設定なしに使用できるシリアル化は提供されていません。 .NET デスクトップまたは .NET Core で永続的なトークン キャッシュ アプリケーションを入手するには、シリアル化をカスタマイズします。

トークン キャッシュのシリアル化では、次のクラスとインターフェイスが使用されます。

- `ITokenCache` では、トークン キャッシュのシリアル化要求をサブスクライブするイベントと、キャッシュをさまざまな形式 (ADAL v3.0、MSAL 2.x、および MSAL 3.x = ADAL v5.0) でシリアル化または逆シリアル化するメソッドが定義されています。
- `TokenCacheCallback` は、シリアル化を処理できるようにイベントに渡されるコールバックです。 `TokenCacheNotificationArgs` の型の引数で呼び出されます。
- `TokenCacheNotificationArgs` では、アプリケーションの `ClientId` と、トークンを使用できるユーザーへの参照のみを提供します。

  ![クラス ダイアグラム](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> アプリケーションの `UserTokenCache` プロパティと `AppTokenCache` プロパティを呼び出すと、MSAL.NET によりトークン キャッシュが自動的に作成され、`IToken` キャッシュが提供されます。 インターフェイスを自分で実装することはできません。 カスタムのトークン キャッシュのシリアル化を実装する際には、お客様の責任で次の処理を行います。
> - `BeforeAccess` と `AfterAccess` の各 "イベント" (またはこれらの非同期版) に対応します。 `BeforeAccess` デリゲートではキャッシュの逆シリアル化を行い、`AfterAccess` デリゲートではキャッシュのシリアル化を行います。
> - これらのイベントの一部では、BLOB が格納されるか、または読み込まれ、イベント引数を介して任意のストレージに渡されます。

[パブリック クライアント アプリケーション](msal-client-applications.md) (デスクトップ) と[機密クライアント アプリケーション](msal-client-applications.md) (Web アプリ/Web API、デーモン アプリ) のどちらに対してトークン キャッシュのシリアル化を作成しているかに応じて、戦略は異なります。

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Web アプリまたは Web API 用のカスタム トークン キャッシュ (機密クライアント アプリケーション)

Web アプリまたは Web API では、キャッシュでセッション、Redis Cache、SQL データベース、または Cosmos DB データベースを利用できます。 Web アプリまたは Web API では、アカウントごとに 1 つのトークン キャッシュを保持します。 
- Web アプリの場合、トークン キャッシュは、アカウント ID によってキー指定されている必要があります。
- Web API の場合、アカウントは、API の呼び出しに使用されるトークンのハッシュによって、キー指定されている必要があります。

トークン キャッシュ シリアライザーの例は、[Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders) で提供されています。

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>デスクトップ アプリまたはモバイル アプリ用のカスタム トークン キャッシュ (パブリック クライアント アプリケーション)

MSAL.NET v2.x 以降では、パブリック クライアント用のトークン キャッシュをシリアル化するための複数のオプションが用意されています。 キャッシュは、MSAL.NET 形式にのみシリアル化することができます (この統一形式のキャッシュは、MSAL およびプラットフォーム間で共通です)。  また、ADAL V3 の[レガシ](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) トークン キャッシュのシリアル化をサポートすることもできます。

ADAL.NET 3.x、ADAL.NET 5.x、および MSAL.NET の間でシングル サインオン状態を共有するようトークン キャッシュのシリアル化をカスタマイズする方法については、サンプル「[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)」を参照してください。

> [!Note]
> MSAL 2.x では、MSAL.NET 1.1.4-preview トークン キャッシュ形式はサポートされなくなりました。 MSAL.NET 1.x を利用するアプリケーションがある場合、ユーザーは再度サインインする必要があります。 または、ADAL 4.x (および 3.x) からの移行がサポートされています。

#### <a name="simple-token-cache-serialization-msal-only"></a>トークン キャッシュの単純なシリアル化 (MSAL のみ)

デスクトップ アプリケーション用のトークン キャッシュについて、カスタムのシリアル化の単純な実装の例を次に示します。 ここでは、ユーザーのトークン キャッシュは、アプリケーションと同じフォルダー内のファイルです。

アプリケーションをビルドした後、`TokenCacheHelper.EnableSerialization()` メソッドを呼び出し、アプリケーションに `UserTokenCache` を渡すことで、シリアル化を有効にします。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` ヘルパー クラスは、次のように定義されます。

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Windows、Mac、Linux で実行されているデスクトップ アプリケーションのパブリック クライアント アプリケーション用の製品品質トークン キャッシュ ファイルベースのシリアライザーは、[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) オープン ソース ライブラリで入手できます。 これを、次の NuGet パッケージからアプリケーションに追加することができます。[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>トークン キャッシュのデュアル シリアル化 (MSAL 統一キャッシュと ADAL v3)

トークン キャッシュのシリアル化を統一キャッシュ形式の両方 (同じプラットフォーム上の ADAL.NET 4.x、MSAL.NET 2.x、および同じ世代以前のその他の MSAL に共通) で実装する場合は、次のコードを参照してください。

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

この場合、ヘルパー クラスは次のように定義されます。

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="next-steps"></a>次のステップ

次のサンプルでは、トークン キャッシュのシリアル化を示します。

| サンプル | プラットフォーム | 説明|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | デスクトップ (WPF) | Microsoft Graph API を呼び出す Windows デスクトップ .NET (WPF) アプリケーション。 ![トポロジの図。Desktop App WPF TodoListClient はトークンを対話形式で取得することで Azure AD に流れます。また、Microsoft Graph に流れます。](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | デスクトップ (コンソール) | Azure AD v1.0 アプリケーション (ADAL.NET を使用) から Microsoft ID プラットフォーム アプリケーション (MSAL.NET を使用) への移行を示す一連の Visual Studio ソリューション。 特に、[トークン キャッシュの移行](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)に関するページと、[機密クライアントのトークン キャッシュ](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)に関するページを参照してください |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | ASP.NET MVC アプリケーションでのトークン キャッシュのシリアル化の例 (MSAL.NET を使用)。 特に、[MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) に関するページを参照してください