---
title: Azure App Configuration を使用した継続的インテグレーションと配信パイプラインの統合 | Microsoft Docs
description: 継続的インテグレーションと配信中に Azure App Configuration のデータを使用して構成ファイルを生成する方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224330"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD パイプラインとの統合

Azure App Configuration にアクセスできなくなるリモートの可能性に対して、アプリケーションの回復性を強化することができます。 そのためには、現在の構成データを、アプリケーションと共にデプロイされ、起動時にローカルで読み込まれるファイルにパッケージ化します。 このアプローチを利用すると、少なくともアプリケーションの既定値が確実に設定されます。 これらの値は、アプリ構成ストアが使用できる状態になったときに、新しい変更によって上書きされます。

Azure App Configuration の[エクスポート](./howto-import-export-data.md#export-data)機能を使用して、現在の構成データを 1 つのファイルとして取得するプロセスを自動化できます。 次に、継続的インテグレーションと継続的配信 (CI/CD) パイプラインのビルドまたはデプロイ手順にこのファイルを組み込みます。

クイック スタートで紹介した Web アプリのビルド手順として App Configuration データを含める方法の例を次に示します。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

## <a name="prerequisites"></a>前提条件

ローカルでビルドする場合、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をまだインストールしていない場合は、ダウンロードしてインストールします。

(たとえば Azure DevOps で) クラウド ビルドを実行するには、必ず [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をビルド システムにインストールします。

## <a name="export-an-app-configuration-store"></a>アプリ構成ストアをエクスポートする

1. *.csproj* ファイルを開き、次のスクリプトを追加します。

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    アプリ構成ストアに関連付けられている *ConnectionString* を環境変数として追加します。

2. Program.cs を開き、`config.AddJsonFile()` メソッドを呼び出すことで、エクスポートされた JSON ファイルを使用するように `CreateWebHostBuilder` メソッドを更新します。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数に、アプリ構成ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS または Linux を使用する場合は、次のコマンドを実行します。

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

        dotnet build

3. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

4. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>次の手順

* [Azure リソースのマネージド ID の統合](./integrate-azure-managed-service-identity.md)
