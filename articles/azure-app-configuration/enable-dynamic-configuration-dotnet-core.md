---
title: チュートリアル:.NET Core アプリで動的な構成を使用する
titleSuffix: Azure App Configuration
description: このチュートリアルでは、.NET Core アプリの構成データを動的に更新する方法を学習します
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: 9a2a8f88cd39b452a54d6fc668737fc1b5be68cc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996969"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>チュートリアル:.NET Core アプリで動的な構成を使用する

App Configuration .NET プロバイダー ライブラリは、アプリケーションを再起動させることなく必要に応じて構成を更新することをサポートします。 このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイックスタートで紹介されているアプリに基づいています。 先に進む前に、[App Configuration を使用した .NET Core アプリの作成](./quickstart-dotnet-core-app.md)を完了しておく必要があります。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration ストアへの変更に合わせて構成を更新するように .NET Core アプリを設定する。
> * 最新の構成をアプリケーションに取り込む。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

クイック スタート「[App Configuration を使用して .NET Core アプリを作成する](./quickstart-dotnet-core-app.md)」を完了します。

## <a name="activity-driven-configuration-refresh"></a>アクティビティ ドリブン構成の更新

*Program.cs* を開き、コードを次のように更新します。

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
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

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

`ConfigureRefresh` メソッドでは、変更の監視のために App Configuration ストア内のキーが登録されます。 `Register` メソッドには、登録済みのキーが変更された場合にすべての構成値を更新するかどうかを示すために使用できる、省略可能なブール型パラメーター `refreshAll` があります。 この例では、キー *TestApp:Settings:Message* のみが更新されます。 `SetCacheExpiration` メソッドでは、構成の変更を確認するために新しい要求が App Configuration に対して行われるまでに経過する必要がある最小時間を指定します。 この例では、既定の有効期限である 30 秒をオーバーライドし、デモンストレーションの目的で代わりに 10 秒を指定しています。

`ConfigureRefresh` メソッドを単独で呼び出しても、構成は自動的に更新されません。 インターフェイス `IConfigurationRefresher` から `TryRefreshAsync` メソッドを呼び出して、更新をトリガーします。 この設計は、アプリケーションがアイドル状態の場合でも App Configuration に送信されるファントム要求を回避するためです。 `TryRefreshAsync` 呼び出しは、アプリケーションをアクティブとみなす場所に含める必要があります。 たとえば、受信メッセージ、注文、複雑なタスクの反復を処理するときなどです。 また、アプリケーションが常にアクティブである場合は、タイマー内に含めることができます。 この例では、Enter キーを押すたびに `TryRefreshAsync` を呼び出します。 何らかの理由で呼び出し `TryRefreshAsync` が失敗した場合でも、アプリケーションではキャッシュされた構成が引き続き使用されます。 構成されたキャッシュの有効期限が切れて、`TryRefreshAsync` 呼び出しがアプリケーション アクティビティによって再度トリガーされると、もう 1 回試行されます。 `TryRefreshAsync` の呼び出しは、構成されたキャッシュの有効期限が過ぎる前には操作を実行しないので、頻繁に呼び出された場合でも、パフォーマンスへの影響は最小限になります。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 次のコマンドを実行して、コンソール アプリをビルドします。

    ```console
     dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、アプリをローカルで実行します。

    ```console
     dotnet run
    ```

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/dotnet-core-app-run.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[Configuration Explorer]\(構成エクスプローラー)** を選択して次のキーの値を更新します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ - 更新済み |

1. Enter キーを押して更新をトリガーし、コマンド プロンプト ウィンドウまたは PowerShell ウィンドウに更新された値を表示します。

    ![クイック スタートのアプリ (ローカルで最新の情報に更新)](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > 更新操作の構成を指定するときに `SetCacheExpiration` メソッドを使ってキャッシュの有効期限を 10 秒に設定したため、構成設定の値は、その設定の前回の更新から少なくとも 10 秒が経過した場合にのみ更新されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように .Net Core アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
