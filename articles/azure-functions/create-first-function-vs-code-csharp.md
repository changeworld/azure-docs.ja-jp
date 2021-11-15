---
title: Visual Studio Code を使用して C# 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して C# 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
ms.openlocfilehash: 61ff0df6a69835862491f05c7e568cd1c6da141d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027909"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Azure に C# 関数を作成する

この記事では、Visual Studio Code を使用して、HTTP 要求に応答する C# 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]    

この記事では、.NET 6.0 で実行される HTTP によってトリガーされる関数を作成します。 また、この記事の [CLI ベースのバージョン](create-first-function-cli-csharp.md)もあります。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

## <a name="configure-your-environment"></a>環境を構成する

作業を開始する前に、次の要件が満たされていることを確認します。

+ [.NET 6.0 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) バージョン 4.x。

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

アクティブなサブスクリプションを含む Azure アカウントも必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを C# で作成します。 後からこの記事の中で、関数コードを Azure に発行します。

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    # <a name="in-process"></a>[インプロセス](#tab/in-process) 

    |Prompt|[選択]|
    |--|--|
    |**Select a language for your function project (関数プロジェクトの言語を選択してください)**|`C#` を選択します。|
    | **Select a .NET runtime (.NET ランタイムを選択してください)** | `.NET 6` を選択します。|
    |**Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)**|`HTTP trigger` を選択します。|
    |**Provide a function name (関数名を指定してください)**|「`HttpExample`.|
    |**Provide a namespace (名前空間を指定してください)** | 「`My.Functions`. |
    |**承認レベル**|`Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。|
    |**Select how you would like to open your project (プロジェクトを開く方法を選択してください)**|`Add to workspace` を選択します。|

    # <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

    |Prompt|[選択]|
    |--|--|
    |**Select a language for your function project (関数プロジェクトの言語を選択してください)**|`C#` を選択します。|
    | **Select a .NET runtime (.NET ランタイムを選択してください)** | `.NET 6 Isolated` を選択します。|
    |**Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)**|`HTTP trigger` を選択します。|
    |**Provide a function name (関数名を指定してください)**|「`HttpExample`.|
    |**Provide a namespace (名前空間を指定してください)** | 「`My.Functions`. |
    |**承認レベル**|`Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。|
    |**Select how you would like to open your project (プロジェクトを開く方法を選択してください)**|`Add to workspace` を選択します。|

    ---
    
    > [!NOTE]
    > ランタイム オプションとして .NET 6 が表示されない場合は、次のことを確認してください。
    > 
    > + .NET 6.0 SDK がインストールされていることを確認します。
    > + F1 キーを押して「`Preferences: Open user settings`」と入力し、`Azure Functions: Project Runtime` を検索して、既定のランタイム バージョンを `~4` に変更します。  
    
1. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。

[!INCLUDE [functions-run-function-test-local-vs-code-csharp](../../includes/functions-run-function-test-local-vs-code-csharp.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、Visual Studio Code を使用してプロジェクトを直接 Azure に発行します。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>次のステップ

[Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、Azure Cosmos DB または Azure Queue Storage に接続することによってその関数を拡張します。 他の Azure サービスへの接続について詳しくは、「[Azure Functions の既存の関数にバインドを追加する](add-bindings-existing-function.md?tabs=csharp)」を参照してください。 

# <a name="in-process"></a>[インプロセス](#tab/in-process) 

> [!div class="nextstepaction"]
> [Azure Cosmos DB に接続する](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [Azure Queue Storage に接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Azure Cosmos DB に接続する](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)
> [Azure Queue Storage に接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)

---

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
