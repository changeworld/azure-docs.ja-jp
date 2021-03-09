---
title: Azure Functions の Python 関数をコマンド ラインから作成する
description: コマンド ラインから Python 関数を作成し、Azure Functions のサーバーレス ホスティングにローカル プロジェクトを発行する方法について説明します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: da7f6fdaedd8105363cc62bf55bae2cb5f72f234
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031652"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に Python 関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する Python 関数を作成します。 コードをローカルでテストした後、デプロイします。 <abbr title="コードをデプロイして管理するプロセスは、ランタイムのコンピューティング環境によって省力化されています。アプリケーション開発者がサーバーの細部を意識する必要は一切ありません。">サーバーレス</abbr> 環境 <abbr title="アプリケーションのための低コストのサーバーレス コンピューティング環境を提供する Azure のサービス。">Azure Functions</abbr>.

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-python.md)も存在します。

## <a name="1-configure-your-environment"></a>1. 環境を構成する

開始する前に、次の項目を用意する必要があります。

+ Azure <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">account</abbr> アクティブ <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。 
  
+ Either the <abbr title="Azure portal を使用する代わりに、ローカルの開発用コンピューターから Azure リソースを操作するための一連のクロスプラットフォーム コマンド ライン ツール。">Azure CLI</abbr> or <abbr title="Azure portal を使用する代わりに、ローカルの開発用コンピューターから Azure リソースを操作するためのコマンドを提供する PowerShell モジュール。">Azure PowerShell</abbr> Azure リソースを作成するための

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

+ [Python 3.8 (64 ビット)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 ビット)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 ビット)](https://www.python.org/downloads/release/python-368/)。これらはいずれも、バージョン 3.x の Azure Functions でサポートされています。

### <a name="11-prerequisite-check"></a>1.1 前提条件をチェックする

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、 <abbr title="ローカル コンピューターで Azure Functions を操作するための一連のコマンド ライン ツール。">Azure Functions Core Tools</abbr> のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、 <abbr title="ローカル コンピューターで Azure Functions を操作するための一連のコマンド ライン ツール。">Azure Functions Core Tools</abbr> のバージョンが 3.x であることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を実行し、バージョン 5.0 以降であることを確認します。 

+ `Connect-AzAccount` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a>仮想環境を作成してアクティブにする

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

以降のコマンドはすべて、このアクティブ化された仮想環境で実行します 

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. ローカル関数プロジェクトを作成する

このセクションでは、ローカル <abbr title="個別に存在する 1 つまたは複数の関数に使用される論理上のコンテナー。これらをまとめてデプロイして管理することができます。">Azure Functions プロジェクト</abbr> を Python で作成します。 プロジェクト内の各関数は、特定のトリガーに応答します。 <abbr title="HTTP 要求、キュー メッセージ、特定の時刻など、関数のコードを呼び出すイベントの種類。">トリガー (trigger)</abbr>.

1. `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```console
    func init LocalFunctionProj --python
    ```

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>LocalFunctionProj フォルダーには何が作成されますか?</strong></summary>
    
    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。
    </details>

1. 次のコマンドを使用して、プロジェクトに関数を追加します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name` 引数は、関数の一意の名前です (HttpExample)。

    `--template` 引数で、関数のトリガー (HTTP) を指定します。
    
    `func new` によって、関数と同じ名前のサブフォルダーが作成されます。そのサブフォルダーに、関数のコードが記述された *\_\_init\_\_.py* ファイルと、*function.json* という名前の構成ファイルが格納されます。

    <br/>    
    <details>
    <summary><strong>__init__.py のコード</strong></summary>
    
    *\_\_init\_\_.py* には、*function.json* 内の構成に従ってトリガーされる Python 関数 `main()` が含まれます。
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    HTTP トリガーの場合、この関数は、*function.json* に定義された変数 `req` で要求データを受け取ります。 `req` は、[azure.functions.HttpRequest クラス](/python/api/azure-functions/azure.functions.httprequest)のインスタンスです。 *function.json* に `$return` として定義されているリターン オブジェクトは、[azure.functions.HttpResponse クラス](/python/api/azure-functions/azure.functions.httpresponse)のインスタンスです。 詳細については、「[Azure Functions の HTTP トリガーとバインド](./functions-bindings-http-webhook.md?tabs=python)」を参照してください。
    </details>

    <br/>
    <details>
    <summary><strong>function.json のコード</strong></summary>

    *function.json* は、関数の <abbr title="関数と他のリソースとの間の宣言型の接続。 入力バインドは関数にデータを提供し、出力バインドは関数から他のリソースにデータを提供します。">入力バインディングと出力バインディング</abbr> (トリガーの種類を含む) を定義する構成ファイルです。
    
    `scriptFile` を変更することで、必要に応じて異なる Python ファイルを呼び出せます。
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    各バインディングは、方向、型、一意の名前を必要とします。 HTTP トリガーには、[`httpTrigger`](functions-bindings-http-webhook-trigger.md) 型の入力バインディングと、[`http`](functions-bindings-http-webhook-output.md) 型の出力バインディングが与えられます。    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4.関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

    ```
    func start
    ```

    出力の最後の方に、次の行があります。 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>HttpExample が出力に見当たりません</strong></summary>

    HttpExample が表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は <kbd>Ctrl+C</kbd> キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。
    </details>

1. この出力から **HttpExample** 関数の URL をブラウザーにコピーし、クエリ文字列 **?name=<YOUR_NAME>** を追加して、URL 全体を **http://localhost:7071/api/HttpExample?name=Functions** のようにします。 **Hello Functions** のようなメッセージがブラウザーに表示されます。

    ![ブラウザーでローカルに関数を実行した結果](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. 要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 完了したら、<kbd>Ctrl + C</kbd> キーを押し、<kbd>y</kbd> を選択して関数ホストを停止してください。

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5.関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、次のコマンドを使用してリソース グループ、ストレージ アカウント、関数アプリを作成する必要があります。 <abbr title="1 つの単位として管理できる関連する Azure リソースの論理コンテナー。">resource group</abbr>, a <abbr title="すべての Azure Storage データ オブジェクトを含むアカウント。 ストレージ アカウントによって、ストレージ データ用の一意の名前空間が提供されます。">ストレージ アカウント</abbr>, and a <abbr title="Azure でサーバーレス関数をホストするクラウド リソース。関数の実行基盤となるコンピューティング環境を提供します。">function app</abbr> by using the following commands:

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az-login) コマンドで Azure アカウントにサインインします。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Azure アカウントにサインインします。

    ---

1. `westeurope` リージョンに `AzureFunctionsQuickstart-rg` という名前のリソース グループを作成します。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 一般に、リソース グループとリソースは、 <abbr title="リソースが割り当てられている特定の Azure データ センターの地理的な呼称。">region</abbr> `az account list-locations` コマンドから返される利用可能なリージョンを使用してお近くのリージョンに作成します。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドレットから返される利用可能なリージョンを使用してください。

    ---

    Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsQuickstart-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。

1. リソース グループとリージョン内に汎用 Azure ストレージ アカウントを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドでストレージ アカウントを作成します。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットでストレージ アカウントを作成します。

    ---

    `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 <abbr title="名前は、全 Azure ユーザーが使用するすべてのストレージ アカウント全体でグローバルに一意である必要があります。 たとえば、contosobizappstorage20 のように、個人名または会社名、アプリケーション名、数値 ID の組み合わせを使用できます。">unique in Azure Storage</abbr>. 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。
    
    このクイックスタートでは、ストレージ アカウントに関して数セント (米国ドル) の料金が発生します。

1. Azure に関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 Python 3.7 または 3.6 を使用している場合は、`--runtime-version` をそれぞれ `3.7` または `3.6` に変更します。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) コマンドレットによって、Azure に関数アプリが作成されます。 Python 3.7 または 3.6 を使用している場合は、`-RuntimeVersion` をそれぞれ `3.7` または `3.6` に変更します。

    ---
    
    `<STORAGE_NAME>` は、前の手順で使用したアカウントの名前に置き換えてください。

    `<APP_NAME>` は <abbr title="Azure ユーザー全体でグローバルに一意であるべき名前。 たとえば、contoso-bizapp-func-20 のように、個人名または組織名、アプリケーション名、数値 ID の組み合わせを使用できます。">適宜、グローバルに一意の名前に置き換えてください。</abbr>. `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    <br/>
    <details>
    <summary><strong>Azure にプロビジョニングされるリソースのコストを教えてください</strong></summary>

    このコマンドでは、[Azure Functions 従量課金プラン](functions-scale.md#overview-of-plans) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6.Azure に関数プロジェクトをデプロイする

Azure への関数アプリの作成に成功したら、[func azure functionapp publish](functions-run-local.md#project-file-deployment) コマンドを使用して、**ローカル関数プロジェクトをデプロイ** することができます。  

次の例の `<APP_NAME>` は、実際のアプリ名に置き換えてください。

```console
func azure functionapp publish <APP_NAME>
```

`publish` コマンドを実行すると、次のような出力結果が表示されます (簡潔にするため一部省略しています)。

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7.Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しているため、呼び出しは、その URL にブラウザーから HTTP 要求を送信するか、または curl などのツールを使用して行います。 <abbr title="URL に対する HTTP 要求を生成するためのコマンド ライン ツール。 https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[ブラウザー](#tab/browser)

`publish` コマンドの出力に表示されている完全な **呼び出し URL** にクエリ パラメーター **&name=Functions** を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

![Azure 上で実行された関数の出力をブラウザーで表示したところ](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**呼び出し URL** にパラメーター **&name=Functions** を追加して [`curl`](https://curl.haxx.se/) を実行します。 "Hello Functions" というテキストがコマンドの出力として表示されます。

![Azure 上で実行された関数の出力を curl を使用して表示したところ](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 リアルタイム ストリーミング ログを表示する

Azure portal の Application Insights に凖リアルタイムの[ストリーミング ログ](functions-run-local.md#enable-streaming-logs)を表示するには、次のコマンドを実行します。

```console
func azure functionapp logstream <APP_NAME> --browser
```

`<APP_NAME>` をお使いの関数アプリの名前に置き換えます。

別のターミナル ウィンドウまたはブラウザーで、もう一度リモート関数を呼び出します。 Azure で実行された関数の詳細ログがターミナルに表示されます。 

<br/>

---

## <a name="8-clean-up-resources"></a>8.リソースをクリーンアップする

[次の手順](#next-steps)に進んで <abbr title="関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。 "> Azure Storage キュー出力バインド</abbr>を追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[問題がある場合は、お知らせください。](https://aka.ms/python-functions-qs-survey)
