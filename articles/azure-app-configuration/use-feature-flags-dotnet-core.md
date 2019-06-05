---
title: .NET Core アプリでの機能フラグの使用に関するチュートリアル |Microsoft Docs
description: このチュートリアルでは、.NET Core アプリに機能フラグを実装する方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b0e48a0db63eded9e9c4921d33b03af39656ce0d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299259"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>チュートリアル:.NET Core アプリ内で機能フラグを使用する

.NET Core 機能管理ライブラリでは、.NET または ASP.NET Core アプリケーションで機能フラグを実装するための慣用的なサポートが提供されます。 それらにより、機能フラグをより宣言的にコードに追加できるため、機能フラグ用のすべての `if` ステートメントを手動で記述する必要がなくなります。 それらは、機能フラグのライフ サイクルをバックグラウンドで管理します (たとえば、フラグの状態を更新およびキャッシュしたり、要求の呼び出し中にフラグの状態が不変であることを保証したりします)。 さらに、ASP.NET Core ライブラリには、MVC コントローラー アクション、ビュー、ルート、およびミドルウェアを含む、すぐに使用できる統合が用意されています。

「[ASP.NET Core アプリに機能フラグを追加する ](./quickstart-feature-flag-aspnet-core.md)」クイック スタートでは、ASP.NET Core アプリケーションに機能フラグを追加する複数の方法が示されています。 このチュートリアルでは、これらの詳細について説明します。 完全なリファレンスについては、[ASP.NET Core の機能管理に関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2091410)をご覧ください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの主要部分に機能フラグを追加して、機能の可用性を制御します。
> * App Configuration を使用して機能フラグを管理する場合は、App Configuration と統合します。

## <a name="setup"></a>セットアップ

.NET Core 機能マネージャー `IFeatureManager` は、フレームワークのネイティブ構成システムから機能フラグを取得します。 その結果、.NET Core がサポートする任意の構成ソース (ローカルの *appsettings.json* ファイルや環境変数など) を使用して、アプリケーションの機能フラグを定義できます。 機能マネージャーは、.NET Core の依存関係の挿入に依存します。 機能管理サービスは、標準の規則を使用して登録できます。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

機能マネージャーは、既定では .NET Core 構成データの "FeatureManagement" セクションから機能フラグを取得します。 次の例では、代わりに "MyFeatureFlags" という別のセクションから読み取るように指示しています。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

機能フラグでフィルターを使用する場合は、追加のライブラリを含めて、それを登録する必要があります。 次の例は、 **"PercentageFilter"** という組み込みの機能フィルターを使用する方法を示しています。

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

効果的に操作するには、機能フラグをアプリケーションの外部で保持し、それらを個別に管理する必要があります。 そうすることで、フラグの状態をいつでも変更して、アプリケーションにその変更をすぐに反映させることができます。 App Configuration は、専用のポータル UI を使用してすべての機能フラグを整理および制御するための一元的な場所を提供し、.NET Core クライアント ライブラリを使用して直接アプリケーションにフラグを配信します。 App Configuration に ASP.NET Core アプリケーションを接続する最も簡単な方法は、構成プロバイダー `Microsoft.Extensions.Configuration.AzureAppConfiguration` を使用することです。 コードでこの NuGet パッケージを使用するには、以下を *Program.cs* ファイルに追加します。

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

機能フラグの値は、時間の経過とともに変化することが予想されます。 既定では、機能マネージャーは 30 秒ごとに機能フラグの値を更新します。 上記の `options.UseFeatureFlags()` 呼び出しでは、別のポーリング間隔を使用できます。

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>機能フラグの宣言

各機能フラグには 2 つの部分があります。1 つは名前で、もう 1 つは、機能の状態が "*オン*" である (つまり、その値が `True` である) かどうかの評価に使用される 1 つ以上のフィルターの一覧です。 フィルターは、機能をオンにする必要がある対象のユース ケースを定義します。 機能フラグに複数のフィルターがある場合、フィルターのいずれかがその機能を有効にする必要があると判断するまで、フィルターの一覧が順番に走査されます。 この時点で、機能フラグは "*オン*" と見なされ、残りのフィルターの結果はすべてスキップされます。 どのフィルターも機能を有効にすることを示していない場合、機能フラグは "*オフ*" になります。

機能マネージャーは、*appsettings.json* を機能フラグの構成ソースとしてサポートしています。 次の例は、json ファイルでの機能フラグの設定方法を示しています。

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

慣例により、この json ドキュメントの `FeatureManagement` セクションが機能フラグの設定に使用されます。 上記の例は、*EnabledFor* プロパティで定義されたフィルターを持つ 3 つの機能フラグを示しています。

* **FeatureA** は "*オン*" です。
* **FeatureB** は "*オフ*" です。
* **FeatureC** は、*Percentage* という名前のフィルターを *Parameters* プロパティとともに指定しています。 *Percentage* は、構成可能なフィルターの例であり、**FeatureC** フラグが "*オン*" になる確率を 50% に指定します。

## <a name="referencing"></a>参照

必須ではありませんが、機能フラグは、コードで簡単に参照できるように `enum` 変数として定義してください。

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>機能フラグのチェック

機能管理の基本的なパターンでは、最初に機能フラグが "*オン*" に設定されているかどうかをチェックし、該当する場合は、含まれるアクションを実行します。

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>依存関係の挿入

ASP.NET Core MVC では、依存関係の挿入を介して機能マネージャー `IFeatureManager` にアクセスできます。

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>コントローラー アクション

MVC コントローラーでは、`Feature` 属性を使用して、コントローラー クラス全体を有効にするか、特定のアクションを有効にするかを制御できます。 次の `HomeController` コントローラーでは、そのコントローラーに含まれるアクションを実行するには、*FeatureA* が "*オン*" になっている必要があります。

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

次の `Index` アクションを実行するには、*FeatureA* が "*オン*" になっている必要があります。

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

制御する機能フラグが "*オフ*" であるために MVC コントローラーまたはアクションがブロックされている場合、登録されている `IDisabledFeatureHandler` が呼び出されます。 既定の `IDisabledFeatureHandler` は、応答本文なしで 404 状態コードをクライアントに返します。

## <a name="view"></a>表示

MVC ビューでは、`<feature>` タグを使用して、機能フラグが有効か無効かに基づいてコンテンツをレンダリングできます。

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>MVC フィルター

MVC フィルターは、機能フラグの状態に基づいてアクティブになるように設定できます。 以下は、`SomeMvcFilter` という名前の MVC フィルターを追加します。 このフィルターは、*FeatureA* が有効になっている場合のみ、MVC パイプライン内でトリガーされます。

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>ルート

ルートは、機能フラグに基づいて動的に公開することができます。 以下は、*FeatureA* が有効になっている場合にのみ、`Beta` を既定のコントローラーとして設定するルートを追加します。

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>ミドルウェア

機能フラグを使用して、アプリケーションの分岐とミドルウェアを条件付きで追加できます。 以下は、*FeatureA* が有効になっている場合にのみ、要求パイプラインにミドルウェア コンポーネントを挿入します。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

以下は、機能フラグに基づいてアプリケーション全体を分岐させる、より汎用的な機能を構築します。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、`Microsoft.FeatureManagement` ライブラリを使用して ASP.NET Core アプリケーションに機能フラグを実装する方法を説明しました。 ASP.NET Core と App Configuration での機能管理サポートの詳細については、次のリソースをご覧ください。

* [ASP.NET Core の機能フラグのサンプル コード](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement のドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [機能フラグを管理する](./manage-feature-flags.md)
