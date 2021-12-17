---
title: カスタム イメージを使用して Linux 上で Azure Functions を作成する
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830536"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>カスタム コンテナーを使用して Linux で関数を作成する

このチュートリアルでは、コードを作成し、Linux の基本イメージを使用したカスタム Docker コンテナーとして Azure Functions にデプロイします。 カスタム イメージを使用するのは通常、特定の言語バージョン、特定の依存関係、または組み込みイメージで提供されない構成が関数に必要になるときです。

::: zone pivot="programming-language-other"
Azure Functions では、[カスタム ハンドラー](functions-custom-handlers.md)を使用するすべての言語またはランタイムがサポートされています。 このチュートリアルで使用する R プログラミング言語など、一部の言語では、カスタム コンテナーの使用を必要とする依存関係として、ランタイムまたは追加のライブラリをインストールする必要があります。
::: zone-end

関数コードをカスタム Linux コンテナーにデプロイするには、[Premium プラン](functions-premium-plan.md)または[専用 (App Service) プラン](dedicated-plan.md)のホスティングが必要です。 このチュートリアルを完了すると、お使いの Azure アカウントで数ドルのコストが発生します。これは、完了時に[リソースをクリーンアップする](#clean-up-resources)ことによって最小限に抑えることができます。

[Linux でホストされる初めての関数の作成](./create-first-function-cli-csharp.md?pivots=programming-language-python)に関するページで説明されている既定の Azure App Service コンテナーを使用することもできます。 Azure Functions でサポートされている基本イメージについては、[Azure Functions 基本イメージ リポジトリ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。

このチュートリアルでは、以下の内容を学習します。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Azure Functions Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * 関数アプリ用の関連リソースを Azure に作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * コンテナーへの SSH 接続を有効にします。
> * Queue storage の出力バインドを追加します。 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Azure Functions Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * 関数アプリ用の関連リソースを Azure に作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * コンテナーへの SSH 接続を有効にします。
::: zone-end

このチュートリアルは、Windows、macOS、または Linux が動作している任意のコンピューターで実行できます。 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>ローカル関数プロジェクトを作成してテストする

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
ターミナルまたはコマンド プロンプトで、自分が選択した言語に合わせて次のコマンドを実行し、現在のフォルダーに関数アプリ プロジェクトを作成します。  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[インプロセス](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Functions プロジェクトを生成します。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` パラメーターは、使用する Java のバージョンを Functions Runtime に指示します。 Java 11 で関数を実行する場合は、`-DjavaVersion=11` を使用します。 `-DjavaVersion` を指定しない場合、Maven の既定値は Java 8 になります。 詳細については、「[Java のバージョン](functions-reference-java.md#java-versions)」を参照してください。

> [!IMPORTANT]
> この記事の作業を行うには、`JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。

Maven により、デプロイ時にプロジェクトの生成を終了するための値の入力が求められます。   
入力を求められたら、次の値を入力します。

| Prompt | 値 | 説明 |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Java の[パッケージ命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)に従って、すべてのプロジェクトにわたって対象のプロジェクトを一意に識別する値。 |
| **artifactId** | `fabrikam-functions` | バージョン番号のない、jar の名前である値。 |
| **version** | `1.0-SNAPSHOT` | 既定値を選択します。 |
| **package** | `com.fabrikam.functions` | 生成された関数コードの Java パッケージである値。 既定値を使用します。 |

「`Y`」と入力するか、Enter キーを押して確認します。

Maven により、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルが作成されます (この例では `fabrikam-functions`)。 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

`--docker` オプションによって、プロジェクトの `Dockerfile` が生成されます。これにより、Azure Functions および選択されたランタイムで使用するための適切なカスタム コンテナーが定義されます。

::: zone pivot="programming-language-java"  
プロジェクト フォルダーに移動します。

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[インプロセス](#tab/in-process)
Dockerfile を変更する必要はありません。
# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)
Dockerfile を開き、最初の `FROM` ステートメントの後に次の行を追加します (まだ存在しない場合)。

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` により、お使いのプロジェクトに C# コード ファイルが作成されます。

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。このサブフォルダーには、*function.json* という名前の構成ファイルが含まれます。

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
テキスト エディターで、プロジェクト フォルダー内に *handler.R* という名前のファイルを作成します。 その内容として、以下を追加します。

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

*host.json* の `customHandler` セクションを、カスタム ハンドラーのスタートアップ コマンドを構成するように変更します。

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

関数をローカルでテストするために、プロジェクト フォルダーのルートでローカルの Azure Functions ランタイム ホストを起動します。 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

出力に `HttpExample` エンドポイントが表示されたら、`http://localhost:7071/api/HttpExample?name=Functions` に移動します。 `name` クエリ パラメーターに指定された値、`Functions` をエコーバックする "hello" メッセージがブラウザーに表示されます。

**Ctrl** - **C** キーを使用してホストを停止します。

## <a name="build-the-container-image-and-test-locally"></a>コンテナー イメージを作成してローカルでテストする

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(省略可) プロジェクト フォルダーのルートにある *Dockerfile* を確認します。 Dockerfile には、Linux 上で関数アプリを実行するために必要な環境が記述されています。  Azure Functions でサポートされている基本イメージの詳細な一覧については、[Azure Functions 基本イメージ ページ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。
::: zone-end

::: zone pivot="programming-language-other"
プロジェクト フォルダーのルートにある *Dockerfile* を確認します。 Dockerfile には、Linux 上で関数アプリを実行するために必要な環境が記述されています。 カスタム ハンドラー アプリケーションでは、そのベースとして `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` イメージが使用されます。

*Dockerfile* を、R をインストールするように変更します。*Dockerfile* の内容を、次のように置き換えます。

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

ルート プロジェクト フォルダーで、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを実行し、名前に `azurefunctionsimage`、タグに `v1.0.0` を指定します。 `<DOCKER_ID>` を Docker Hub アカウント ID で置換します。 このコマンドでは、コンテナーの Docker イメージがビルドされます。

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

コマンドが完了すると、新しいコンテナーをローカルで実行できます。
    
ビルドをテストするために、ローカル コンテナーで [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してイメージを実行します。この場合も、`<DOCKER_ID` は実際の Docker ID に置き換え、ポート引数 `-p 8080:80` を追加してください。

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[インプロセス](#tab/in-process)
イメージがローカル コンテナーで開始したら、`http://localhost:8080/api/HttpExample?name=Functions` に移動します。そこに、前と同じ "hello" メッセージが表示されるはずです。 作成した HTTP トリガー関数は匿名承認を使用するため、コンテナーで実行されている関数は、アクセス キーを取得しなくても呼び出すことができます。 詳細については、「[承認キー]」を参照してください。
# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)
イメージがローカル コンテナーで開始したら、`http://localhost:8080/api/HttpExample` に移動します。そこに、前と同じ挨拶メッセージが表示されるはずです。 作成した HTTP トリガー関数は匿名承認を使用するため、コンテナーで実行されている関数は、アクセス キーを取得しなくても呼び出すことができます。 詳細については、「[承認キー]」を参照してください。

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
イメージがローカル コンテナーで開始したら、`http://localhost:8080/api/HttpExample?name=Functions` に移動します。そこに、前と同じ "hello" メッセージが表示されるはずです。 作成した HTTP トリガー関数は匿名承認を使用するため、コンテナーで実行されている関数は、アクセス キーを取得しなくても呼び出すことができます。 詳細については、「[承認キー]」を参照してください。
::: zone-end  

コンテナー内の関数アプリを確認したら、**Ctrl** + **C** キーで Docker を停止します。

## <a name="push-the-image-to-docker-hub"></a>イメージを Docker Hub にプッシュする

Docker Hub は、イメージのホストとしてイメージ サービスとコンテナー サービスを提供するコンテナー レジストリです。 イメージを共有するには (Azure へのデプロイも含む)、それをレジストリにプッシュする必要があります。

1. まだ Docker にサインインしていない場合は、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドでサインインします。`<docker_id>` は、実際の Docker ID に置き換えてください。 このコマンドでは、ユーザー名とパスワードを入力するよう求められます。 "ログインに成功しました" のメッセージで、サインインしていることを確認します。

    ```console
    docker login
    ```
    
1. サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。ここでも、`<docker_id>` は実際の Docker ID に置き換えてください。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. ネットワーク速度によっては、イメージの初回プッシュに数分かかる場合があります (それ以降に行う変更のプッシュは、はるかに短い時間で済みます)。 待っている間、次のセクションに進んで、別のターミナルで Azure リソースを作成することができます。

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- [リソース グループ](../azure-resource-manager/management/overview.md)。関連リソースの論理コンテナーです。
- [ストレージ アカウント](../storage/common/storage-account-create.md)。関数についての情報 (状態など) を維持する目的で使用されます。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。 Azure CLI と PowerShell の両方がサポートされます。

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) コマンドで Azure アカウントにサインインします。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Azure アカウントにサインインします。

    ---

1. 選択したリージョンに `AzureFunctionsContainers-rg` という名前のリソース グループを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 上記のコマンドで、[az account list-locations](/cli/azure/account#az_account_list_locations) コマンドから返された使用可能なリージョン コードを使用して、`<REGION>` を自分の近くのリージョンに置き換えます。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドレットから返される利用可能なリージョンを使用してください。

    ---

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドでストレージ アカウントを作成します。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットでストレージ アカウントを作成します。

    ---

    前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。
    
1. コマンドを使用して、Azure Functions 用の Premium プランを `myPremiumPlan` という名前で作成します。価格レベルは **Elastic Premium 1** (`--sku EP1`) とし、`<REGION>` を指定し、作成先を Linux コンテナー (`--is-linux`) とします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    ここでは、必要に応じてスケーリングできる Premium プランを使用します。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。 コストを計算するには、[Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

    また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>イメージを使用して Azure 上の関数アプリを作成、構成する

Azure 上の関数アプリでは、ホスティング プランで関数の実行を管理します。 このセクションでは、前のセクションの Azure リソースを使用して、Docker Hub 上のイメージから関数アプリを作成し、Azure Storage への接続文字列を使用してそれを構成します。

1. 次のコマンドを使用して、関数アプリを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで、*deployment-container-image-name* パラメーターは関数アプリに使用するイメージを指定します。 デプロイに使用されているイメージに関する情報は、[az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) コマンドを使用して表示できます。 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) コマンドを使用して、別のイメージからデプロイすることもできます。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    この例では、`<STORAGE_NAME>` を、前のセクションで使用したストレージ アカウントの名前に置き換えます。 また、`<APP_NAME>` は適宜グローバルに一意の名前に、`<DOCKER_ID>` は実際の DockerHub ID に置き換えます。    
    
    > [!TIP]  
    > host.json ファイルで [`DisableColor` の設定](functions-host-json.md#console)を使用して、ANSI 制御文字がコンテナー ログに書き込まれないようにすることができます。 

1. 次のコマンドを使用して、作成したストレージ アカウントの接続文字列を取得します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    ストレージ アカウントの接続文字列は、[az storage account show-connection-string](/cli/azure/storage/account) コマンドを使用することで返されます。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) コマンドレットによって返されるキーは、ストレージ アカウントの接続文字列を作成するために使用されます。

    ---    

    `<STORAGE_NAME>` を、前に作成したストレージ アカウントの名前に置き換えます。

1. 次のコマンドを使用して、この設定を関数アプリに追加します。 
 
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) コマンドによって、設定が作成されます。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) コマンドレットによって、設定が作成されます。

    ---

    このコマンドで、`<APP_NAME>` を関数アプリの名前に、`<CONNECTION_STRING>` を前の手順の接続文字列に置き換えます。 接続は、`DefaultEndpointProtocol=` で始まる長いエンコードされた文字列である必要があります。
 

1. 関数でこの接続文字列を使用してストレージ アカウントにアクセスできるようになりました。

> [!NOTE]    
> カスタム イメージをプライベート コンテナー アカウントに発行する場合は、接続文字列に Dockerfile 内の環境変数を使用する必要があります。 詳細については、[ENV の手順](https://docs.docker.com/engine/reference/builder/#env)を参照してください。 また、変数 `DOCKER_REGISTRY_SERVER_USERNAME` および `DOCKER_REGISTRY_SERVER_PASSWORD` も設定する必要があります。 これらの値を使用するには、イメージをリビルドしてそのイメージをレジストリにプッシュした後、Azure で関数アプリを再起動する必要があります。

## <a name="verify-your-functions-on-azure"></a>Azure 上で関数を確認する

イメージを Azure の関数アプリにデプロイしたら、関数を前のように HTTP 要求を通じて呼び出すことができます。
ブラウザーで、次のような URL に移動します。

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[インプロセス](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

`<APP_NAME>` をお使いの関数アプリの名前に置き換えます。 この URL にアクセスすると、この関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

## <a name="enable-continuous-deployment-to-azure"></a>Azure への継続的デプロイを有効にする

レジストリ内のイメージを更新するたびに、イメージのデプロイを Azure Functions に自動的に更新させることができます。

1. 次のコマンドを使用して継続的デプロイを有効にし、Webhook URL を取得します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) コマンドは、継続的デプロイを有効にして、デプロイの Webhook URL を返します。 この URL は、後から [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) コマンドを使用していつでも取得できます。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    `DOCKER_ENABLE_CI` アプリケーション設定は、コンテナー リポジトリから継続的デプロイを有効にするかどうかを制御します。  [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) コマンドレットは、デプロイ Webhook の URL を返します。

    ---    

    これまでと同様に、`<APP_NAME>` をお使いの関数アプリ名に置き換えます。 

1. デプロイの Webhook URL をクリップボードにコピーします。

1. [[Docker Hub]](https://hub.docker.com/) を開いてサインインし、ナビゲーション バーの **[Repositories]\(リポジトリ\)** を選択します。 イメージを検索して選択し、 **[Webhooks]** タブを選択します。次に、**Webhook の名前** を指定して、 **[Webhook URL]** に URL を貼り付け、 **[Create]\(作成\)** を選択します。

    ![DockerHub リポジトリに Webhook を追加する](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Webhook の設定後は、Docker Hub でイメージを更新するたびに、それが Azure Functions によって再デプロイされます。

## <a name="enable-ssh-connections"></a>SSH 接続を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 SSH が有効になっている場合は、App Service Advanced Tools (Kudu) を使用してコンテナーに接続できます。 SSH を使用してコンテナーに簡単に接続できるように、Azure Functions には、SSH が既に有効になっている基本イメージが用意されています。 必要な作業は、Dockerfile を編集してから、イメージをリビルドして再デプロイするだけです。 その後、[高度なツール (Kudu)] を使用してコンテナーに接続することができます。

1. Dockerfile で、`FROM` 命令の基本イメージに文字列 `-appservice` を追加します。

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. もう一度 `docker build` コマンドを使用してイメージをリビルドします。`<docker_id>` は、実際の Docker ID に置き換えてください。

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 更新済みのイメージを Docker Hub にプッシュします。アップロードする必要があるのは、イメージの更新済みセグメントのみであるため、初回プッシュ時よりもはるかに短時間で済みます。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions によってイメージが自動的に関数アプリに再デプロイされます。この処理の所要時間は 1 分未満です。

1. ブラウザーで `https://<app_name>.scm.azurewebsites.net/` を開きます。`<app_name>` は、一意の名前に置き換えてください。 この URL は、関数アプリ コンテナーの [高度なツール (Kudu)] エンドポイントです。

1. 自分の Azure アカウントにサインインし、 **[SSH]** を選択して、コンテナーとの接続を確立します。 Azure によるコンテナー イメージの更新がまだ行われている場合、接続に少し時間がかかる場合があります。

1. コンテナーとの接続が確立されたら、`top` コマンドを実行して、現在実行中のプロセスを表示します。 

    ![SSH セッションで実行されている Linux top コマンド](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>Azure Queue Storage に書き込む

Azure Functions を使用すると、独自の統合コードを記述することなく他の Azure サービスやリソースに関数を接続できます。 これらの *バインド* は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

このセクションでは、関数と Azure Queue Storage を統合する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>バインディング拡張機能を登録する
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>出力バインディングを使用するコードを追加する

キュー バインディングが定義されたら、バインディング パラメーターを使用してメッセージをキューに書き込むように関数を更新することができます。
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>レジストリ内のイメージを更新する

1. ルート フォルダーで `docker build` コマンドを再度実行します。今回は、タグ内のバージョンを `v1.0.1` に更新します。 以前と同様に、`<docker_id>` を Docker Hub アカウント ID に置き換えてください。

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. `docker push` を使用して、更新済みのイメージをリポジトリにプッシュして戻します。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 継続的デリバリーを構成したため、レジストリにあるイメージを再度更新すると、Azure にある関数アプリが自動的に更新されます。

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage キューのメッセージを確認する

ブラウザーで、これまでと同じ URL を使用して自分の関数を呼び出します。 以前と同じ応答がブラウザーに表示されるはずです。その部分については、関数コードに変更を加えていないためです。 ただし追加したコードでは、URL パラメーター `name` を使用して、`outqueue` ストレージ キューにメッセージを書き込みました。

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースを使用して、引き続き Azure 関数に取り組む場合は、それらのリソースをすべてそのままにしてかまいません。 Azure Functions 用の Premium プランを作成したため、継続するためのコストとして、1 日につき 1 ドルまたは 2 ドルの料金がかかります。

継続コストを避けるためには、`AzureFunctionsContainer-rg` リソース グループを削除して、そのグループのリソースをすべてクリーンアップしてください。 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>次のステップ

+ [関数の監視](functions-monitoring.md)
+ [スケールとホスティングのオプション](functions-scale.md)
+ [Kubernetes ベースのサーバーレス ホスティング](functions-kubernetes-keda.md)

[承認キー]: functions-bindings-http-webhook-trigger.md#authorization-keys