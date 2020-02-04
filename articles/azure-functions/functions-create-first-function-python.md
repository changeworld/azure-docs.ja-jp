---
title: Azure で HTTP によってトリガーされる Python 関数を作成する
description: Azure Functions を使用してサーバーレスの Python コードを作成し、クラウドにデプロイします。
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 452c5aeab5d2a1092cb7d338d37e26a82d92396e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845495"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>クイック スタート:Azure で HTTP によってトリガーされる Python 関数を作成する

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する Python 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。 このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](/azure/python/tutorial-vs-code-serverless-python-01)も存在します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.7.1846 以降。
- [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.0.76 以降。 
- [Python 3.7.4 - 64 ビット](https://www.python.org/downloads/release/python-374/) (Python 3.7.4 は Azure Functions で検証されています。Python 3.8 以降のバージョンは、まだサポートされていません)。

### <a name="prerequisite-check"></a>前提条件のチェック

1. ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 2.7.1846 以降であることを確認します。
1. `az --version` を実行して、Azure CLI バージョンが 2.0.76 以降であることを確認します。
1. `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。
1. `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、Python のバージョンが 3.7. x であることを確認します。

## <a name="create-and-activate-a-virtual-environment"></a>仮想環境を作成してアクティブにする

適切なフォルダーで次のコマンドを実行し、`.venv` という名前の仮想環境を作成してアクティブにします。 必ず、Azure Functions でサポートされている Python 3.7 を使用してください。


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

お使いの Linux ディストリビューションに Python の venv パッケージがインストールされていなかった場合は、次のコマンドを実行します。

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

以降のコマンドはすべて、このアクティブ化された仮想環境で実行します (仮想環境を終了するには、`deactivate` を実行します)。

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 仮想環境で、`func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。

    ```
    func init LocalFunctionProj --python
    ```
    
    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

    > [!TIP]
    > 関数プロジェクトは特定のランタイムに関連付けられているので、プロジェクト内の関数はすべて同じ言語で記述する必要があります。

1. プロジェクト フォルダーに移動します。

    ```
    cd LocalFunctionProj
    ```
    
1. 次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* には、*function.json* 内の構成に従ってトリガーされる Python 関数 `main()` が含まれます。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

HTTP トリガーの場合、この関数は、*function.json* に定義された変数 `req` で要求データを受け取ります。 `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 *function.json* に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](functions-bindings-http-webhook.md)」を参照してください。

### <a name="functionjson"></a>function.json

*function.json* は、関数の入出力 `bindings` (トリガーの型を含む) を定義する構成ファイルです。 `scriptFile` を変更することで、必要に応じて異なる Python ファイルを呼び出せます。

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

各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook.md#trigger) 型の入力バインディングと、[`http`](functions-bindings-http-webhook.md#output) 型の出力バインディングが与えられます。


## <a name="run-the-function-locally"></a>関数をローカルで実行する

*LocalFunctionProj* フォルダーで、ローカルの Azure Functions ランタイム ホストを起動して、関数を開始します。

```
func start
```

次の出力が表示されるはずです。 (HttpExample が以下のように表示されない場合、*HttpExample* フォルダー内からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、親 *LocalFunctionProj* フォルダーに移動してから、再度 `func start` を実行してください。)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<your-name>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

![ブラウザーでローカルに関数を実行した結果](./media/functions-create-first-function-python/function-test-local-browser.png)

要求を送信すると、`func start` を実行したターミナルにもログ出力が表示されます。

準備が完了したら、**Ctrl** + **C** キーを押して、関数ホストを停止してください。

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイするには、3 つのリソースを作成する必要があります。

- リソース グループ。関連リソースの論理コンテナーです。
- Azure ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
- Azure 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

Azure CLI コマンドを使用して、これらの項目を作成しましょう。 それぞれのコマンドからは、完了時に JSON 出力が返されます。

1. まだ Azure にサインインしていない場合は、[az login](/cli/azure/reference-index#az-login) コマンドでサインインします。

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、`AzureFunctionsQuickstart-rg` という名前のリソース グループを `westeurope` リージョンに作成します。 (リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsQuickstart-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。
    
1. [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して、リソース グループとリージョン内に汎用ストレージ アカウントを作成します。 次の例の `<storage_name>` は適宜、グローバルに一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` では、標準的な汎用アカウントを指定します。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    このクイックスタートでは、ストレージ アカウントに関して数セントの料金が発生します。
    
1. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して関数アプリを作成します。 次の例では、`<storage_name>` を前の手順で使用したアカウントの名前に、`<app_name>` を適宜グローバルに一意の名前に置き換えてください。 `<app_name>` は、関数アプリの既定の DNS ドメインでもあります。

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    このコマンドでは、[Azure Functions 従量課金プラン](functions-scale.md#consumption-plan) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムを実行して関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。
    
## <a name="deploy-the-function-project-to-azure"></a>Azure に関数プロジェクトをデプロイする

必要なリソースが揃ったら、[func azure functionapp publish](functions-run-local.md#project-file-deployment) コマンドを使用して、ローカル関数プロジェクトを Azure の関数アプリにデプロイすることができます。 次の例の `<app_name>` は、実際のアプリ名に置き換えてください。

```
func azure functionapp publish <app_name>
```

"... という名前のアプリが見つからない" という内容のエラーが表示された場合は、数秒待ってからやり直してください。先ほどの `az functionapp create` コマンドの後、Azure でアプリが完全には初期化されていない可能性があります。

publish コマンドを実行すると、次のような出力結果が表示されます (簡潔にするため一部省略しています)。

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しているため、呼び出しは、その URL にブラウザーから HTTP 要求を送信するか、または curl などのツールを使用して行います。 どちらの場合も、URL パラメーターである `code` が、関数エンドポイントでの呼び出しを承認する一意の関数キーです。

# <a name="browsertabbrowser"></a>[ブラウザー](#tab/browser)

publish コマンドの出力に表示されている完全な **Invoke url** にクエリ パラメーター `&name=Azure` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

![Azure 上で実行された関数の出力をブラウザーで表示したところ](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

**Invoke url** にパラメーター `&name=Azure` を追加して [curl](https://curl.haxx.se/) を実行します。 "Hello Azure" というテキストがコマンドの出力として表示されます。

![Azure 上で実行された関数の出力を curl を使用して表示したところ](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> 公開された Python アプリのほぼリアルタイムのログを表示するには、[Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs) を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順である [Azure Storage キュー出力バインディングの追加](functions-add-output-binding-storage-queue-python.md)に進む場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キュー出力バインディングの追加](functions-add-output-binding-storage-queue-python.md)
