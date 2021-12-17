---
title: ラベルを使用して環境ごとの構成値を提供します。
titleSuffix: Azure App Configuration
description: この記事では、ラベルを使用して、アプリが現在実行されている環境のアプリ構成値を取得する方法について説明します。
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: e6d9aadff5fba66aef260c674f5a01904b289da2
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592360"
---
# <a name="use-labels-to-provide-per-environment-configuration-values"></a>ラベルを使用して環境ごとの構成値を提供します。

多くのアプリケーションでは、異なる環境に対して異なる構成を使用する必要があります。 アプリケーションに、バックエンド データベースに使用する接続文字列を定義する構成値があるとします。 アプリケーション開発者は、運用環境で使用されているものとは異なるデータベースを使用します。 アプリケーションを開発環境から運用環境に移行するときに、アプリケーションで使用されているデータベース接続文字列を変更する必要があります。

Azure App Configuration では、"*ラベル*" を使用することで、同じキーに対して異なる値を定義できます。 たとえば、開発環境用と運用環境用で異なる値を持つ 1 つのキーを定義できます。 App Configuration に接続するときに読み込むラベルを指定できます。

この機能を示すために、「[クイックスタート: Azure App Configuration を使用して ASP.NET Core アプリを作成する](./quickstart-aspnet-core-app.md)」で作成した Web アプリを、開発と運用で異なる構成設定を使用するように変更します。 先に進む前にクイックスタートを完了してください。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>構成値を追加するときにラベルを指定する

Azure portal で **[構成エクスプローラー]** に移動し、クイックスタートで作成した *TestApp:Settings:FontColor* キーを見つけます。 そのコンテキスト メニューを選択して、 **[値の追加]** を選択します。

> [!div class="mx-imgBorder"]
> ![[値の追加] メニュー項目](media/labels-add-value.png)

**[値の追加]** 画面で、 **[値]** に「**red**」と入力し、 **[ラベル]** に「**Development**」と入力します。 **[コンテンツの種類]** は空のままにします。 **[適用]** を選択します。

## <a name="load-configuration-values-with-a-specified-label"></a>指定したラベルの構成値を読み込む

既定の場合、Azure App Configuration ではラベルのない構成値のみが読み込まれます。 構成値に対してラベルを定義した場合、App Configuration に接続するときに使用するラベルを指定できます。

前のセクションで、開発環境用に別の構成値を作成しました。 アプリが現在実行されている環境を動的に判断するには、`HostingEnvironment.EnvironmentName` 変数を使用します。 詳しくは、「[ASP.NET Core で複数の環境を使用する](/aspnet/core/fundamentals/environments)」をご覧ください。

[KeyFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.keyfilter) および [LabelFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.labelfilter) クラスにアクセスするための [Microsoft.Extensions.Configuration.AzureAppConfiguration](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration) 名前空間への参照を追加します。

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
``` 

環境名を `Select` メソッドに渡すことにより、現在の環境に対応するラベルを使用して構成値を読み込みます。

### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>();
```
---


> [!IMPORTANT]
> 前のコード スニペットでは、シークレット マネージャー ツールを使用して、App Configuration の接続文字列を読み込みます。 シークレット マネージャーを使用して接続文字列を保存する方法については、「[ASP.NET Core による Azure App Configuration のクイック スタート](quickstart-aspnet-core-app.md)」を参照してください。

`Select` メソッドは 2 回呼び出されます。 1 回目では、ラベルのない構成値が読み込まれます。 次に、現在の環境に対応するラベルを持つ構成値が読み込まれます。 これらの環境固有の値により、ラベルなしに対応する値はオーバーライドされます。 すべてのキーに対して環境固有の値を定義する必要はありません。 現在の環境に対応するラベルを持つ値がキーにない場合は、ラベルなしの値が使用されます。

## <a name="test-in-different-environments"></a>さまざまな環境でテストする

`Properties` ディレクトリにある `launchSettings.json` ファイルを開きます。 `profiles` で `config` エントリを見つけます。 `environmentVariables` セクションで、`ASPNETCORE_ENVIRONMENT` 変数を `Production` に設定します。

新しい値を設定したら、アプリケーションをビルドして実行します。

```dotnetcli
dotnet build
dotnet run
```

Web ブラウザーを使用して `http://localhost:5000` に移動します。 フォントの色が黒であることがわかります。

![運用環境の構成で実行されている Web アプリケーション](media/labels-website-prod.png)

`launchSettings.json` を更新して、`ASPNETCORE_ENVIRONMENT` 変数を `Development` に設定します。 `dotnet run` をもう一度実行します。 

フォントの色が今度は赤であることがわかります。 これは、アプリケーションで `Development` というラベルを持つ `TestApp:Settings:FontColor` の値が使用されるようになったためです。 他のすべての構成値は、運用環境の値と同じままです。

![開発環境の構成で実行されている Web アプリケーション](media/labels-website-dev.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ASP.NET Core での構成](/aspnet/core/fundamentals/configuration/)
