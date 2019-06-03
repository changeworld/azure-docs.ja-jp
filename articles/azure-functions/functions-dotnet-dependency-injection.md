---
title: .NET Azure Functions で依存関係の挿入を使用する
description: .NET 関数にサービスを登録して使用するために、依存関係の挿入を使用する方法について学習します
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure 関数、関数、サーバーレス アーキテクチャ
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408445"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions で依存関係の挿入を使用する

Azure Functions では、依存関係の挿入 (DI) ソフトウェア デザイン パターンがサポートされています。これは、クラスと依存関係の間で[制御の反転 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための技術です。

Azure Functions は、ASP.NET Core の依存関係の挿入機能の上にビルドされます。  関数で使用する前に、[ASP.NET Core の依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)のサービス、有効期間、デザイン パターンを理解する必要があります。

## <a name="installing-dependency-injection-packages"></a>依存関係の挿入パッケージをインストールする

依存関係の挿入機能を使用するには、それらの API を公開する NuGet パッケージを含める必要があります。

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>サービスを登録する

サービスを登録するには、構成メソッドを作成して、`IFunctionsHostBuilder` インスタンスにコンポーネントを追加します。  Azure Functions ホストにより、`IFunctionsHostBuilder` が作成され、構成したメソッドに直接渡されます。

構成メソッドを登録するには、`FunctionsStartup` 属性を使用して構成メソッドの種類を指定するアセンブリ属性を追加する必要があります。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="service-lifetimes"></a>サービスの有効期間

Azure Function アプリでは、[ASP.NET の依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)と同じサービスの有効期間 (一時、範囲指定、シングルトン) が提供されます。

関数アプリでは、スコープ付きサービスの有効期間は、関数の実行の有効期間に一致します。 スコープ付きサービスは毎回作成されます。  実行中のサービスに対する後続の要求では、そのインスタンスを再利用します。  シングルトン サービスの有効期間はホストの有効期間に一致し、そのインスタンスでの関数の実行全体で再利用されます。

接続とクライアント (`SqlConnection`、`CloudBlobClient`、または `HttpClient`) には、シングルトンの有効期間サービスをお勧めします。

[さまざまなサービスの有効期間のサンプル](https://aka.ms/functions/di-sample)を表示するか、ダウンロードします。

## <a name="logging-services"></a>ログ記録サービス

ご自分のログ記録プロバイダーが必要な場合は、`ILoggerProvider` を登録する方法をお勧めします。  Application Insights の場合、Functions によって Application Insights が自動的に追加されます。  

> [!WARNING]
> 環境によって提供されるものと競合するサービスが登録されるため、サービス コレクションに `AddApplicationInsightsTelemetry()` を追加しないでください。 
 
## <a name="function-app-provided-services"></a>関数アプリで提供されるサービス

関数ホストでは、それ自体で多くのサービスを登録します。  依存関係を取得するのに安全なサービスは次のとおりです。  登録や依存にその他のホスト サービスはサポートされていません。  依存関係を取得する必要があるその他のサービスがある場合、[GitHub 上でイシューとディスカッションを作成](https://github.com/azure/azure-functions-host)してください。

|サービスの種類|有効期間|説明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|シングルトン|実行時の構成|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|シングルトン|ホスト インスタンスの ID を提供する責任がある|

### <a name="overriding-host-services"></a>ホスト サービスのオーバーライド

ホストによって提供されるサービスのオーバーライドは、現在サポートされていません。  オーバーライドが必要なサービスがある場合、[GitHub 上でイシューとディスカッションを作成](https://github.com/azure/azure-functions-host)してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [関数アプリを監視する方法](functions-monitoring.md)
* [関数のベスト プラクティス](functions-best-practices.md)