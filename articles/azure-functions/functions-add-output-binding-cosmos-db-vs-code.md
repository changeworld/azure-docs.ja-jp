---
title: Visual Studio Code を使用して Azure Functions を Azure Cosmos DB に接続する
description: Visual Studio Code プロジェクトに出力バインドを追加して Azure Functions を Azure Cosmos DB アカウントに接続する方法を説明します。
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023250"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Visual Studio Code を使用して Azure Functions を Azure Cosmos DB に接続する

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

この記事では、Visual Studio Code を使用して、前のクイックスタート記事で作成した関数に [Azure Cosmos DB](../cosmos-db/introduction.md) を接続する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータを Azure Cosmos DB コンテナーに格納された JSON ドキュメントに書き込みます。 

::: zone pivot="programming-language-csharp"
開始する前に、「[クイックスタート: Visual Studio Code を使用して Azure に C# 関数を作成する](create-first-function-vs-code-csharp.md)」を済ませておく必要があります。 その記事の最後でリソースをクリーンアップした場合は、もう一度手順に従って Azure で関数アプリと関連リソースを再作成してください。
::: zone-end
::: zone pivot="programming-language-javascript"  
開始する前に、「[クイックスタート: Visual Studio Code を使用して Azure に JavaScript 関数を作成する](create-first-function-vs-code-node.md)」を済ませておく必要があります。 その記事の最後でリソースをクリーンアップした場合は、もう一度手順に従って Azure で関数アプリと関連リソースを再作成してください。  
::: zone-end

## <a name="configure-your-environment"></a>環境を構成する

作業を開始する前に、必ず Visual Studio Code 用の [Azure データベース拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)をインストールしてください。

## <a name="create-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを選択する

> [!IMPORTANT]
> [Azure Cosmos DB サーバーレス](../cosmos-db/serverless.md)は現在プレビューで利用できます。 この使用量ベースのモードにより、Azure Cosmos DB がサーバーレス ワークロードのための強力なオプションになります。 サーバーレス モードで Azure Cosmos DB を使用するには、アカウントの作成時に、 **[Capacity mode]\(容量モード\)** として **[サーバーレス]** を選択します。

1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. **[リソースの作成]** > **[データベース]** > **[Azure Cosmos DB]** の順にクリックします。
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Azure portal の [データベース] ウィンドウ" border="true":::

3. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    設定|値|説明
    ---|---|---
    サブスクリプション|*該当するサブスクリプション*|[前の記事](./create-first-function-vs-code-csharp.md)で関数アプリを作成した Azure サブスクリプションを選択します。
    リソース グループ|"*リソース グループ名*"|[前の記事](./create-first-function-vs-code-csharp.md)で関数アプリを作成したリソース グループを選択します。
    アカウント名|*一意の名前を入力*|自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。<br><br>アカウント名に使用できるのは、小文字、数字、ハイフン (-) のみで、長さは 3 文字から 31 文字の範囲にする必要があります。
    API|コア (SQL)|SQL 構文を使用してクエリを実行できるドキュメント データベースを作成するには、 **[コア (SQL)]** を選択します。 [Azure Cosmos DB SQL API の詳細については、こちらを参照してください](../cosmos-db/introduction.md)。|
    場所|"*ユーザーの場所に最も近いリージョンを選択*"|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできるよう、お客様またはお客様のユーザーに最も近い場所を使用します。
    容量モード|サーバーレスまたはプロビジョニング スループット|[サーバーレス](../cosmos-db/serverless.md) モードでアカウントを作成するには、 **[サーバーレス]** を選択します。 [プロビジョニング スループット](../cosmos-db/set-throughput.md) モードでアカウントを作成するには、 **[Provisioned throughput]\(プロビジョニング スループット\)** を選択します。<br><br>Azure Cosmos DB の使用を開始する場合は、 **[サーバーレス]** を選択します。

4. **[確認および作成]** をクリックします。 **[ネットワーク]** セクションと **[タグ]** セクションはスキップできます。 

5. 概要情報を確認し、**[作成]** をクリックします。 

6. 新しい Azure Cosmos DB が作成されるのを待ってから、 **[リソースに移動]** を選択します。

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Azure Cosmos DB アカウントの作成の完了" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Azure Cosmos DB データベースとコンテナーを作成する

Azure Cosmos DB アカウントから、 **[データ エクスプローラー]** 、 **[新しいコンテナー]** の順に選択します。 *my-database* という名前の新しいデータベース、*my-container* という名前の新しいコンテナーを作成し、[パーティション キー](../cosmos-db/partitioning-overview.md)として `/id` を選択します。

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Azure portal で新しい Azure Cosmos DB コンテナーを作成する" border="true":::

## <a name="update-your-function-app-settings"></a>関数アプリの設定を更新する

[前のクイック スタートの記事](./create-first-function-vs-code-csharp.md)では、Azure で関数アプリを作成しました。 この記事では、上記で作成した Azure Cosmos DB コンテナーに JSON ドキュメントを書き込むように関数アプリを更新します。 Azure Cosmos DB アカウントに接続するには、その接続文字列をアプリの設定に追加する必要があります。 その後に、新しい設定を local.settings.json ファイルにダウンロードして、ローカルで実行する際に Azure Cosmos DB アカウントに接続できるようにします。

1. Visual Studio Code で、先ほど作成した Azure Cosmos DB アカウントを見つけます。 その名前を右クリックし、 **[Copy Connection String]\(接続文字列をコピーする\)** を選択します。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Azure Cosmos DB 接続文字列のコピー" border="true":::

1. <kbd>F1</kbd> キーを押してコマンド パレットを開き、コマンド `Azure Functions: Add New Setting...` を検索して実行します。

1. 前の記事で作成した関数アプリを選択します。 プロンプトで、次の情報を入力します。

    + **新しいアプリ設定名を入力する**: `CosmosDbConnectionString` と入力します。

    + **"CosmosDbConnectionString" の値を入力する**: 先ほどコピーした Azure Cosmos DB アカウントの接続文字列を貼り付けます。

1. <kbd>F1</kbd> キーをもう一度押してコマンド パレットを開き、コマンド `Azure Functions: Download Remote Settings...` を検索して実行します。 

1. 前の記事で作成した関数アプリを選択します。 **[すべてはい]** を選択して既存のローカル設定を上書きします。 

## <a name="register-binding-extensions"></a>バインディング拡張機能を登録する

Azure Cosmos DB の出力バインドを使用しているため、このプロジェクトを実行する前に対応するバインド拡張機能をインストールしておく必要があります。 

::: zone pivot="programming-language-csharp"

HTTP トリガーとタイマー トリガーを除き、バインドは拡張機能パッケージとして実装されます。 ターミナル ウィンドウで次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージをプロジェクトに追加します。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

プロジェクトは、[拡張機能バンドル](functions-bindings-register.md#extension-bundles)を使用するように構成されています。これにより、事前定義された一連の拡張機能パッケージが自動的にインストールされます。 

拡張機能バンドルの使用は、プロジェクトのルートにある host.json ファイルで次のように有効になっています。

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

これで、Azure Cosmos DB の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 これらの属性を定義する方法は、関数アプリの言語によって異なります。

::: zone pivot="programming-language-csharp"

C# クラス ライブラリ プロジェクトでは、バインドは関数メソッドのバインド属性として定義されます。 その後、Functions に必要な *function.json* ファイルが、これらの属性に基づいて自動的に生成されます。

*HttpExample.cs* プロジェクト ファイルを開いて、`Run` メソッドの定義に次のパラメーターを追加します。

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut` パラメーターは IAsyncCollector<T> 型です。これは、関数の完了時に Azure Cosmos DB コンテナーに書き込まれる JSON ドキュメントのコレクションを表します。 それぞれの属性は、コンテナーの名前とその親データベースの名前を指定します。 Azure Cosmos DB アカウントの接続文字列は `ConnectionStringSettingAttribute` によって設定されます。

Run メソッドの定義は次のようになります。  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

バインドの属性は、function.json ファイルで直接定義されています。 バインドの種類によっては、追加のプロパティが必要になることもあります。 [Azure Cosmos DB の出力構成](./functions-bindings-cosmosdb-v2-output.md#configuration)では、Azure Cosmos DB 出力バインドに必要なフィールドについて説明します。 この拡張機能により、バインドを簡単に function.json ファイルに追加できます。 

バインドを作成するには、HttpTrigger フォルダー内の `function.json` ファイルを右クリック (macOS では Ctrl キーを押しながらクリック) して、 **[バインドの追加]** を選択します。プロンプトに従って、新しいバインドの次のバインド プロパティを定義します。

| Prompt | 値 | 説明 |
| -------- | ----- | ----------- |
| **Select binding direction (バインド方向を選択する)** | `out` | バインドは出力バインドです。 |
| **Select binding with direction "out" ("外" 方向のバインドを選択する)** | `Azure Cosmos DB` | バインドは Azure Cosmos DB バインドです。 |
| **コードでこのバインドの特定に使用する名前** | `outputDocument` | コードで参照されているバインド パラメーターを識別する名前。 |
| **データを書き込む Cosmos DB データベース** | `my-database` | ターゲット コンテナーを含む Azure Cosmos DB データベースの名前。 |
| **データが書き込まれるデータベース コレクション** | `my-container` | JSON ドキュメントが書き込まれる Azure Cosmos DB コンテナーの名前。 |
| **[If true, creates the Cosmos DB database and collection]\(オンの場合、Cosmos DB データベースとコレクションを作成する\)** | `false` | ターゲットのデータベースとコンテナーは既に存在します。 |
| **Select setting from "local.setting.json" ("local.setting.json" から設定を選択する)** | `CosmosDbConnectionString` | Azure Cosmos DB アカウントの接続文字列を含むアプリケーション設定の名前。 |
| **パーティション キー (省略可能)** | *空白のまま* | 出力バインドによってコンテナーが作成される場合にのみ必須です。 |
| **コレクションのスループット (省略可能)** | *空白のまま* | 出力バインドによってコンテナーが作成される場合にのみ必須です。 |

バインドは、function.json の `bindings` 配列に追加されます。このファイルは次のようになります。

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

::: zone pivot="programming-language-csharp"  

`documentsOut` 出力バインド オブジェクトを使用して JSON ドキュメントを作成するコードを追加します。 このコードは、メソッドから制御が戻る前に追加します。

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

この時点で、関数は次のようになります。

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

`context.bindings` で `outputDocument` 出力バインド オブジェクトを使用して JSON ドキュメントを作成するコードを追加します。 このコードを `context.res` ステートメントの前に追加します。

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

この時点で、関数は次のようになります。

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. 前の記事と同様、<kbd>F5</kbd> キーを押して関数アプリ プロジェクトと Core Tools を起動します。 

1. Core Tools を実行したまま、**Azure: Functions** 領域に移動します。 **[Functions]** の **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Mac では Ctrl キーを押しながらクリック) し、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code から今すぐ関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  
 
1. 応答が返されたら、<kbd>Ctrl + C</kbd> キーを押して Core Tools を停止します。

### <a name="verify-that-a-json-document-has-been-created"></a>JSON ドキュメントが作成されたことを確認する

1. Azure portal で Azure Cosmos DB アカウントに戻り、 **[データ エクスプローラー]** を選択します。

1. データベースとコンテナーを展開し、 **[項目]** を選択して、コンテナー内に作成されたドキュメントを一覧表示します。

1. 出力バインドによって新しい JSON ドキュメントが作成されたことを確認します。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Azure Cosmos DB コンテナーに新しいドキュメントが作成されたことを確認する" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>更新したアプリを再デプロイして検証する

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Deploy to function app...` を検索して選択します。

1. 最初の記事で作成した関数アプリを選択します。 同じアプリにプロジェクトを再デプロイしているため、 **[デプロイ]** を選択して、ファイルの上書きに関する警告を無視します。

1. デプロイの完了後、もう一度 **[Execute Function Now]\(今すぐ関数を実行\)** 機能を使用して Azure で関数をトリガーできます。

1. この場合も [Azure Cosmos DB コンテナーに作成されたドキュメントを確認](#verify-that-a-json-document-has-been-created)し、出力バインドによって再び新しい JSON ドキュメントが生成されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure では、"*リソース*" とは、関数アプリ、関数、ストレージ アカウントなどのことを指します。 これらは "*リソース グループ*" に分類されており、グループを削除することでグループ内のすべてのものを削除できます。

これらのクイックスタートを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 リソースの必要がなくなった場合にそれらを削除する方法を、次に示します。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>次のステップ

JSON ドキュメントが Azure Cosmos DB コンテナーに書き込まれるように、HTTP によってトリガーされる関数を更新しました。 この後は、Visual Studio Code を使用した Functions の開発について理解を深めましょう。

+ [Visual Studio Code を使用する Azure Functions の開発](functions-develop-vs-code.md)

+ [Azure Functions のトリガーとバインディング](functions-triggers-bindings.md)。
::: zone pivot="programming-language-csharp"  
+ [C# での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions の JavaScript 開発者向けガイド](functions-reference-node.md)  
::: zone-end  