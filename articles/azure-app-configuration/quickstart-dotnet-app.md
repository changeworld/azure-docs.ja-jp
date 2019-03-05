---
title: .NET Framework を使用した Azure App Configuration のクイック スタート | Microsoft Docs
description: .NET Framework アプリと共に Azure App Configuration を使用する場合のクイック スタート
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962164"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して .NET Framework アプリを作成する

Azure App Configuration は、Azure 内にあるマネージド構成サービスです。 すべてのアプリケーション設定を、お使いのコードとは別の 1 つの場所に簡単に保存して管理することが可能です。 このクイック スタートでは、.NET Framework ベースの Windows デスクトップ コンソール アプリにサービスを組み込む方法を示します。

![クイック スタート完了 (ローカル)](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、まだお持ちでない場合、[Visual Studio 2017](https://visualstudio.microsoft.com/vs) および [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) 以降をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>.NET コンソール アプリを作成する

1. Visual Studio を起動して、**[ファイル]** > **[新規]** > **[プロジェクト]** の順に選択します。

2. **[新しいプロジェクト]** ダイアログで、**[インストール済み]** を選択し、**[Visual C#]** > **[Windows デスクトップ]** の順に展開し、**[コンソール アプリ (.NET Framework)]** を選択して、プロジェクトの **[名前]** を入力し、**[.NET Framework 4.7.1]** またはそれ以降を選択して、**[OK]** をクリックします。

## <a name="connect-to-app-configuration-store"></a>アプリ構成ストアに接続する

1. プロジェクトを右クリックし、**[NuGet パッケージの管理...]** を選択します。**[参照]** タブで、次の NuGet パッケージを検索して、お使いのプロジェクトに追加します (見つからない場合は、**[プレリリースを含める]** チェック ボックスをオンにします)。
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. お使いのプロジェクトの *App.config* ファイルを次のように更新します。

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   ここでは、環境変数 `ConnectionString` からアプリ構成ストアの接続文字列を読み込むことになるので、`appSettings` セクションの `configBuilders` プロパティにある `MyConfigStore` の前に、`Environment` 構成ビルダーを追加することが重要である点に注意してください。

3. *Program.cs* を開き、`ConfigurationManager` を呼び出して App Configuration を使用するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数をアプリ構成ストアの接続文字列に設定します。 Windows コマンド プロンプトを使用している場合は、次のコマンドを実行します。

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell を使用している場合は、次のコマンドを実行します。

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Visual Studio を再起動して変更を有効にしてから、キーボード上で **Ctrl + F5** を押して、コンソール アプリをビルドして実行します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しいアプリ構成ストアを作成して、.NET Framework コンソール アプリと共に使用しました。 App Configuration の使用についてさらに学習するには、認証について示した次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID の統合](./integrate-azure-managed-service-identity.md)
