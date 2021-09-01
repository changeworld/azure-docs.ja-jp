---
title: Azure Cosmos DB を ASP.NET セッション状態プロバイダーとキャッシュ プロバイダーとして使用する
description: Azure Cosmos DB を ASP.NET セッション状態プロバイダーとキャッシュ プロバイダーとして使用する方法について説明します
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: 9a7b7ad15d1d4850e910010e4fdc195e474d10af
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113934"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Azure Cosmos DB を ASP.NET セッション状態プロバイダーとキャッシュ プロバイダーとして使用する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB セッションとキャッシュ プロバイダーにより、その短い待機時間とグローバル スケール機能を活用して、セッション状態データを格納し、アプリケーション内の分散キャッシュとして Azure Cosmos DB を使用することができます。

## <a name="what-is-session-state"></a>セッション状態とは

[セッション状態](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true)とは、同じブラウザー内で、一定期間に Web アプリケーションを参照しているユーザーを追跡するユーザー データのことです。 セッション状態は有効期限があり、特定のブラウザーによる相互作用に限定され、ブラウザー間には拡張されません。 一時的なデータと見なされ、存在しない場合でもアプリケーションは中断されません。 ただし、存在する場合は、Web アプリケーションが同じユーザーのブラウザー要求ごとにフェッチする必要がないため、ユーザーのエクスペリエンスが向上します。

多くの場合、これは何らかのストレージ メカニズムによって支えられています。これは、場合によっては使用される Web サーバーの外部にあり、同じブラウザーからの要求を複数の Web サーバー間にまたがって負荷分散することで、より高いスケーラビリティを実現します。

最も単純なセッション状態プロバイダーは、ローカル Web サーバーのメモリにのみデータを格納し、アプリケーションが[アプリケーション要求ルーティング処理](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)を使用する必要があるメモリ内プロバイダーです。 これにより、ブラウザー セッションは特定の Web サーバーに固定されます (そのブラウザーのすべての要求は、常に同じ Web サーバーに配送される必要があります)。 プロバイダーは単純なシナリオではうまく機能しますが、Web アプリケーションの規模が拡大したときに、持続性の要件により負荷分散の問題が発生する可能性があります。

多くの外部ストレージ プロバイダーが使用可能で、セッションの持続性を必要とせずに、複数の Web サーバーで読み取りおよびアクセスできる方法でセッション データを格納でき、より高いスケールを実現できます。

## <a name="session-state-scenarios"></a>セッション状態のシナリオ

Cosmos DB は、拡張パッケージ [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos) を介して [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-standard.md) を利用することで、セッション状態プロバイダーとして使用できます。キーがセッション識別子であるキー/値のアプローチに基づいて効果的なセッション ストレージとしてコンテナーを使用します。

パッケージが追加されると、スタートアップ プロセスの一部として `AddCosmosCache` を使用できます (services.AddSession と app.UseSession は、セッション状態プロバイダーに必要な[一般的な初期化](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true)のステップです)。

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

ここでは、セッション状態を格納するデータベースとコンテナーを指定し、存在しない場合は必要に応じて作成します。

SDK のクライアント構成は `CosmosClientBuilder` を使用してカスタマイズすることができます。また、アプリケーションがすでに `CosmosClient` を使用して Cosmos DB との他の操作を行っている場合は、それをプロバイダーに挿入することもできます。

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

その後、他のプロバイダーと同様の ASP.NET Core セッションを使用して、HttpContext.Session オブジェクトを使用できます。 [ASP.NET 推奨事項](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true)に従って、常にセッション情報を非同期で読み込むようにしてください。

##  <a name="distributed-cache-scenarios"></a>分散キャッシュのシナリオ

Cosmos DB プロバイダーが[分散キャッシュ プロバイダーとして機能する IDistributedCache インターフェイス](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)を実装している場合、パフォーマンスが高い分散セッション状態プロバイダーを必要とする Web アプリケーションだけでなく、分散キャッシュを必要とするアプリケーションにも使用できます。

分散キャッシュでは、独立したインスタンスがキャッシュされたデータを共有できるように、データの整合性が必要です。 Cosmos DB プロバイダーを使用する場合、次のことができます。

- [アプリケーション要求ルーティング処理](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)を有効にして、特定のインスタンスに対して要求をスティッキーにできる場合は、**セッション整合性** で Cosmos DB アカウントを使用します。
- 要求の持続性が必要ないように、**有界整合性制約または厳密な整合性** で Cosmos DB アカウントを使用してください。 これにより、インスタンス全体の負荷分散の観点から最大のスケールを実現します。

Cosmos DB プロバイダーを分散キャッシュとして使用するには、`services.AddCosmosCache` 呼び出しを使用して `ConfiguredService` に登録する必要があります。 これが完了すると、アプリケーション内の任意のコンストラクターが `IDistributedCache` の参照によってキャッシュを要求し、使用される[依存関係の挿入](/dotnet/core/extensions/dependency-injection)によって挿入されたインスタンスを受け取ります。

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>トラブルシューティングと診断
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cosmos DB プロバイダーは、基盤になっている .NET SDK を使用しているため、潜在的な問題を理解するには、既存の[パフォーマンス ガイドライン](performance-tips-dotnet-sdk-v3-sql.md)と[トラブルシューティング ガイド](troubleshoot-dot-net-sdk.md)がすべて適用されます。 IDistributedCache API を通じて公開することはできないため、基になる Cosmos DB 操作から診断にアクセスするための独自の方法があることに注意してください。

オプションの診断デリゲートを登録すると、高待機時間などの状況をトラブルシューティングするために、診断をキャプチャして条件付きでログに記録することができます。

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>次のステップ
- Azure Cosmos DB セッションとキャッシュ プロバイダーの詳細については、[GitHub のソース コード](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/)を参照してください。
- 「ASP.NET Core Web アプリケーションを探索する」のサンプルを参照して、Azure Cosmos DB セッションとキャッシュ プロバイダーを[試してみてください](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample)。
- 詳細については、.NET の「[分散キャッシュ](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)」を参照してください。
