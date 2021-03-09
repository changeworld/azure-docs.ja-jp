---
title: コマンド ラインから C# 関数を作成する - Azure Functions
description: コマンド ラインから C# 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044325"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>クイックスタート: Azure でコマンド ラインから C# 関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する C# クラス ライブラリベースの関数を作成します。 コードをローカルでテストした後、デプロイします。 <abbr title="コードをデプロイして管理するプロセスは、ランタイムのコンピューティング環境によって省力化されています。アプリケーション開発者がサーバーの細部を意識する必要は一切ありません。">サーバーレス</abbr> 環境 <abbr title="アプリケーションのための低コストのサーバーレス コンピューティング環境を提供する Azure のサービス。">Azure Functions</abbr>.

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [Visual Studio Code ベースのバージョン](create-first-function-vs-code-csharp.md)も存在します。

## <a name="1-prepare-your-environment"></a>1.環境を準備する

+ アクティブなサブスクリプションが含まれる Azure アカウントを取得します。 <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">account</abbr> with an active <abbr title="Azure でのリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download) をインストールします

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x をインストールします。

+ Either the <abbr title="Azure portal を使用する代わりに、ローカルの開発用コンピューターから Azure リソースを操作するための一連のクロスプラットフォーム コマンド ライン ツール。">Azure CLI</abbr> or <abbr title="Azure portal を使用する代わりに、ローカルの開発用コンピューターから Azure リソースを操作するためのコマンドを提供する PowerShell モジュール。">Azure PowerShell</abbr> Azure リソースを作成するための

    + [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

    + [Azure PowerShell](/powershell/azure/install-az-ps) バージョン 5.0 以降。

---

### <a name="2-verify-prerequisites"></a>2. 前提条件を確認する

前提条件を確認します。前提条件は、Azure リソースの作成に Azure CLI を使用するか、Azure PowerShell を使用するかによって異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、 <abbr title="ローカル コンピューターで Azure Functions を操作するための一連のコマンド ライン ツール。">Azure Functions Core Tools</abbr> のバージョンが 3.x であることを確認します。

+ `az --version` を **実行** して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を **実行** して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `dotnet --list-sdks` を **実行** して、.NET Core SDK バージョン 3.1.x がインストールされていることを確認します。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+`func --version` を **実行** して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `(Get-Module -ListAvailable Az).Version` を **実行** し、バージョン 5.0 以降であることを確認します。 

+ `Connect-AzAccount` を **実行** して Azure にサインインし、アクティブなサブスクリプションを確認します。

+ `dotnet --list-sdks` を **実行** して、.NET Core SDK バージョン 3.1.x がインストールされていることを確認します。

---

## <a name="3-create-a-local-function-project"></a>3. ローカル関数プロジェクトを作成する

このセクションでは、ローカル <abbr title="個別に存在する 1 つまたは複数の関数に使用される論理上のコンテナー。これらをまとめてデプロイして管理することができます。">Azure Functions プロジェクト</abbr> を C# で作成します。 プロジェクト内の各関数は、特定のトリガーに応答します。 <abbr title="HTTP 要求、キュー メッセージ、特定の時刻など、関数のコードを呼び出すイベント。">トリガー (trigger)</abbr>.

1. `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. 「cd LocalFunctionProj」を **実行** してプロジェクト フォルダーに移動します。 <abbr title="このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、local.settings.json や host.json といった名前の構成ファイルです。 local.settings.json には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で .gitignore ファイルによってソース管理から除外されます。">プロジェクト フォルダー</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. 次のコマンドを使用して、プロジェクトに関数を追加します。
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name` 引数は、関数の一意の名前です (HttpExample)。

    `--template` 引数で、関数のトリガー (HTTP) を指定します。

    
    <br/>   
    <details>  
    <summary><strong>省略可: HttpExample.cs のコード</strong></summary>  
    
    *HttpExample.cs* には、`Run` メソッドが含まれています。これは、トリガー動作を定義する **HttpTriggerAttribute** で装飾された [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 内の `req` 変数で要求データを受け取ります。

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    返されるオブジェクトは、[OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) または [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) として応答メッセージを返す [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) です。 詳細については、「[Azure Functions の HTTP トリガーとバインド](./functions-bindings-http-webhook.md?tabs=csharp)」を参照してください。  
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


1. <kbd>Ctrl + C</kbd> キーを押し、<kbd>y</kbd> を選択して、関数ホストを停止します。

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5.関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、次のコマンドを使用してリソース グループ、ストレージ アカウント、関数アプリを作成する必要があります。 <abbr title="1 つの単位として管理できる関連する Azure リソースの論理コンテナー。">resource group</abbr>, a <abbr title="すべての Azure Storage データ オブジェクトを含むアカウント。 ストレージ アカウントによって、ストレージ データ用の一意の名前空間が提供されます。">ストレージ アカウント</abbr>, and a <abbr title="Azure でサーバーレス関数をホストするクラウド リソース。関数の実行基盤となるコンピューティング環境を提供します。">function app</abbr> by using the following commands:

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsQuickstart-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。
    
1. リソース グループとリージョン内に汎用 Azure ストレージ アカウントを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 <abbr title="名前は、全 Azure ユーザーが使用するすべてのストレージ アカウント全体でグローバルに一意である必要があります。 たとえば、contosobizappstorage20 のように、個人名または会社名、アプリケーション名、数値 ID の組み合わせを使用できます">unique in Azure Storage</abbr>. 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。


1. Azure に関数アプリを作成します。
"<STORAGE_NAME>** は、前の手順の名前に **置き換え** てください。
"<APP_NAME>" は、グローバルに一意の名前に **置き換え** てください。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    `<STORAGE_NAME>` は、前の手順で使用したアカウントの名前に置き換えてください。

    `<APP_NAME>` は <abbr title="Azure ユーザー全体でグローバルに一意であるべき名前。 たとえば、contoso-bizapp-func-20 のように、個人名または組織名、アプリケーション名、数値 ID の組み合わせを使用できます。"><一意の名前></abbr>. `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 

    <br/>
    <details>
    <summary><strong>Azure にプロビジョニングされるリソースのコストを教えてください</strong></summary>

    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md) (ここで発生する使用量に関しては無料) で、指定された言語ランタイムで実行される関数アプリを作成します。 また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6.Azure に関数プロジェクトをデプロイする


func azure funtionapp publish <APP_NAME> をターミナルに **コピー** します。`<APP_NAME>` は、実際のアプリの名前に **置き換え** てください。
**[実行]**

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

`publish` コマンドの出力に表示されている完全な **呼び出し URL** をブラウザーのアドレス バーにコピーします。 クエリ パラメーター **&name=Functions** を **追加** します。 

![Azure 上で実行された関数の出力をブラウザーで表示したところ](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8.リソースをクリーンアップする

[次の手順](#next-steps)に進んで Azure Storage キュー出力バインディングを追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。 <abbr title="関数と他のリソースとの間の宣言型の接続。 入力バインドは関数にデータを提供し、出力バインドは関数から他のリソースにデータを提供します。">binding</abbr>, keep all your resources in place as you'll build on what you've already done.

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
