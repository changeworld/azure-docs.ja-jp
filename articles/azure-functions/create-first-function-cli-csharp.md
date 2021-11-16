---
title: コマンド ラインから C# 関数を作成する - Azure Functions
description: コマンド ラインから C# 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 09/14/2021
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: 75d8177b8feaeb5bf4dfa0d7a3791b65fab930d4
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026685"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>クイックスタート: Azure でコマンド ラインから C# 関数を作成する

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する C# 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]

この記事では、.NET 6.0 で実行される HTTP によってトリガーされる関数を作成します。 また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-csharp.md)も存在します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ [.NET 6.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 4.x。

+ 次のいずれかのツール。Azure リソースの作成に使用します。

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + Azure [Az PowerShell モジュール](/powershell/azure/install-az-ps) バージョン 5.9.0 以降。

アクティブなサブスクリプションを含む Azure アカウントも必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="prerequisite-check"></a>前提条件のチェック

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 4.x であることを確認します。

+ `dotnet --list-sdks` を実行して、必要なバージョンがインストールされていることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 4.x であることを確認します。

+ `dotnet --list-sdks` を実行して、必要なバージョンがインストールされていることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を実行し、バージョン 5.0 以降であることを確認します。

+ `Connect-AzAccount` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

---

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。

    # <a name="in-process"></a>[インプロセス](#tab/in-process)

    ```console
    func init LocalFunctionProj --dotnet
    ```

    # <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

    ```console
    func init LocalFunctionProj --worker-runtime dotnet-isolated
    ```
    ---

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-develop-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```

    `func new` によって、HttpExample.cs コード ファイルが作成されます。

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

#### <a name="httpexamplecs"></a>HttpExample.cs

テンプレートから生成される関数コードは、コンパイル済み C# プロジェクトの種類によって異なります。

# <a name="in-process"></a>[インプロセス](#tab/in-process)

*HttpExample.cs* には、`Run` メソッドが含まれています。これは、トリガー動作を定義する **HttpTriggerAttribute** で装飾された [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 内の `req` 変数で要求データを受け取ります。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

返されるオブジェクトは、[OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) または [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) として応答メッセージを返す [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) です。

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

*HttpExample.cs* には、`Run` メソッドが含まれています。これは、トリガー動作を定義する **HttpTriggerAttribute** で装飾された [HttpRequestData](/dotnet/api/microsoft.azure.functions.worker.http.httprequestdata) オブジェクト内の `req` 変数で要求データを受け取ります。 分離プロセス モデルのため、`HttpRequestData` は、要求オブジェクト自体ではなく、実際の `HttpRequest` の表現です。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs":::

返されるオブジェクトは、HTTP 応答に戻されるデータを格納する [HttpResponseData](/dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata) オブジェクトです。

---

詳細については、「[Azure Functions の HTTP トリガーとバインド](./functions-bindings-http-webhook.md?tabs=csharp)」を参照してください。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

    ```
    func start
    ```

    出力の最後の方に、次の行があります。

    <pre>
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    >[!NOTE]
    > HttpExample が上記のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. `HttpExample` 関数の URL をこの出力からブラウザーにコピーします。

    # <a name="in-process"></a>[インプロセス](#tab/in-process)

     関数の URL にクエリ文字列 `?name=<YOUR_NAME>` を追加して、`http://localhost:7071/api/HttpExample?name=Functions` のような完全な URL を作成します。 ブラウザーには、クエリ文字列値をエコー バックする応答メッセージが表示されるはずです。 要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

    # <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

    この関数の URL にアクセスすると、 _[Azure Functions へようこそ]_ メッセージが表示されるはずです。

    ---

1. 完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/in-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/isolated-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet-isolated --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/in-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location '<REGION>'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/isolated-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet-isolated -FunctionsVersion 3 -Location '<REGION>'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。

    ---

    > [!NOTE]
    > このコマンドによって、3.x バージョンの Azure Functions ランタイムを使用し、関数アプリが作成されます。 後で実行する `func azure functionapp publish` コマンドによってアプリがバージョン 4.x に更新されます。

    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。

    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

## <a name="invoke-the-function-on-azure"></a>Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しており、GET 要求をサポートしているため、これを呼び出すには、その URL に対して HTTP 要求を行います。 これは、ブラウザーで行うのが最も簡単です。

# <a name="in-process"></a>[インプロセス](#tab/in-process)

publish コマンドの出力に表示されている完全な **呼び出し URL** にクエリ パラメーター `?name=Functions` を追加して、ブラウザーのアドレス バーにコピーします。 この URL にアクセスすると、この関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

publish コマンドの出力に表示されている完全な **呼び出し URL** をブラウザーのアドレス バーにコピーします。 この URL にアクセスすると、この関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>次のステップ

# <a name="in-process"></a>[インプロセス](#tab/in-process)

> [!div class="nextstepaction"]
> [Azure Queue Storage へ接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

> [!div class="nextstepaction"]
> [Azure Queue Storage へ接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=isolated-process)

---
