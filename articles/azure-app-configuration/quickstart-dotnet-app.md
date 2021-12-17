---
title: .NET Framework を使用した Azure App Configuration のクイック スタート | Microsoft Docs
description: この記事では、Azure App Configuration でコードとは別にアプリケーション設定のストレージと管理を一元化して、.NET Framework アプリを作成します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: f5bd0809de5aa7e7e585662ee0c98feaf9be2e79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245097"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して .NET Framework アプリを作成する

.NET Framework ベースのアプリに Azure App Configuration を組み込むには、2 つの方法があります。
- App Configuration 用の構成ビルダーを使用すると、App Configuration からのデータをアプリ設定に読み込むことができます。 アプリは、いつもと同様に `ConfigurationManager` を使用して構成にアクセスします。 *app.config* または *web.config* ファイルへの更新以外に、コードに変更を加える必要はありません。 このクイックスタートでは、このオプションについて説明します。
- .NET Framework の設計により、アプリ設定はアプリケーションの再起動時にのみ更新できます。 App Configuration .NET プロバイダーは、.NET Standard ライブラリです。 アプリケーションの再起動なしで、構成の動的なキャッシュと更新がサポートされます。 動的構成が不可欠な場合にコードを変更するには、[.NET Framework コンソール アプリ](./enable-dynamic-configuration-dotnet.md)または [ASP.NET Web アプリ](./enable-dynamic-configuration-aspnet-netfx.md)で動的構成の更新を実装する方法に関するチュートリアルを参照してください。

このクイックスタートでは、例として .NET Framework コンソール アプリを使用しますが、同じ手法が ASP.NET Web Forms/MVC アプリにも適用されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 以降](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | キー                        | [値]                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Azure App Configuration からのデータ* |

    **[ラベル]** と **[コンテンツの種類]** は空にしておきます。

## <a name="create-a-net-framework-console-app"></a>.NET Framework コンソール アプリを作成する

1. Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール]** プロジェクトの種類でフィルターを掛け、プロジェクト テンプレート リストから **[コンソール アプリ (.NET Framework)]** と C# を選びます。 **[次へ]** を押します。

1. **[新しいプロジェクトの構成]** で、プロジェクト名を入力します。 **[フレームワーク]** で、 **.NET Framework 4.7.2** 以上を選択します。 **[作成]** をクリックします。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで以下の NuGet パッケージを検索し、プロジェクトに追加します。

    - *Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration* バージョン 1.0.0 以降
    - *Microsoft.Configuration.ConfigurationBuilders.Environment* バージョン 2.0.0 以降
    - *System.Configuration.ConfigurationManager* バージョン 4.6.0 以降

1. プロジェクトの *App.config* ファイルを次のように更新します。

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

   App Configuration ストアの接続文字列は環境変数 `ConnectionString` から読み取られます。 `appSettings` セクションの `configBuilders` プロパティの `MyConfigStore` の前に、`Environment` 構成ビルダーを追加します。

1. *Program.cs* を開き、`ConfigurationManager` を呼び出して App Configuration を使用するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. **ConnectionString** という名前の環境変数を、App Configuration ストアの作成中に取得した読み取り専用のキー接続文字列に設定します。 

    Windows コマンド プロンプトを使用する場合は、次のコマンドを実行します。
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. 変更を有効にするために、Visual Studio を再起動します。 

1. Ctrl + F5 キーを押して、アプリをビルドし、実行します。 コンソールに App Configuration の出力からのメッセージが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、.NET Framework コンソール アプリと共に使用しました。 .NET Framework アプリで構成設定を動的に更新できるようにする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [.NET Framework アプリで動的な構成を有効にする](./enable-dynamic-configuration-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET Web アプリで動的な構成を有効にする](./enable-dynamic-configuration-aspnet-netfx.md)
