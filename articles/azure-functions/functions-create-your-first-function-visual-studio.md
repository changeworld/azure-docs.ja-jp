---
title: 'クイック スタート: Visual Studio を使用して Azure で初めての C# 関数を作成する'
description: このクイックスタートでは、Visual Studio を使用して、.NET Core 3.1 で実行されて C# HTTP でトリガーされる関数を作成し、Azure Functions に発行する方法について説明します。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 11/03/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 60e40b675ac8bc32328ea68786329b372f91d056
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028213"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>クイック スタート: Visual Studio を使用して Azure で初めての C# 関数を作成する

Azure Functions を使用すると、Azure のサーバーレス環境で C# コードを実行できます。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Visual Studio を使用して、.NET 6.0 の C# クラス ライブラリ プロジェクトを作成します。
> * HTTP 要求に応答する関数を作成します。 
> * コードをローカル環境で実行して、関数の動作を確認します。
> * コード プロジェクトを Azure Functions にデプロイします。
 
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-csharp.md)も存在します。

## <a name="prerequisites"></a>前提条件

+ .NET 6.0 をサポートする [Visual Studio 2022](https://azure.microsoft.com/downloads/)。 インストール時に必ず **[Azure の開発]** ワークロードを選択してください。 

+ [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 アカウントをまだ持っていない場合は、始める前に[無料アカウントを作成](https://azure.microsoft.com/free/dotnet/)してください。

## <a name="create-a-function-app-project"></a>関数アプリ プロジェクトを作成する

Visual Studio の Azure Functions プロジェクト テンプレートを使用すると、Azure の関数アプリに発行できる C# クラス ライブラリ プロジェクトを作成できます。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

1. Visual Studio メニューで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** の検索ボックスに「*functions*」と入力し、**Azure Functions** テンプレートを選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトの **プロジェクト名** を入力し、 **[作成]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[新しい Azure Functions アプリケーションの作成]** 設定で、次の表の値を使用します。

    | 設定      | 値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET のバージョン** | **.NET 6** | この値により、Azure Functions ランタイムのバージョン 4.x でインプロセスを実行する関数プロジェクトが作成されます。 **[.NET 6 (isolated)]\(.NET 6 (分離)\)** を選択して、別のワーカー プロセスで実行されるプロジェクトを作成することもできます。 Azure Functions 1.x では、.NET Framework がサポートされます。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](./functions-versions.md)」をご覧ください。   |
    | **関数テンプレート** | **HTTP トリガー** | この値は、HTTP 要求によってトリガーされる関数を作成します。 |
    | **ストレージ アカウント (AzureWebJobsStorage)**  | **ストレージ エミュレーター** | Azure の関数アプリにはストレージ アカウントが必要であるため、プロジェクトを Azure に発行する際に割り当てられるか、作成されます。 HTTP トリガーによって、Azure Storage アカウントの接続文字列が使用されることはありません。その他のすべてのトリガーの種類には、有効な Azure Storage アカウントの接続文字列が必要です。 このオプションを選択すると、Azurite エミュレーターが使用されます。 |
    | **承認レベル** | **Anonymous** | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[承認キー](./functions-bindings-http-webhook-trigger.md#authorization-keys)」と [HTTP と Webhook のバインド](./functions-bindings-http-webhook.md)に関するページをご覧ください。 |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Azure Functions プロジェクトの設定":::

    **[承認レベル]** を **[匿名]** に設定していることを確認します。 **関数** の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](./functions-bindings-http-webhook-trigger.md#authorization-keys)を提示する必要があります。

1. **[作成]** を選択して、関数プロジェクトと HTTP トリガー関数を作成します。

Visual Studio によってプロジェクトとクラスが作成されます。クラスの中には、HTTP トリガー関数型のスケルトン コードが含まれています。 スケルトン コードは、要求本文またはクエリ文字列の値を含む HTTP 応答を送信します。 `HttpTrigger`属性は、関数が HTTP 要求によってトリガーされることを指定します。

## <a name="rename-the-function"></a>フォルダーの名前を変更する

`FunctionName` メソッド属性は、関数の名前を設定します。これは、既定では `Function1` として生成されます。 このツールでは、プロジェクトを作成するときに既定の関数名をオーバーライドすることはできないため、ここで関数クラス、ファイル、およびメタデータに対してより適切な名前を指定します。

1. **エクスプローラー** で Function1.cs ファイルを右クリックし、`HttpExample.cs` という名前に変更します。

1. コードで、Function1 クラスの名前を `HttpExample` に変更します。

1. `Run` という名前の `HttpTrigger` メソッドで、`FunctionName` メソッド属性の名前を `HttpExample` に変更します。 

関数の定義は次のコードのようになります。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

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

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="実行されている関数アプリ":::

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

