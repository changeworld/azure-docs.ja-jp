---
title: HTTP 要求に応答する関数を Azure で作成する
description: コマンド ラインから関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 01/28/2020
ms.topic: quickstart
ms.openlocfilehash: f2ec642a477348923e8f587879d4804c07fff5a0
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096259"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>クイック スタート:HTTP 要求に応答する関数を Azure で作成する

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。 このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](functions-create-first-function-vs-code.md)も存在します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.7.1846 以降の 2.x バージョン。
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 および 3.7 では、[Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.7.1846 以降の 2.x バージョンが必要です。 Python 3.8 では、Core Tools の[バージョン 3.x](./functions-run-local.md#v2) が必要です。
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.0.76 以降。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/)、[Python 3.7](https://www.python.org/downloads/release/python-375/)、[Python 3.6](https://www.python.org/downloads/release/python-368/) が Azure Functions でサポートされます。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 以上](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>使用環境を確認する

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 2.7.1846 以降の 2.x バージョンであることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.0.76 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ `node --version` を実行して、使用している Node.js のバージョンが 8.x または 10.x であることを確認します。
::: zone-end
::: zone pivot="programming-language-python"
+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。

## <a name="create-venv"></a>仮想環境を作成してアクティブにする

適切なフォルダーで次のコマンドを実行し、`.venv` という名前の仮想環境を作成してアクティブにします。 必ず、Azure Functions でサポートされている Python 3.8、3.7、または 3.6 を使用してください。


# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

以降のコマンドはすべて、このアクティブ化された仮想環境で実行します (仮想環境を終了するには、`deactivate` を実行します)。

::: zone-end

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 仮想環境で、`func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

1. プロジェクト フォルダーに移動します。

    ```
    cd LocalFunctionProj
    ```
    
1. 次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` によって、HttpExample.cs コード ファイルが作成されます。
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* には、`Run` メソッドが含まれています。これは、トリガー動作を定義する **HttpTriggerAttribute** で装飾された [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 内の `req` 変数で要求データを受け取ります。 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

返されるオブジェクトは、[OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) または [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) として応答メッセージを返す [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) です。 詳細については、「[Azure Functions の HTTP トリガーとバインド](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp)」を参照してください。
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* には、*function.json* 内の構成に従ってトリガーされる Python 関数 `main()` が含まれます。

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP トリガーの場合、この関数は、*function.json* に定義された変数 `req` で要求データを受け取ります。 `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 *function.json* に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](/azure/azure-functions/functions-bindings-http-webhook?tabs=python)」を参照してください。
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* は、*function.json* の構成に従ってトリガーされる関数をエクスポートします。

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

HTTP トリガーの場合、この関数は、*function.json* に定義された変数 `req` で要求データを受け取ります。 *function.json* で `$return` として定義されている返されるオブジェクトが応答です。 詳細については、「[Azure Functions の HTTP トリガーとバインド](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript)」を参照してください。
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* は、*function.json* の構成に従ってトリガーされる関数をエクスポートします。

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP トリガーの場合、この関数は、*function.json* に定義されている **HttpRequest** 型の変数 `req` で要求データを受け取ります。 *function.json* で `$return` として定義されている返されるオブジェクトが応答です。 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* は、*function.json* の構成に従ってトリガーされる関数スクリプトを定義します。

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP トリガーの場合、この関数は、*function.json* に定義された `$Request` パラメーターに渡された要求データを受け取ります。 *function.json* で `Response` として定義されている返されるオブジェクトが、応答として `Push-OutputBinding` コマンドレットに渡されます。 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* は、関数の入出力 `bindings` (トリガーの型を含む) を定義する構成ファイルです。 
::: zone-end

::: zone pivot="programming-language-python"
`scriptFile` を変更することで、必要に応じて異なる Python ファイルを呼び出せます。

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook-trigger.md) 型の入力バインディングと、[`http`](functions-bindings-http-webhook-output.md) 型の出力バインディングが与えられます。
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- リソース グループ。関連リソースの論理コンテナーです。
- ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

これらの項目を作成するには、次の Azure CLI コマンドを使用します。 それぞれのコマンドからは、完了時に JSON 出力が返されます。

1. まだ Azure にサインインしていない場合は、[az login](/cli/azure/reference-index#az-login) コマンドでサインインします。

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、`AzureFunctionsQuickstart-rg` という名前のリソース グループを `westeurope` リージョンに作成します。 (リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsQuickstart-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。
    ::: zone-end  
    
1. [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して、リソース グループとリージョン内に汎用ストレージ アカウントを作成します。 次の例の `<STORAGE_NAME>` は適宜、グローバルに一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    このクイックスタートでは、ストレージ アカウントに関して数セント (米国ドル) の料金が発生します。
    
1. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して関数アプリを作成します。 次の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 

    ::: zone pivot="programming-language-python"  
    Python 3.8 を使用している場合は、`--runtime-version` を `3.8` に変更し、`--functions_version` を `3` に変更します。
    
    Python 3.6 を使用している場合は、`--runtime-version` を `3.6` に変更します。

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Node.js 8 を使用している場合は、さらに `--runtime-version` を `8` に変更します。

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    このコマンドでは、[Azure Functions 従量課金プラン](functions-scale.md#consumption-plan) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。
    
## <a name="deploy-the-function-project-to-azure"></a>Azure に関数プロジェクトをデプロイする

::: zone pivot="programming-language-typescript"  
Core Tools を使用して対象のプロジェクトを Azure にデプロイする前に、TypeScript ソース ファイルから JavaScript ファイルの運用対応のビルドを作成します。

次のコマンドを使用して、TypeScript プロジェクトをデプロイする準備をします。

```
npm run build:production 
```
::: zone-end  

必要なリソースが揃ったら、[func azure functionapp publish](functions-run-local.md#project-file-deployment) コマンドを使用して、ローカル関数プロジェクトを Azure の関数アプリにデプロイすることができます。 次の例の `<APP_NAME>` は、実際のアプリ名に置き換えてください。

```
func azure functionapp publish <APP_NAME>
```

"... という名前のアプリが見つからない" という内容のエラーが表示された場合は、数秒待ってからやり直してください。先ほどの `az functionapp create` コマンドの後、Azure でアプリが完全には初期化されていない可能性があります。

publish コマンドを実行すると、次のような出力結果が表示されます (簡潔にするため一部省略しています)。

<pre>
...

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
</pre>

## <a name="invoke-the-function-on-azure"></a>Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しているため、呼び出しは、その URL にブラウザーから HTTP 要求を送信するか、または curl などのツールを使用して行います。 どちらの場合も、`code` URL パラメーターは、関数エンドポイントの呼び出しを承認する一意の[関数キー](functions-bindings-http-webhook-trigger.md#authorization-keys)です。

# <a name="browser"></a>[ブラウザー](#tab/browser)

publish コマンドの出力に表示されている完全な**呼び出し URL** にクエリ パラメーター `&name=Functions` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

![Azure 上で実行された関数の出力をブラウザーで表示したところ](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

**呼び出し URL** にパラメーター `&name=Functions` を追加して [`curl`](https://curl.haxx.se/) を実行します。 "Hello Functions" というテキストがコマンドの出力として表示されます。

![Azure 上で実行された関数の出力を curl を使用して表示したところ](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> 公開された関数アプリのほぼリアルタイムのログを表示するには、[Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs) を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順である [Azure Storage キュー出力バインディングの追加](functions-add-output-binding-storage-queue-cli.md)に進む場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md)
