---
title: '.NET Framework チュートリアル: Azure App Configuration の動的構成'
description: このチュートリアルでは、Azure App Configuration を使用して、.NET Framework アプリの構成データを動的に更新する方法について説明します。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: d6c3bc0ee45c214419820208598e5721309ea144
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261046"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>チュートリアル: .NET Framework アプリで動的な構成を使用する

App Configuration からのデータは、.NET Framework アプリでアプリ設定として読み込むことができます。 詳細については、[クイックスタート](./quickstart-dotnet-app.md)をご覧ください。 ただし、.NET Framework の設計により、アプリ設定はアプリの再起動時にのみ更新できます。 App Configuration .NET プロバイダーは、.NET Standard ライブラリです。 アプリの再起動なしで、構成の動的なキャッシュと更新がサポートされます。 このチュートリアルでは、.NET Framework コンソール アプリに、動的構成の更新を実装する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration ストアへの変更に合わせて構成を更新するように .NET Framework アプリを設定する。
> * 最新の構成をアプリケーションに挿入する。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 以降](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値を追加します。

    | キー                        | [値]                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Azure App Configuration からのデータ* |

    **[ラベル]** と **[コンテンツの種類]** は空のままにしておきます。

## <a name="create-a-net-framework-console-app"></a>.NET Framework コンソール アプリを作成する

1. Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール]** プロジェクト タイプでフィルター処理し、プロジェクト テンプレート リストから C# で **[コンソール アプリ (.NET Framework)]** を選びます。 **[次へ]** を押します。

1. **[新しいプロジェクトの構成]** で、プロジェクト名を入力します。 **[フレームワーク]** で、 **.NET Framework 4.7.2** 以上を選択します。 **[作成]** をクリックします。

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む
1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで、次の NuGet パッケージの最新バージョンを検索し、プロジェクトに追加します。

   *Microsoft.Extensions.Configuration.AzureAppConfiguration*

1. *Program.cs* を開き、次の名前空間を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 構成に関連したオブジェクトを格納するために 2 つの変数を追加します。

    ```csharp
    private static IConfiguration _configuration;
    private static IConfigurationRefresher _refresher;
    ```

1. 指定した更新オプションを使用して App Configuration に接続するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    // Load all keys that start with `TestApp:`.
                    .Select("TestApp:*")
                    // Configure to reload the key 'TestApp:Settings:Message' if it is modified.
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```

    `ConfigureRefresh` メソッドでは、変更の監視のために App Configuration ストア内のキーが登録されます。 `Register` メソッドには、登録済みのキーが変更された場合にすべての構成値を更新するかどうかを示すために使用できる、省略可能なブール型パラメーター `refreshAll` があります。 この例では、キー *TestApp:Settings:Message* のみが更新されます。 `SetCacheExpiration` メソッドでは、構成の変更を確認するために新しい要求が App Configuration に対して行われるまでに経過する必要がある最小時間を指定します。 この例では、既定の有効期限である 30 秒をオーバーライドし、デモンストレーションの目的で代わりに 10 秒を指定しています。

1. App Configuration からの構成データの更新をトリガーする `PrintMessage()` というメソッドを追加します。

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

    `ConfigureRefresh` メソッドを単独で呼び出しても、構成は自動的に更新されません。 インターフェイス `IConfigurationRefresher` から `TryRefreshAsync` メソッドを呼び出して、更新をトリガーします。 この設計は、アプリケーションがアイドル状態の場合でも App Configuration に送信されるファントム要求を回避するためです。 アプリケーションがアクティブであるとみなす場所に `TryRefreshAsync` 呼び出しを含めることができます。 たとえば、受信メッセージ、注文、複雑なタスクの反復を処理するときなどです。 また、アプリケーションが常にアクティブである場合は、タイマー内に含めることができます。 この例では、Enter キーを押すと `TryRefreshAsync` を呼び出します。 何らかの理由で呼び出し `TryRefreshAsync` が失敗した場合でも、アプリケーションではキャッシュされた構成が引き続き使用されます。 構成されたキャッシュの有効期限が切れて、`TryRefreshAsync` 呼び出しがアプリケーション アクティビティによって再度トリガーされると、もう 1 回試行されます。 `TryRefreshAsync` の呼び出しは、構成されたキャッシュの有効期限が過ぎる前には操作を実行しないので、頻繁に呼び出された場合でも、パフォーマンスへの影響は最小限になります。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

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

1. Ctrl + F5 キーを押して、アプリをビルドし、実行します。

    ![ローカルでのアプリの起動](./media/dotnet-app-run.png)

1. Azure portal で、App Configuration ストアの **[構成エクスプローラー]** に移動し、次のキーの値を更新します。

    | キー                        | [値]                                         |
    |----------------------------|-----------------------------------------------|
    | *TestApp:Settings:Message* | *Azure App Configuration からのデータ - 更新済み* |

1. 実行中のアプリケーションに戻り、Enter キーを押して更新をトリガーし、コマンド プロンプト ウィンドウまたは PowerShell ウィンドウに更新された値を表示します。

    ![ローカルでのアプリの更新](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 更新操作の構成を指定するときに `SetCacheExpiration` メソッドを使ってキャッシュの有効期限を 10 秒に設定したため、構成設定の値は、その設定の前回の更新から少なくとも 10 秒が経過した場合にのみ更新されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように .NET Framework アプリを設定しました。 ASP.NET Web アプリケーション (.NET Framework) で動的構成を有効にする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ASP.NET Web アプリケーションで動的構成を有効にする](./enable-dynamic-configuration-aspnet-netfx.md)

App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
