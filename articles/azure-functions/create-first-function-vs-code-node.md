---
title: Visual Studio Code を使用して JavaScript 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して JavaScript 関数を作成し、ローカル Node.js プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 07/01/2021
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-node_uiex
ms.openlocfilehash: 2ceaa4173de4c4e997f2942308a2af733ea7fcfb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667789"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Azure に JavaScript 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Visual Studio Code を使用して、HTTP 要求に応答する JavaScript 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [CLI ベースのバージョン](create-first-function-cli-node.md)もあります。

## <a name="configure-your-environment"></a>環境を構成する

作業を開始する前に、次の要件が満たされていることを確認します。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Node.js 10.14.1 以降](https://nodejs.org/)。 `node --version` コマンドを使用して、現在のバージョンを確認してください。  

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを JavaScript で作成します。 後からこの記事の中で、関数コードを Azure に発行します。

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    |Prompt|[選択]|
    |--|--|
    |**Select a language for your function project (関数プロジェクトの言語を選択してください)**|`JavaScript` を選択します。|
    |**Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)**|`HTTP trigger` を選択します。|
    |**Provide a function name (関数名を指定してください)**|「`HttpExample`.|
    |**承認レベル**|`Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。|
    |**Select how you would like to open your project (プロジェクトを開く方法を選択してください)**|`Add to workspace` を選択します。|

    Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、Visual Studio Code を使用してプロジェクトを直接 Azure に発行します。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

<a name="Publish the project to Azure"></a>

## <a name="deploy-the-project-to-azure"></a>Azure にプロジェクトをデプロイする

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。 

> [!IMPORTANT]
> 既存の関数アプリにデプロイすると、Azure のそのアプリのコンテンツが上書きされます。 


1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    |Prompt| [選択]|
    |--|--|
    |**Select function app in Azure (Azure で関数アプリを選択してください)**|`+ Create new Function App` を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)|
    |**Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)**|URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。|
    |**Select a runtime (ランタイムを選択してください)**|ローカルで実行している Node.js のバージョンを選択します。 `node --version` コマンドを使用してバージョンを確認してください。|
    |**Select a location for new resources (新しいリソースの場所を選択してください)**|パフォーマンスを向上させるために、お近くの[リージョン](https://azure.microsoft.com/regions/)を選択してください。|

    この拡張機能は、Azure に作成されている個々のリソースの状態を通知領域に表示します。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure リソース作成の通知":::

    完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

1. 関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

1. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

## <a name="change-the-code-and-redeploy-to-azure"></a>コードを変更して Azure に再デプロイする

1. VSCode エクスプローラー ビューで、`./HttpExample/index.js` ファイルを選択します。 
1. このファイルを次のコードに置き換え、JSON オブジェクトを作成して返します。

    ```javascript
    module.exports = async function (context, req) {
        
        try {
            context.log('JavaScript HTTP trigger function processed a request.');
    
            // Read incoming data
            const name = (req.query.name || (req.body && req.body.name));
            const sport = (req.query.sport || (req.body && req.body.sport));
        
            // fail if incoming data is required
            if (!name || !sport) {
    
                context.res = {
                    status: 400
                };
                return;
            }
            
            // Add or change code here
            const message = `${name} likes ${sport}`;
        
            // Construct response
            const responseJSON = {
                "name": name,
                "sport": sport,
                "message": message,
                "success": true
            }
    
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: responseJSON,
                contentType: 'application/json'
            };
        } catch(err) {
            context.res = {
                status: 500
            };
        }
    }
    ```
1. [関数アプリをローカルで実行](#run-the-function-locally)します。
1. プロンプト **[Enter request body]\(要求本文を入力してください\)** で、要求メッセージ本文を { "name": "Tom","sport":"basketball" } に変更します。 Enter キーを押して、この要求メッセージを関数に送信します。
1. 通知の応答を表示します。

    ```json
    {
      "name": "Tom",
      "sport": "basketball",
      "message": "Tom likes basketball",
      "success": true
    }
    ```

1. [関数を Azure に再デプロイ](#deploy-the-project-to-azure)します。

## <a name="troubleshooting"></a>トラブルシューティング

次の表を利用して、このクイックスタートの使用時に発生する最も一般的な問題を解決してください。

|問題|解決策|
|--|--|
|ローカル関数プロジェクトを作成できない場合|[Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)がインストールされていることを確認してください。|
|関数をローカルで実行できない場合|[Azure Functions Core Tools](functions-run-local.md?tabs=windows%2Ccsharp%2Cbash) がインストールされていることを確認してください。 <br/>Windows で実行している場合、Visual Studio Code の既定のターミナル シェルが WSL Bash に設定されていないことを確認してください。|
|関数を Azure にデプロイできない場合|出力でエラー情報を確認します。 出力は、右下隅にあるベル アイコンでも確認できます。 既存の関数アプリに発行していないか確認します。 この操作を実行すると、Azure にあるそのアプリの内容が上書きされます。|
|クラウドベースの関数アプリを実行できなかった場合|必ず、パラメーターで送信するクエリ文字列を使用してください。|

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code-extension.md)]

## <a name="next-steps"></a>次のステップ

[Visual Studio Code](functions-develop-vs-code.md?tabs=javascript) を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、Azure Cosmos DB または Azure Storage に接続することによってその関数を拡張します。 他の Azure サービスへの接続について詳しくは、「[Azure Functions の既存の関数にバインドを追加する](add-bindings-existing-function.md?tabs=javascript)」を参照してください。 セキュリティの詳細については、「[Azure Functions のセキュリティ保護](security-concepts.md)」を参照してください。

> [!div class="nextstepaction"]
> [Azure Cosmos DB に接続する](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-javascript)
> [Azure Queue Storage に接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
