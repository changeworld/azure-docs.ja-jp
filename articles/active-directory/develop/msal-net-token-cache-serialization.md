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
ms.date: 11/09/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 22e6dbfbda88035f74e19fabfe296974b362e286
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517294"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET でのトークン キャッシュのシリアル化

Microsoft Authentication Library (MSAL) によって<bpt id="p1">[</bpt>取得されたトークン<ept id="p1">](msal-acquire-cache-tokens.md)</ept>は、キャッシュに格納されます。 パブリック クライアント アプリケーション (デスクトップまたはモバイル アプリ) では、別の方法でトークンを取得する前に、キャッシュからトークンを取得する必要があります。 機密クライアント アプリケーションの取得方法によって、キャッシュ自体が管理されます。 この記事では、MSAL.NET でのトークン キャッシュの既定のシリアル化とカスタムのシリアル化について説明します。

## <a name="quick-summary"></a>簡単な概要

次のようにすることが推奨されます。
- デスクトップ アプリケーションを作成する場合は、「[デスクトップ アプリ](msal-net-token-cache-serialization.md?tabs=desktop#cross-platform-token-cache-msal-only)」で説明されているように、クロスプラットフォーム トークン キャッシュを使用します。
- [モバイル アプリと UWP アプリ](msal-net-token-cache-serialization.md?tabs=mobile)に対しては何もしません。 キャッシュは、MSAL.NET によって提供されます。
- ASP.NET Core の [Web アプリ](scenario-web-app-call-api-overview.md)と [Web API](scenario-web-api-call-api-overview.md) で、ASP.NET Core での上位レベル API として [Microsoft.Identity.Web](microsoft-identity-web.md) を使用します。 トークン キャッシュなどを取得できます。 [ASP.NET Core Web アプリと Web API](msal-net-token-cache-serialization.md?tabs=aspnetcore) に関する記事をご覧ください。
- [Web アプリ](scenario-web-app-call-api-overview.md) と [Web API](scenario-web-api-call-api-overview.md) のその他のケースでは、次のようにします。
  - 実稼働アプリケーションでユーザー向けのトークンを要求する場合は、[分散トークン キャッシュ](msal-net-token-cache-serialization.md?tabs=aspnet#distributed-caches) (Redis、SQL、Cosmos DB、分散メモリ) を使用します。 [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache/) で使用できるトークン キャッシュ シリアライザーを使用します。
  - それ以外の場合、メモリ内キャッシュを使用するには、次のようにします。
    -   `AcquireTokenForClient` のみを使用している場合:
      - 機密クライアント アプリケーション インスタンスを再利用し、シリアライザーを追加しないか、
      - または新しい機密クライアント アプリケーションを起動し、[共有キャッシュ オプション](msal-net-token-cache-serialization.md?tabs=aspnet#no-token-cache-serialization)を有効にします。 このキャッシュはシリアル化されないので高速ですが、トークンがキャッシュされるとメモリが増加します。 トークンの数は、テナントの数にダウンストリーム API の数を掛けたものと同じです。 アプリ トークンのサイズは約 2 KB です。 開発に適しています。また、ユーザー数が少ない場合にも適しています。 削除が必要な場合は、次の箇条書き点を参照してください。
      - メモリ内のトークン キャッシュを使用し、かつそのサイズと削除のポリシーを制御する場合は、[Microsoft.Identity.Web メモリ内キャッシュ オプション](msal-net-token-cache-serialization.md?tabs=aspnet#in-memory-token-cache-1)を使用します。
-   SDK をビルドし、機密クライアント アプリケーション用に独自のトークン キャッシュ シリアライザーを記述する場合は、[Microsoft.Identity.Web.MsalAsbtractTokenCacheProvider](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.TokenCache/MsalAbstractTokenCacheProvider.cs) から継承し、`WriteCacheBytesAsync` と `ReadCacheBytesAsync` メソッドをオーバーライドします。


## <a name="aspnet-core-web-apps-and-web-apis"></a><bpt id="p1">[</bpt>ASP.NET Core Web アプリと Web API<ept id="p1">](#tab/aspnetcore)</ept>

[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) ライブラリでは、トークン キャッシュのシリアル化を含む NuGet パッケージ [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) が提供されています。

| 拡張メソッド | 説明  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | メモリ内トークン キャッシュのシリアル化。 この実装は、サンプルとデーモン アプリケーション (アプリからアプリへのトークン/ `AcquireTokenForClient` ) に最適です 。 また、Web アプリの再起動時にトークン キャッシュが失われても構わない場合は、実稼働アプリケーションにも適しています。 Microsoft.Identity.Web 1.19.0 以降では、MSAL.Net はすべてのアプリ インスタンスで静的 (共有) キャッシュを使用するように構成されており、他のキャッシュ メカニズムよりも高速ですが、キャッシュ サイズを制御することはできません。
| `AddSessionTokenCaches` | トークン キャッシュは、ユーザー セッションにバインドされています。 このオプションは、ID トークンに多くの要求が含まれている場合は、Cookie が大きくなりすぎるため適していません。
| `AddDistributedTokenCaches` | トークン キャッシュは ASP.NET Core <ph id="ph1">`IDistributedCache`</ph> 実装に対するアダプターであるため、分散メモリ キャッシュ、Redis キャッシュ、分散 NCache、または SQL Server キャッシュから選択できます。 <ph id="ph1">`IDistributedCache`</ph> の実装の詳細については、「<bpt id="p1">[</bpt>分散メモリ キャッシュ<ept id="p1">](/aspnet/core/performance/caching/distributed)</ept>」を参照してください。


### <a name="in-memory-token-cache"></a>メモリ内トークン キャッシュ

ASP.NET Core アプリケーション内の <bpt id="p2">[</bpt>Startup<ept id="p2">](/aspnet/core/fundamentals/startup)</ept> クラスの <bpt id="p1">[</bpt>ConfigureServices<ept id="p1">](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)</ept> メソッドでメモリ内キャッシュを使用するコードの例を次に示します。

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

アプリ トークンのみを要求する場合、実働環境では AddInMemoryTokenCaches が適しています。 ユーザー トークンを使用する場合は、分散トークン キャッシュを使用することをお勧めします。 

キャッシュの観点からは、このコードは ASP.NET Core Web アプリと Web API では同じようになります。

### <a name="distributed-token-caches"></a>分散トークン キャッシュ

可能な分散キャッシュの例を次に示します。

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// Distributed token caches have a L1/L2 mechanism.
// L1 is in memory, and L2 is the distributed cache
// implementation that you will choose below.
// You can configure them to limit the memory of the 
// L1 cache, encrypt, and set eviction policies.
services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
  {
    // You can disable the L1 cache if you wish. For instance in some cases where you share the L2 cache
    // between instances of your apps.
    options.DisableL1Cache = false;
    
    // Or limit the memory (by default this is 500 Mb)
    options.SizeLimit = 500 * 1024 * 1024,   // 500 Mb

    // You can choose if you encrypt or not the cache
    options.Encrypt = false;

    // And you can set eviction policies for the distributed
    // cache.
    options.SlidingExpiration = TimeSpan.FromHours(1);
  }

// Then, choose your implementation of distributed cache
// -----------------------------------------------------

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// You can even decide if you want to repair the connection
// with Redis and retry on Redis failures. 
services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
{
  options.OnL2CacheFailure = (ex) =>
  {
    if (ex is StackExchange.Redis.RedisConnectionException)
    {
      // action: try to reconnect or something
      return true; //try to do the cache operation again
    }
    return false;
  };
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

詳細については、次を参照してください。
- [メモリ内キャッシュと分散メモリ内キャッシュの相違点](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#inmemory-vs-distributedmemory-cache-options)
- [分散キャッシュの詳細設定オプション](https://github.com/AzureAD/microsoft-identity-web/wiki/L1-Cache-in-Distributed-(L2)-Token-Cache)
- [L2 キャッシュの削除を処理する](https://github.com/AzureAD/microsoft-identity-web/wiki/Handle-L2-cache-eviction)
- [Docker で Redis キャッシュを設定する](https://github.com/AzureAD/microsoft-identity-web/wiki/Set-up-a-Redis-cache-in-Docker)

分散キャッシュの使用法は、フェーズ [2-2 トークン キャッシュ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)の [ASP.NET Core Web アプリのチュートリアル](/aspnet/core/tutorials/first-mvc-app/)で説明しています。

## <a name="non-aspnet-core-web-apps-and-web-apis"></a><bpt id="p1">[</bpt>ASP.NET Core 以外の Web アプリと Web API<ept id="p1">](#tab/aspnet)</ept>

MSAL.NET を使用する場合でも、Microsoft.Identity.Web.TokenCache に含まれるトークン キャッシュ シリアライザーのベネフィットがあります。 

### <a name="referencing-the-nuget-package"></a>NuGet パッケージの参照

MSAL.NET の代わりに、[Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) NuGet パッケージをプロジェクトに追加します。

### <a name="configuring-the-token-cache"></a>トークン キャッシュの構成

次のコードでは、メモリ内の十分にパーティション分割されたトークン キャッシュをアプリに追加する方法を示します。

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

public static async Task<AuthenticationResult> GetTokenAsync(string clientId, X509Certificate cert, string authority, string[] scopes)
 {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithAuthority(authority)
       .Build();

     // Add a static in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();  // Microsoft.Identity.Web.TokenCache 1.17+
   
     // Make the call to get a token for client_credentials flow (app to app scenario) 
     return await app.AcquireTokenForClient(scopes).ExecuteAsync();
     
     // OR Make the call to get a token for OBO (web api scenario)
     return await app.AcquireTokenOnBehalfOf(scopes, userAssertion).ExecuteAsync();
     
     // OR Make the call to get a token via auth code (web app scenario)
     return await app.AcquireTokenByAuthorizationCode(scopes, authCode);    
     
     // OR, when the user has previously logged in, get a token silently
     var homeAccountId = GetHomeAccountIdFromClaimsPrincipal(); // uid and utid claims
     var account = await app.GetAccountAsync(homeAccountId);
     try
     {
          return await app.AcquireTokenSilent(scopes, account).ExecuteAsync();; 
     } 
     catch (MsalUiRequiredException)
     {
        // cannot get a token silently, so redirect the user to be challenged 
     }
  }
```

### <a name="available-caching-technologies"></a>使用可能なキャッシュ テクノロジ

`app.AddInMemoryTokenCache();` の代わりに、シリアル化なし、メモリ内、.NET によって提供される分散トークン キャッシュ ストレージなど、さまざまなキャッシュ シリアル化テクノロジを使用できます。

#### <a name="no-token-cache-serialization"></a>トークン キャッシュのシリアル化なし

次の場合は、(MSAL.NET 内部キャッシュを使用して) トークン キャッシュのシリアル化を使用しないように指定できます。
- アプリケーションをビルドするときに `.WithCacheOptions(CacheOptions.EnableSharedCacheOptions)` を使用する。
- シリアライザーを追加しない。

```CSharp
    // Create the confidential client application
    app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithCacheOptions(CacheOptions.EnableSharedCacheOptions)
       .WithAuthority(authority)
       .Build();
```

#### <a name="in-memory-token-cache"></a>メモリ内トークン キャッシュ

メモリ内トークン キャッシュのシリアル化は、サンプルでは非常に便利です。 また、Web アプリの再起動時にトークン キャッシュが失われても構わない場合に、アプリ トークン (`AcquireTokenForClient`) のみを要求する場合は、実稼働アプリケーションにも適しています。 ユーザー トークン (`AcquireTokenByAuthorizationCode`、`AcquireTokenSilent`、`AcquireTokenOnBehalfOf`) を要求する場合は、運用環境では推奨されません。

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

メモリ内トークン キャッシュのサイズを制限するオプションを指定することもできます。

```CSharp 
  // Add an in-memory token cache with options
  app.AddInMemoryTokenCache(services =>
  {
      // Configure the memory cache options
      services.Configure<MemoryCacheOptions>(options =>
      {
          options.SizeLimit = 500 * 1024 * 1024; // in bytes (500 Mb)
      });
  }
  );
```


#### <a name="distributed-caches"></a>分散キャッシュ

<ph id="ph1">`app.AddDistributedTokenCache`</ph> を使用している場合、トークン キャッシュは .NET <ph id="ph2">`IDistributedCache`</ph> 実装に対するアダプターであるため、分散メモリ キャッシュ、Redis キャッシュ、CosmosDb、または SQL Server キャッシュから選択できます。 <ph id="ph1">`IDistributedCache`</ph> の実装の詳細については、「<bpt id="p1">[</bpt>分散メモリ キャッシュ<ept id="p1">](/aspnet/core/performance/caching/distributed)</ept>」を参照してください。

##### <a name="distributed-in-memory-token-cache"></a>分散メモリ内トークン キャッシュ

```CSharp 
  // In memory distributed token cache
  app.AddDistributedTokenCache(services =>
  {
    // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
    services.AddDistributedMemoryCache();

    // Distributed token caches have a L1/L2 mechanism.
    // L1 is in memory, and L2 is the distributed cache
    // implentation that you will choose below.
    // You can configure them to limit the memory of the 
    // L1 cache, encrypt, and set eviction policies.
    services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
      {
        // You can disable the L1 cache if you wish
        options.DisableL1Cache = false;
        
        // Or limit the memory (by default this is 500 Mb)
        options.SizeLimit = 500 * 1024 * 1024,   // 500 Mb

        // You can choose if you encrypt or not the cache
        options.Encrypt = false;

        // And you can set eviction policies for the distributed
        // cache
        options.SlidingExpiration = TimeSpan.FromHours(1);
      });
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

      // You can even decide if you want to repair the connection
      // with REDIS and retry on Redis failures. 
      services.Configure<MsalDistributedTokenCacheAdapterOptions>(options => 
      {
        options.OnL2CacheFailure = (ex) =>
        {
          if (ex is StackExchange.Redis.RedisConnectionException)
          {
            // action: try to reconnect or something
            return true; //try to do the cache operation again
          }
          return false;
        };
      });
    });
```

トークンの取得に redis キャッシュのタイムアウトと同じくらいの時間がかかることがある場合は、「<bpt id="p1">[</bpt>キャッシュ同期の無効化<ept id="p1">](#disabling-cache-synchronization)</ept>」も参照してください。 

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

##### <a name="more-about-the-distributed-cache"></a>分散キャッシュの詳細

分散キャッシュの詳細については、以下をご覧ください。
- [メモリ内キャッシュと分散メモリ内キャッシュの相違点](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#inmemory-vs-distributedmemory-cache-options)
- [分散キャッシュの詳細設定オプション](https://github.com/AzureAD/microsoft-identity-web/wiki/L1-Cache-in-Distributed-(L2)-Token-Cache)
- [L2 キャッシュの削除を処理する](https://github.com/AzureAD/microsoft-identity-web/wiki/Handle-L2-cache-eviction)
- [Docker で Redis キャッシュを設定する](https://github.com/AzureAD/microsoft-identity-web/wiki/Set-up-a-Redis-cache-in-Docker)

### <a name="disabling-legacy-token-cache"></a>レガシ トークン キャッシュの無効化

MSAL には、特にレガシ ADAL キャッシュと対話する機能を有効にするための内部コードがいくつかあります。 MSAL と ADAL がサイド バイ サイドで使用されていない (したがって、レガシ キャッシュが使用されていない) 場合、関連するレガシ キャッシュ コードは必要ありません。 MSAL <bpt id="p1">[</bpt>4.25.0<ept id="p1">](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0)</ept> により、レガシ ADAL キャッシュ コードを無効にして、キャッシュ使用パフォーマンスを向上させる機能が追加されます。 レガシ キャッシュを無効にする前と後のパフォーマンスの比較については、pull request <bpt id="p1">[</bpt>#2309<ept id="p1">](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309)</ept> を参照してください。 次のようにアプリケーション ビルダーで <ph id="ph1">`.WithLegacyCacheCompatibility(false)`</ph> を呼び出します。

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>キャッシュ同期の無効化

MSAL では、既定でキャッシュの読み取りとキャッシュの書き込みの間に、機密クライアント アプリケーション レベルでキャッシュ アクセスがロックされます。 このロックは、キャッシュ シリアライザーがタイムアウトするまでの時間が長い場合に問題となることがあり、Redis キャッシュがこれに該当します。 <ph id="ph1">`WithCacheSynchronization`</ph> フラグを false に設定してキャッシュの楽観的ロック戦略を有効にすることで、特に ConfidentialClientApplication オブジェクトが要求間で再利用されるときに、パフォーマンスが向上します。 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```
### <a name="samples"></a>サンプル

- .NET Framework および .NET Core アプリケーションでのトークン キャッシュ シリアライザーの使用方法については、こちらのサンプル <bpt id="p1">[</bpt>ConfidentialClientTokenCache<ept id="p1">](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)</ept> を参照してください 
- 次のサンプルは、同じ手法を使用する ASP.NET Web アプリです: <ph id="ph1">https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect</ph> (<bpt id="p1">[</bpt>WebApp/Utils/MsalAppBuilder.cs<ept id="p1">](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)</ept> を参照)

## <a name="desktop-apps"></a><bpt id="p1">[</bpt>デスクトップ アプリ<ept id="p1">](#tab/desktop)</ept>

デスクトップ アプリケーションでは、クロス プラットフォーム トークン キャッシュを使用することをお勧めします。

#### <a name="cross-platform-token-cache-msal-only"></a>クロス プラットフォーム トークン キャッシュ (MSAL のみ)

MSAL.NET では、Microsoft.Identity.Client.Extensions.Msal という名前の別のライブラリでクロス プラットフォーム トークン キャッシュが提供されています。そのソース コードは、 <ph id="ph1">https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet</ph> から入手できます。

##### <a name="referencing-the-nuget-package"></a>NuGet パッケージの参照

<bpt id="p1">[</bpt>Microsoft.Identity.Client.Extensions.Msal<ept id="p1">](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)</ept> NuGet パッケージを、プロジェクトに追加します。

##### <a name="configuring-the-token-cache"></a>トークン キャッシュの構成

詳細については、<ph id="ph1">https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache</ph> を参照してください。 クロス プラットフォーム トークン キャッシュを使用する例を次に示します。

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


##### <a name="plain-text-fallback-mode"></a>プレーン テキスト フォールバック モード

クロス プラットフォーム トークン キャッシュを使用すると、暗号化されていないトークンをクリア テキストで格納できます。 この機能は、開発環境でデバッグ用途にのみ使用するためのものです。 プレーン テキスト フォールバック モードは、次のコード パターンで使用できます。

```csharp
storageProperties =
    new StorageCreationPropertiesBuilder(
        Config.CacheFileName + ".plaintext",
        Config.CacheDir)
    .WithUnprotectedFile()
    .Build();

var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties).ConfigureAwait(false);
```


## <a name="mobile-apps"></a><bpt id="p1">[</bpt>モバイル アプリ<ept id="p1">](#tab/mobile)</ept>

MSAL.NET では、既定でメモリ内のトークン キャッシュが提供されています。 プラットフォームの一部としてセキュリティで保護されたストレージをユーザーが使用できる次のプラットフォーム用に、既定でシリアル化が提供されています: ユニバーサル Windows プラットフォーム (UWP)、Xamarin.iOS、Xamarin.Android。

## <a name="write-your-own-cache"></a><bpt id="p1">[</bpt>独自のキャッシュを作成する<ept id="p1">](#tab/custom)</ept>

独自のトークン キャッシュ シリアライザーを記述する場合は、MSAL.NET によって、.NET Framework および .NET Core サブプラットフォームでのカスタム トークン キャッシュ シリアル化が提供されています。 キャッシュへのアクセスが行われた場合にイベントが発生し、アプリでは、キャッシュをシリアル化するか逆シリアル化するかを選択できます。 ユーザーを処理する機密クライアント アプリケーション (ユーザーにサインインして Web API を呼び出す Web アプリと、ダウンストリームの Web API を呼び出す Web API) 上には、多くのユーザーがいる場合があり、ユーザーは並列で処理されます。 セキュリティとパフォーマンス上の理由から、ユーザーごとに 1 つのキャッシュをシリアル化することをお勧めします。 シリアル化イベントでは、処理されたユーザーの ID に基づいてキャッシュ キーが計算され、そのユーザーのトークン キャッシュがシリアル化/逆シリアル化されます。

モバイル プラットフォーム (UWP、Xamarin.iOS、Xamarin.Android) ではカスタムのシリアル化を使用できません。 MSAL では、これらのプラットフォームに対して安全性とパフォーマンスに優れたシリアル化メカニズムを既に定義しています。 ただし、.NET デスクトップおよび .NET Core アプリケーションのアーキテクチャにはさまざまなものがあり、MSAL では汎用的なシリアル化メカニズムを実装できません。 たとえば、Web サイトでは Redis Cache にトークンが格納され、デスクトップ アプリでは暗号化ファイルにトークンが格納されたりする場合があります。 したがって、追加設定なしに使用できるシリアル化は提供されていません。 .NET デスクトップまたは .NET Core で永続的なトークン キャッシュ アプリケーションを入手するには、シリアル化をカスタマイズします。

トークン キャッシュのシリアル化では、次のクラスとインターフェイスが使用されます。

- <ph id="ph1">`ITokenCache`</ph> では、トークン キャッシュのシリアル化要求をサブスクライブするイベントと、キャッシュをさまざまな形式 (ADAL v3.0、MSAL 2.x、および MSAL 3.x = ADAL v5.0) でシリアル化または逆シリアル化するメソッドが定義されています。
- <ph id="ph1">`TokenCacheCallback`</ph> は、シリアル化を処理できるようにイベントに渡されるコールバックです。 <ph id="ph1">`TokenCacheNotificationArgs`</ph> の型の引数で呼び出されます。
- <ph id="ph1">`TokenCacheNotificationArgs`</ph> では、アプリケーションの <ph id="ph2">`ClientId`</ph> と、トークンを使用できるユーザーへの参照のみを提供します。

  ![クラス ダイアグラム](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> アプリケーションの <ph id="ph2">`UserTokenCache`</ph> プロパティと <ph id="ph3">`AppTokenCache`</ph> プロパティを呼び出すと、MSAL.NET によりトークン キャッシュが自動的に作成され、<ph id="ph1">`IToken`</ph> キャッシュが提供されます。 インターフェイスを自分で実装することはできません。 カスタムのトークン キャッシュのシリアル化を実装する際には、お客様の責任で次の処理を行います。
> - <ph id="ph1">`BeforeAccess`</ph> と <ph id="ph2">`AfterAccess`</ph> の各 "イベント" (またはこれらの非同期版) に対応します。 <ph id="ph1">`BeforeAccess`</ph> デリゲートではキャッシュの逆シリアル化を行い、<ph id="ph2">`AfterAccess`</ph> デリゲートではキャッシュのシリアル化を行います。
> - これらのイベントの一部では、BLOB が格納されるか、または読み込まれ、イベント引数を介して任意のストレージに渡されます。

<bpt id="p1">[</bpt>パブリック クライアント アプリケーション<ept id="p1">](msal-client-applications.md)</ept> (デスクトップ) と<bpt id="p2">[</bpt>機密クライアント アプリケーション<ept id="p2">](msal-client-applications.md)</ept> (Web アプリ/Web API、デーモン アプリ) のどちらに対してトークン キャッシュのシリアル化を作成しているかに応じて、戦略は異なります。

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Web アプリまたは Web API 用のカスタム トークン キャッシュ (機密クライアント アプリケーション)

機密クライアント アプリケーション用に独自のトークン キャッシュ シリアライザーを記述する場合は、[Microsoft.Identity.Web.MsalAsbtractTokenCacheProvider](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.TokenCache/MsalAbstractTokenCacheProvider.cs) から継承し、`WriteCacheBytesAsync` と `ReadCacheBytesAsync` メソッドをオーバーライドすることをお勧めします。

トークン キャッシュ シリアライザーの例は、<bpt id="p1">[</bpt>Microsoft.Identity.Web/TokenCacheProviders<ept id="p1">](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders)</ept> で提供されています。

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>デスクトップ アプリまたはモバイル アプリ用のカスタム トークン キャッシュ (パブリック クライアント アプリケーション)

MSAL.NET v2.x 以降では、パブリック クライアント用のトークン キャッシュをシリアル化するための複数のオプションが用意されています。 キャッシュは、MSAL.NET 形式にのみシリアル化することができます (この統一形式のキャッシュは、MSAL およびプラットフォーム間で共通です)。  また、ADAL V3 の<bpt id="p1">[</bpt>レガシ<ept id="p1">](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)</ept> トークン キャッシュのシリアル化をサポートすることもできます。

ADAL.NET 3.x、ADAL.NET 5.x、および MSAL.NET の間でシングル サインオン状態を共有するようトークン キャッシュのシリアル化をカスタマイズする方法については、サンプル「<bpt id="p1">[</bpt>active-directory-dotnet-v1-to-v2<ept id="p1">](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)</ept>」を参照してください。

> [!Note]
> MSAL 2.x では、MSAL.NET 1.1.4-preview トークン キャッシュ形式はサポートされなくなりました。 MSAL.NET 1.x を利用するアプリケーションがある場合、ユーザーは再度サインインする必要があります。 または、ADAL 4.x (および 3.x) からの移行がサポートされています。

#### <a name="simple-token-cache-serialization-msal-only"></a>トークン キャッシュの単純なシリアル化 (MSAL のみ)

デスクトップ アプリケーション用のトークン キャッシュについて、カスタムのシリアル化の単純な実装の例を次に示します。 ここでは、ユーザーのトークン キャッシュは、アプリケーションと同じフォルダー内のファイルです。

アプリケーションをビルドした後、<ph id="ph1">`TokenCacheHelper.EnableSerialization()`</ph> メソッドを呼び出し、アプリケーションに <ph id="ph2">`UserTokenCache`</ph> を渡すことで、シリアル化を有効にします。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

<ph id="ph1">`TokenCacheHelper`</ph> ヘルパー クラスは、次のように定義されます。

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

Windows、Mac、Linux で実行されているデスクトップ アプリケーションのパブリック クライアント アプリケーション用の製品品質トークン キャッシュ ファイルベースのシリアライザーは、<bpt id="p1">[</bpt>Microsoft.Identity.Client.Extensions.Msal<ept id="p1">](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)</ept> オープン ソース ライブラリで入手できます。 これを、次の NuGet パッケージからアプリケーションに追加することができます。<bpt id="p1">[</bpt>Microsoft.Identity.Client.Extensions.Msal<ept id="p1">](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)</ept>。

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

## <a name="monitor-cache-hit-ratios-and-cache-performance"></a>キャッシュ ヒット率とキャッシュ パフォーマンスの監視

MSAL は、[AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata) オブジェクトの一部として、重要なメトリックを公開しています。 これらのメトリックをログに記録して、ご自分のアプリケーションの正常性を評価できます。

| メトリック       | 意味     | アラームをトリガーするタイミング    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | MSAL で費やした合計時間 (ネットワーク呼び出しとキャッシュを含む)   | 全体の待機時間が長い場合のアラーム (1 秒以上)。 値はトークン ソースによって異なります。 キャッシュから: 1 回のキャッシュ アクセス。 AAD から: 2 回のキャッシュ アクセス + 1 回の HTTP 呼び出し。 1 回目 (プロセスごと) の呼び出しでは、1 回分の HTTP 呼び出しが追加で発生するため、時間がかかります。 |
|  `DurationInCacheInMs` | アプリ開発者によってカスタマイズされた、トークン キャッシュの読み込みまたは保存に要した時間 (Redis に保存するなど)。| スパイク時のアラーム。 |
|  `DurationInHttpInMs`| AAD への HTTP 呼び出しに要した時間。  | スパイク時のアラーム。|
|  `TokenSource` | トークンのソースを示します。 キャッシュからのトークンの取得が格段に速くなります (例: ～ 700 ms に対して ～ 100 ms)。 キャッシュ ヒット率の監視とアラーム生成に使用できます。 | `DurationTotalInMs` で使用します。 |
|  `CacheRefreshReason` | ID プロバイダーからアクセス トークンをフェッチする理由を指定します。 | `TokenSource` で使用します。 |

## <a name="next-steps"></a>次のステップ

次のサンプルでは、トークン キャッシュのシリアル化を示します。

| サンプル | プラットフォーム | 説明|
| ------ | -------- | ----------- |
|<bpt id="p1">[</bpt>active-directory-dotnet-desktop-msgraph-v2<ept id="p1">](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)</ept> | デスクトップ (WPF) | Microsoft Graph API を呼び出す Windows デスクトップ .NET (WPF) アプリケーション。 ![トポロジの図。Desktop App WPF TodoListClient はトークンを対話形式で取得することで Azure AD に流れます。また、Microsoft Graph に流れます。](media/msal-net-token-cache-serialization/topology.png)|
|<bpt id="p1">[</bpt>active-directory-dotnet-v1-to-v2<ept id="p1">](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)</ept> | デスクトップ (コンソール) | Azure AD v1.0 アプリケーション (ADAL.NET を使用) から Microsoft ID プラットフォーム アプリケーション (MSAL.NET を使用) への移行を示す一連の Visual Studio ソリューション。 特に、<bpt id="p1">[</bpt>トークン キャッシュの移行<ept id="p1">](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)</ept>に関するページと、<bpt id="p2">[</bpt>機密クライアントのトークン キャッシュ<ept id="p2">](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)</ept>に関するページを参照してください |
<bpt id="p1">[</bpt>ms-identity-aspnet-webapp-openidconnect<ept id="p1">](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)</ept> | ASP.NET (net472) | ASP.NET MVC アプリケーションでのトークン キャッシュのシリアル化の例 (MSAL.NET を使用)。 特に、<bpt id="p1">[</bpt>MsalAppBuilder<ept id="p1">](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)</ept> に関するページを参照してください
