---
title: Python 関数に Azure Storage キュー バインドを追加する
description: 出力バインディングを使用して Azure Storage キューを Python 関数に統合します。
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485167"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Python 関数に Azure Storage キュー バインドを追加する

この記事では、[HTTP によってトリガーされる Python 関数の作成](functions-create-first-function-python.md)に関するページで作成したストレージ アカウントと関数に Azure Storage キューを統合します。 この統合は、HTTP 要求からキュー内のメッセージにデータを書き込む "*出力バインディング*" を使用して実現します。 この記事を完了しても、前のクイックスタートの数セントを超えるコストが追加で発生することはありません。

## <a name="prerequisites"></a>前提条件

- [HTTP によってトリガーされる Python 関数の作成](functions-create-first-function-python.md)に関するクイックスタートを完了します。 その記事の最後でリソースをクリーンアップした場合は、もう一度手順に従って Azure で関数アプリを再作成し、リソースを維持してください。

## <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage の接続文字列を取得する

前のクイックスタートで Azure に関数アプリを作成したときに、ストレージ アカウントも作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 関数をローカルで実行するときは、*local.settings.json* ファイルに設定をダウンロードすることにより、その接続を使用して同じアカウントのストレージ キューへの書き込みを行うことができます。 

1. プロジェクトのルートから、次のコマンドを実行します。`<app_name>` は、前のクイックスタートの関数アプリ名に置き換えてください。 このコマンドを実行すると、ファイル内の既存の値はすべて上書きされます。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.json* を開き、`AzureWebJobsStorage` という名前の値を検索します。それがストレージ アカウントの接続文字列です。 `AzureWebJobsStorage` という名前と接続文字列は、この記事の他のセクションで使用します。

> [!IMPORTANT]
> *local.settings.json* には、Azure からダウンロードされたシークレットが含まれているため、このファイルは必ずソース管理から除外してください。 ローカル関数プロジェクトで作成される *.gitignore* ファイルからは、このファイルが既定で除外されます。

## <a name="add-an-output-binding-to-functionjson"></a>function.json に出力バインディングを追加する

関数に割り当てることができるトリガーは 1 つだけですが、入力と出力のバインディングは複数割り当てることができます。これらを使用すると、カスタム統合コードを記述しなくても、他の Azure サービスやリソースに接続できます。 関数に使用している言語に応じて、自分の関数フォルダーの *function.json* ファイルでそれらのバインディングを宣言します。

前のクイックスタートの *HttpExample* フォルダーにある *function.json* ファイルでは、`bindings` コレクション内に 2 つのバインディングが含まれています。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

それぞれのバインディングには、少なくとも型、方向、名前があります。 上の例の 1 つ目のバインディングは、型が `httpTrigger` で、方向が `in` になっています。 `in` 方向の場合、トリガーによって呼び出された関数に送信される入力パラメーターの名前が `name` で指定されます。

2 つ目のバインディングは、型が `http` で方向が `out` です。この場合、このバインディングは入力パラメーターを渡すものではなく、関数の戻り値を使用するものであることが、`$return` という特殊な `name` からわかります。

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

このケースでは、`msg` が出力引数として関数に与えられています。 型 `queue` では、`queueName` にキューの名前を指定し、(*local.settings.json* から得られる) Azure Storage 接続の "*名前*" を `connection` に指定する必要もあります。

バインディングの詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」と[キューの出力の構成](functions-bindings-storage-queue-output.md#configuration)に関する記事を参照してください。

## <a name="add-code-to-use-the-output-binding"></a>出力バインディングを使用するコードを追加する

キュー バインディングを *function.json* に指定したら、`msg` 出力パラメーターを受け取ってキューにメッセージを書き込むよう関数を更新することができます。

次のコードに合わせて *HttpExample\\\_\_init\_\_.py* を更新します。関数の定義に `msg` パラメーターを、`if name:` ステートメントの下に `msg.set(name)` を追加してください。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

`msg` パラメーターは、[`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) のインスタンスです。 その `set` メソッドでは、文字列メッセージ (この場合、URL のクエリ文字列として関数に渡された名前) をキューに書き込みます。

認証、キューの参照の取得、データの書き込みのためのコードを記述する必要が "*ない*" 点に注目してください。 これらの統合タスクはすべて、Azure Functions ランタイムとキュー出力バインディングで処理され、手間がかかりません。

## <a name="run-and-test-the-function-locally"></a>ローカルで関数を実行してテストする

1. ターミナルまたはコマンド プロンプトで、自分の関数のプロジェクト フォルダー *LocalFunctionProj* に移動します。

1. 次のコマンドを使用して Azure Functions ランタイム ホストを起動します。

    ```
    func host start
    ```

1. 起動が完了して `HttpExample` エンドポイントの URL が表示されたら、その URL をブラウザーにコピーし、クエリ文字列 `?name=<your-name>` を追加します。URL 全体は `http://localhost:7071/api/HttpExample?name=Guido` のようになります。 前の記事と同様、`Hello Guido` のようなメッセージがブラウザーに表示されます。

    `HttpExample` エンドポイントが表示されない場合は、**Ctrl** + **C** キーでホストを停止し、出力にエラーがないかを確認してください。 たとえば、*function.json* にエラーがある場合、ホストはエンドポイントをアクティブにしません。 また、*HttpExample* フォルダーからではなく、関数のプロジェクト フォルダーから `func host start` を実行していることも確認してください。

1. 完了したら、**Ctrl** + **C** キーでホストを停止します。

> [!TIP]
> 起動中、[Storage バインディング拡張機能](functions-bindings-storage-blob.md#add-to-your-functions-app)など、Microsoft のバインディング拡張機能がホストによってダウンロードされてインストールされます。 このインストールが実行される理由は、*host.json* ファイルでバインディング拡張機能が次のプロパティによって既定で有効になっているためです。
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> バインディング拡張機能に関連したエラーが発生した場合は、*host.json* に上記のプロパティが存在することを確認してください。

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage キューのメッセージを確認する

この関数は、Web ブラウザーへの HTTP 応答を生成するときに、`msg.set(name)` も呼び出します。これによって、キューのバインディングで指定した `outqueue` という名前の Azure Storage キューにメッセージが書き込まれます。 キューは、[Azure portal](../storage/queues/storage-quickstart-queues-portal.md) または [Microsoft Azure Storage Explorer](https://storageexplorer.com/) で確認できます。 次の手順に従って、Azure CLI でキューを確認することもできます。

1. 関数プロジェクトの *local.setting.json* ファイルを開き、接続文字列の値をコピーします。 ターミナルまたはコマンド ウィンドウで、次のコマンドを実行して、`AZURE_STORAGE_CONNECTION_STRING` という名前の環境変数を作成し、`<connection_string>` の代わりに実際の接続文字列を貼り付けます。 (この環境変数を作成すれば、`--connection-string` 引数を使用して接続文字列を後続の各コマンドに指定する必要はありません。)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (省略可) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) コマンドを使用して、ご利用のアカウント内のストレージ キューを表示します。 このコマンドからの出力には、`outqueue` という名前のキューが含まれています。これはこのキューに対する最初のメッセージを関数が書き込んだときに作成されたものです。
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) コマンドを使用して、このキュー内のメッセージ (先ほど関数をテストするときに使用した名) を表示します。 このコマンドでは、キューの 1 つ目のメッセージを [base64 エンコーディング](functions-bindings-storage-queue-trigger.md#encoding)で取得します。そのため、テキストとして表示するためには、メッセージをデコードする必要もあります。

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    メッセージ コレクションを逆参照して、base64 からデコードする必要があるため、PowerShell を実行し、PowerShell コマンドを使用してください。

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Azure にプロジェクトを再デプロイする

関数をローカルでテストし、Azure Storage キューにメッセージが書き込まれたことを確認したので、自分のプロジェクトを再デプロイして、Azure 上で実行するようにエンドポイントを更新することができます。

1. *LocalFunctionsProj* フォルダーで [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) コマンドを使用してプロジェクトを再デプロイし、`<app_name>` を自分のアプリの名前に置き換えます。

    ```
    func azure functionapp publish <app_name>
    ```
    
1. 前のクイックスタートと同様、ブラウザーまたは CURL を使用して、再デプロイした関数をテストします。

    # <a name="browser"></a>[ブラウザー](#tab/browser)
    
    publish コマンドの出力に表示されている完全な **Invoke url** にクエリ パラメーター `&name=Azure` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

    ![Azure 上で実行された関数の出力をブラウザーで表示したところ](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    **Invoke url** にパラメーター `&name=Azure` を追加して [curl](https://curl.haxx.se/) を実行します。 "Hello Azure" というテキストがコマンドの出力として表示されます。
    
    ![Azure 上で実行された関数の出力を curl を使用して表示したところ](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. 前セクションの説明に従って、もう一度ストレージ キューを確認します。キューに新しいメッセージが書き込まれていることを確かめてください。

    Azure CLI を使用してキューを確認する場合、`az storage message peek` コマンドで表示されるのは、キュー内の最初のメッセージだけです。 メッセージの処理をシミュレートするには、まったく同じ引数で `az storage message get` を代わりに使用します。 `get` コマンドを実行すると、メッセージが返され、キューからそれが削除されます。 その後、キューが空になるまで (コマンドからエラーが返されるまで) 同じコマンドを繰り返すことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順「[Application Insights との統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)」に進む場合は、既存の作業をベースにするので、リソースをすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Application Insights と Azure Functions アプリの統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)

その他のリソース:

- [Python での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=python)。
- [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)
- [Azure Functions のトリガーとバインディング](functions-triggers-bindings.md)。
- [Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)
- [従量課金プランのコストの見積もり](functions-consumption-costs.md)に関する記事。
