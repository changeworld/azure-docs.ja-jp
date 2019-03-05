---
title: ASP.NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: ASP.NET Core で Azure App Configuration を使用する場合のクイック スタートです
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961484"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して ASP.NET Core アプリを作成する

Azure App Configuration は、Azure 内にあるマネージド構成サービスです。 すべてのアプリケーション設定を、お使いのコードとは別の 1 つの場所に簡単に保存して管理することが可能です。 このクイック スタートでは、ASP.NET Core Web アプリにサービスを組み込む方法を示します。 ASP.NET Core では、アプリケーションによって指定される 1 つ以上のデータ ソース ("*構成プロバイダー*" と呼ばれます) からの設定を使用して、キーと値に基づく 1 つの構成オブジェクトが作成されます。 App Configuration の .NET Core クライアントはそのようなプロバイダーとして実装されるので、サービスは他のデータ ソースと同じように表示されます。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できるという点です。

1. プロジェクト用の新規フォルダーを作成します。 このクイック スタートでは、*TestAppConfig* という名前にします。

2. 新しいフォルダーで次のコマンドを実行して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。

        dotnet new mvc

## <a name="add-secret-manager"></a>シークレット マネージャーを追加する

プロジェクトに[シークレット マネージャー ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets)を追加します。 シークレット マネージャー ツールは、開発作業の機密データをプロジェクト ツリーの外部に格納します。 これにより、ソース コード内のアプリ シークレットが偶発的に共有されるのを防止できます。

- *.csproj* ファイルを開きます。 次に示すように、`UserSecretsId` 要素を追加し、その値を自分の値 (通常は GUID) に置き換えます。 ファイルを保存します。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-app-configuration-store"></a>アプリ構成ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet パッケージへの参照を追加します。

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

        dotnet restore

3. シークレット マネージャーに、*ConnectionStrings:AppConfig* という名前のシークレットを追加します。

    このシークレットには、アプリ構成ストアにアクセスするための接続文字列が格納されます。 下記のコマンドの値を、自分のアプリ構成ストアの接続文字列に置き換えます。

    このコマンドは、*.csproj* ファイルと同じディレクトリで実行する必要があります。

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    シークレット マネージャーは、ローカル環境での Web アプリのテスト用にのみ使用されます。 アプリをデプロイするときは (たとえば、[Azure App Service](https://azure.microsoft.com/services/app-service/web) に)、シークレット マネージャーで接続文字列を格納する代わりに、アプリケーションの設定 (たとえば、App Service での**接続文字列**) を使用します。

    このシークレットには構成 API でアクセスします。 サポートされているすべてのプラットフォームで構成 API を使用する構成名にコロン (:) を使用できます。[環境別の構成](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment)に関するページを参照してください。

4. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを呼び出すことで、App Configuration を使用するように `CreateWebHostBuilder` メソッドを更新します。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. *Views* > *Home* ディレクトリにある *Index.cshtml* を開いて、内容を次のコードに置き換えます。

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. *Views* > *Shared* ディレクトリにある *_Layout.cshtml* を開いて、内容を次のコードに置き換えます。

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドし、ローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

        dotnet build

2. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

3. ブラウザー ウィンドウを起動して `http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しいアプリ構成ストアを作成して、ASP.NET Core Web アプリと共に使用しました。 App Configuration の使用についてさらに学習するには、認証について示した次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID の統合](./integrate-azure-managed-service-identity.md)
