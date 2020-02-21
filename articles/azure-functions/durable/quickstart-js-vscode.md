---
title: JavaScript を使用して Azure で最初の Durable Functions を作成する
description: Visual Studio Code を使用して Azure Durable Functions を作成して発行します。
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 431bd45763cbe24e44d47342b32c5c452a27b0f6
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210295"
---
# <a name="create-your-first-durable-function-in-javascript"></a>JavaScript で最初の Durable Functions を作成する

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

この記事では、Visual Studio Code Azure Functions 拡張機能を使用して、"hello world" Durable Functions をローカルで作成してテストする方法について説明します。  この関数は、他の関数の呼び出しを調整し、連結します。 その後、関数コードを Azure に発行します。

![Azure で Durable Functions を実行する](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/download) をインストールします。

* 最新バージョンの [Azure Functions Core Tools](../functions-run-local.md) があることを確認します。

* Windows コンピューターでは、[Azure Storage Emulator](../../storage/common/storage-use-emulator.md) がインストールされ、実行されていることを確認します。 Mac または Linux コンピューターでは、実際の Azure ストレージ アカウントを使用する必要があります。

* バージョン 8.0 以降の [Node.js](https://nodejs.org/) がインストールされていることを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する 

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを作成します。 

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Create new project...` を検索して選択します。

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. ご希望の言語に関して、プロンプトに従って次の情報を入力します。

    | Prompt | Value | 説明 |
    | ------ | ----- | ----------- |
    | Select a language for your function app project (関数アプリ プロジェクトの言語を選択してください) | JavaScript | ローカル Node.js 関数プロジェクトを作成します。 |
    | Select a version (バージョンを選択してください) | Azure Functions v2 | このオプションが表示されるのは、Core Tools がまだインストールされていない場合だけです。 その場合、アプリの初回実行時に Core Tools がインストールされます。 |
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 新しい関数アプリで HTTP トリガー関数を作成します。 |
    | Provide a function name (関数名を指定してください) | HttpTrigger | Enter キーを押して既定の名前を使用します。 |
    | 承認レベル | Function | `function` 承認レベルでは、関数の HTTP エンドポイントを呼び出す際にアクセス キーの指定が必須となります。 これには、セキュリティで保護されていないエンドポイントへのアクセスを難しくする効果があります。 詳細については、「[承認キー](../functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。  |
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) | 現在のワークスペースに関数アプリを作成します。 |

Azure Functions Core Tools は、必要に応じて Visual Studio Code によりインストールされます。 また、関数アプリ プロジェクトが新しいワークスペースに作成されます。 このプロジェクトには、[host.json](../functions-host-json.md) および [local.settings.json](../functions-run-local.md#local-settings-file) 構成ファイルが含まれています。 さらに、[function.json 定義ファイル](../functions-reference-node.md#folder-structure)と [index.js ファイル](../functions-reference-node.md#exporting-a-function) (関数コードを含む Node.js ファイル) の格納先となる HttpExample フォルダーも作成されます。

また、ルート フォルダーには、package.json ファイルが作成されます。

## <a name="install-the-durable-functions-npm-package"></a>Durable Functions npm パッケージをインストールする

1. 関数アプリのルート ディレクトリで `npm install durable-functions` を実行して、`durable-functions` npm パッケージをインストールします。

## <a name="creating-your-functions"></a>関数を作成する

ここでは、Durable Functions を使い始めるために必要な 3 つの関数、つまり HTTP スターター、オーケストレーター、およびアクティビティ関数を作成します。 HTTP スターターによって対象のソリューション全体が開始され、オーケストレーターによってさまざまなアクティビティ関数に作業がディスパッチされます。

### <a name="http-starter"></a>HTTP スターター

まず、Durable Functions のオーケストレーションを開始する HTTP トリガー関数を作成します。

1. *[Azure:Functions]* で **[関数の作成]** アイコンを選択します。

    ![関数を作成する](./media/quickstart-js-vscode/create-function.png)

2. 自分の関数アプリ プロジェクトが含まれたフォルダーを選択し、 **[Durable Functions HTTP スターター]** 関数テンプレートを選択します。

    ![HTTP スターター テンプレートを選択する](./media/quickstart-js-vscode/create-function-choose-template.png)

3. 名前を既定値の `DurableFunctionsHttpStart` のままにして ** **Enter** キーを押し、 **[匿名]** 認証を選択します。

    ![匿名認証を選択する](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

これで Durable Functions へのエントリ ポイントが作成されます。 オーケストレーターを追加してみましょう。

### <a name="orchestrator"></a>オーケストレーター

ここでは、アクティビティ関数を調整するオーケストレーターを作成します。

1. *[Azure:Functions]* で **[関数の作成]** アイコンを選択します。

    ![関数を作成する](./media/quickstart-js-vscode/create-function.png)

2. 自分の関数アプリ プロジェクトが含まれたフォルダーを選択し、 **[Durable Functions オーケストレーター]** 関数テンプレートを選択します。 名前は既定値の "DurableFunctionsOrchestrator" のままにします

    ![オーケストレーター テンプレートを選択する](./media/quickstart-js-vscode/create-function-choose-template.png)

ここでは、アクティビティ関数を調整するオーケストレーターを追加しました。 次は参照アクティビティ関数を追加してみましょう。

### <a name="activity"></a>アクティビティ

ここで、実際にソリューションの作業を実行するアクティビティ関数を作成します。

1. *[Azure:Functions]* で **[関数の作成]** アイコンを選択します。

    ![関数を作成する](./media/quickstart-js-vscode/create-function.png)

2. 自分の関数アプリ プロジェクトが含まれたフォルダーを選択し、 **[Durable Functions のアクティビティ]** 関数テンプレートを選択します。 名前は既定値の "Hello" のままにします。

    ![アクティビティ テンプレートを選択する](./media/quickstart-js-vscode/create-function-choose-template.png)

これで、オーケストレーションを開始し、アクティビティ関数を連結するために必要なすべてのコンポーネントが追加されます。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。 Visual Studio Code から初めて関数を起動すると、これらのツールをインストールするよう求めるメッセージが表示されます。

1. Windows コンピューターで、Azure Storage Emulator を起動し、*local.settings.json* の **AzureWebJobsStorage** プロパティが `UseDevelopmentStorage=true` に設定されていることを確認します。

    Storage Emulator 5.8 では、local.settings.json の **AzureWebJobsSecretStorageType** プロパティが `files` に設定されていることを確認します。 Mac または Linux コンピューターでは、既存の Azure ストレージ アカウントの接続文字列に **AzureWebJobsStorage** プロパティを設定する必要があります。 ストレージ アカウントの作成については、この記事で後述します。

2. 関数をテストするには、関数コードにブレークポイントを設定し、F5 キーを押して関数アプリ プロジェクトを開始します。 Core Tools からの出力が**ターミナル** パネルに表示されます。 Durable Functions を初めて使用する場合は、Durable Functions 拡張機能がインストールされるので、ビルドに数秒かかる場合があります。

    > [!NOTE]
    > JavaScript Durable Functions では、バージョン **1.7.0** 以降の **Microsoft.Azure.WebJobs.Extensions.DurableTask** 拡張機能が必要です。 Azure Functions アプリのルート フォルダーから次のコマンドを実行して、Durable Functions 拡張機能 `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0` をインストールします

3. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。

    ![Azure のローカル出力](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. `{functionName}` を `DurableFunctionsOrchestrator` で置き換え

5. [Postman](https://www.getpostman.com/) または [cURL](https://curl.haxx.se/) のようなツールを使用して、HTTP POST 要求を URL エンドポイントに送信します。

   応答は、永続的なオーケストレーションが正常に開始されたことを示す HTTP 関数の最初の結果です。 これはまだオーケストレーションの最終的な結果ではありません。 応答には、いくつかの便利な URL が含まれています。 ここでは、オーケストレーションの状態を照会してみましょう。

6. `statusQueryGetUri` の URL 値をコピーし、ブラウザーのアドレス バーに貼り付け、要求を実行します。 また、引き続き Postman を使用して GET 要求を発行することもできます。

   この要求によって、オーケストレーション インスタンスの状態が照会されます。 インスタンスが完了したことを示し、持続的な関数の出力または結果を含む、最終的な応答を受け取ります。 次のように表示されます。 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. デバッグを停止するには、VS Code で **Shift キーを押しながら F5 キー**を押します。

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. **出力**パネルから HTTP トリガーの URL をコピーします。 HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 以前の発行済みアプリの使用時と同じ状態応答を受け取るはずです。

## <a name="next-steps"></a>次のステップ

Visual Studio Code を使用して、JavaScript の Durable Functions アプリを作成して発行しました。

> [!div class="nextstepaction"]
> [Durable Functions の一般的なパターンについて学習する](durable-functions-overview.md#application-patterns)