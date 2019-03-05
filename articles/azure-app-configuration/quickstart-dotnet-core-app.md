---
title: .NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: .NET Core で Azure App Configuration を使用する場合のクイック スタートです
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960677"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>クイック スタート:App Configuration を使用して .NET Core アプリを作成する

Azure App Configuration は、Azure 内にあるマネージド構成サービスです。 すべてのアプリケーション設定を、お使いのコードとは別の 1 つの場所に簡単に保存して管理することが可能です。 このクイック スタートでは、.NET Core コンソール アプリにサービスを組み込む方法を示します。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>.NET Core コンソール アプリを作成する

[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、新しい .NET Core コンソール アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できるという点です。

1. プロジェクト用の新規フォルダーを作成します。

2. 新しいフォルダーで次のコマンドを実行して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>アプリ構成ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet パッケージへの参照を追加します。

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

        dotnet restore

3. *Program.cs* を開き、`builder.AddAzureAppConfiguration()` メソッドを呼び出すことで、App Configuration を使用するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドし、ローカルで実行する

1. **ConnectionString** という名前の環境変数に、アプリ構成ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用している場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell を使用している場合は、次のコマンドを実行します。

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS または Linux を使用している場合は、次のコマンドを実行します。

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 次のコマンドを実行して、コンソール アプリをビルドします。

        dotnet build

3. ビルドが正常に完了したら、次のコマンドを実行して、アプリをローカル環境で実行します。

        dotnet run

    ![クイック スタートのアプリの実行](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しいアプリ構成ストアを作成して、.NET Core コンソール アプリと共に使用しました。 App Configuration の使用についてさらに学習するには、認証について示した次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID の統合](./integrate-azure-managed-service-identity.md)
