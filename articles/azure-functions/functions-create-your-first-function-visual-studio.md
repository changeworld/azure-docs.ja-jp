---
title: 'クイック スタート: Visual Studio を使用して Azure で初めての C# 関数を作成する'
description: このクイックスタートでは、Visual Studio を使用して、.NET Core 3.1 で実行されて C# HTTP でトリガーされる関数を作成し、Azure Functions に発行する方法について説明します。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 05/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 5a7df784ec30b958eb6924de674e09cbbe21c91e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830034"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>クイック スタート: Visual Studio を使用して Azure で初めての C# 関数を作成する

Azure Functions を使用すると、Azure のサーバーレス環境で C# コードを実行できます。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Visual Studio を使用して、C# クラス ライブラリ (.NET Core 3.1) プロジェクトを作成します。
> * HTTP 要求に応答する関数を作成します。 
> * コードをローカル環境で実行して、関数の動作を確認します。
> * コード プロジェクトを Azure Functions にデプロイします。 
 
この記事では、次の 2 種類のコンパイル済み C# 関数の作成をサポートしています。 

+ [インプロセス](functions-create-your-first-function-visual-studio.md?tabs=in-process) - Functions ホストプロセスと同じプロセスで実行されます。 詳細については、「[Azure Functions を使用する C# クラス ライブラリ関数を開発する](functions-dotnet-class-library.md)」を参照してください。
+ [分離プロセス](functions-create-your-first-function-visual-studio.md?tabs=isolated-process) - 別の .NET ワーカー プロセスで実行されます。 詳細については、「[Azure において関数を .NET 5.0 で実行するためのガイド](dotnet-isolated-process-guide.md)」を参照してください。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-csharp.md)も存在します。

## <a name="prerequisites"></a>前提条件

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/)。 インストール時に必ず **[Azure の開発]** ワークロードを選択してください。 

+ [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 アカウントをまだ持っていない場合は、始める前に[無料アカウントを作成](https://azure.microsoft.com/free/dotnet/)してください。

## <a name="create-a-function-app-project"></a>関数アプリ プロジェクトを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio によってプロジェクトとクラスが作成されます。クラスの中には、HTTP トリガー関数型のスケルトン コードが含まれています。 スケルトン コードは、要求本文またはクエリ文字列の値を含む HTTP 応答を送信します。 `HttpTrigger`属性は、関数が HTTP 要求によってトリガーされることを指定します。 

## <a name="rename-the-function"></a>フォルダーの名前を変更する

`FunctionName` メソッド属性は、関数の名前を設定します。これは、既定では `Function1` として生成されます。 このツールでは、プロジェクトを作成するときに既定の関数名をオーバーライドすることはできないため、ここで関数クラス、ファイル、およびメタデータに対してより適切な名前を指定します。

1. **エクスプローラー** で Function1.cs ファイルを右クリックし、`HttpExample.cs` という名前に変更します。

1. コードで、Function1 クラスの名前を `HttpExample` に変更します。

1. `Run` という名前の `HttpTrigger` メソッドで、`FunctionName` メソッド属性の名前を `HttpExample` に変更します。 

関数の定義は次のコードのようになります。

# <a name="in-process"></a>[インプロセス](#tab/in-process) 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs" range="11-13"::: 

---

関数の名前の変更が済んだので、この関数をローカル コンピューターでテストできるようになりました。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

完全な Azure Functions ランタイムを使用してローカルで関数をテストできるように、Visual Studio は Azure Functions Core Tools と統合されます。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリが存在する必要があります。 初めてプロジェクトを発行するときに、Visual Studio の発行機能によって、関数アプリが自動的に作成されます。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Azure 内で関数を確認する

1. Cloud Explorer では、新しい関数アプリが選択されているはずです。 そうでない場合は、自分のサブスクリプション > **[App Services]** の順に展開して、新しい関数アプリを選択します。

1. 関数アプリを右クリックし、 **[ブラウザーで開く]** を選択します。 これにより、関数アプリのルートが既定の Web ブラウザーで開かれ、関数アプリが実行されていることを示すページが表示されます。 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="実行されている関数アプリ":::

1. ブラウザーのアドレス バーで、ベース URL に文字列 `/api/HttpExample?name=Functions` を追加し、要求を実行します。

    HTTP トリガー関数を呼び出す URL は、次の形式になります。

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. この URL に移動し、関数によって返されたリモート GET 要求に対する応答がブラウザーに表示されます。次の例のような内容です。

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="ブラウザーでの関数の応答":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 クイック スタート、チュートリアル、またはこのクイック スタートで作成したサービスのいずれかでの作業を引き続き行う場合は、リソースをクリーンアップしないでください。

Azure の "*リソース*" とは、関数アプリ、関数、ストレージ アカウントなどのことを指します。 これらは "*リソース グループ*" に分類されており、グループを削除することでグループ内のすべてのものを削除できます。 

これらのクイックスタートを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Visual Studio を使用して、シンプルな HTTP トリガー関数を含む C# 関数アプリを作成し、Azure に発行しました。 

次の記事に進み、Azure Storage キュー バインドを関数に追加する方法を学習してください。
> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs.md)

