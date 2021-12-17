---
title: 'クイック スタート: カスタム コンテナー内で Azure Arc に関数アプリを作成する'
description: カスタムの Linux コンテナー内に最初の関数アプリをデプロイすることによって、Azure Arc で Azure Functions の使用を開始します。
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: a22907387a647ca46a5784661324d3f6edcc3594
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706929"
---
# <a name="create-your-first-function-on-azure-arc-using-a-custom-container-preview"></a>カスタム コンテナーを使用して Azure Arc に最初の関数を作成する (プレビュー)

このクイック スタートでは、カスタム コンテナー内で実行する Azure Functions プロジェクトを作成し、これを Docker Hub アカウントから [Azure Arc 対応の Kubernetes クラスター](../azure-arc/kubernetes/overview.md)にデプロイします。 詳細については、「[Azure Arc の App Service、Functions、および Logic Apps](../app-service/overview-arc-integration.md)」を参照してください。このシナリオでは、Linux で実行されている関数アプリのみをサポートしています。   

> [!NOTE]
> Azure Arc 対応 Kubernetes クラスターでの関数の実行は、現在プレビュー段階です。  

## <a name="prerequisites"></a>前提条件

ローカル コンピューター上:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) バージョン 12。 Node.js バージョン 10 もサポートされます。
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions でサポートされているバージョンの Python](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する 1 つまたは複数の個別の関数に対するコンテキストです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet --docker
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript --docker
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python には仮想環境が必要で、そのためのコマンドは、bash と Windows のコマンド ラインとで異なります。
    
     + Bash: 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + コマンド ライン:

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    ここで、仮想環境内にプロジェクトを作成します。 
    
    ```console
    func init LocalFunctionProj --python --docker
    ```
    ---

    `--docker` オプションによって、プロジェクトの `Dockerfile` が生成されます。これにより、Azure Functions および選択されたランタイムで使用するための適切なカスタム コンテナーが定義されます。

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

    このフォルダーには、プロジェクト用の Dockerfile の他のファイルが含まれています。たとえば、[local.settings.json](functions-develop-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 既定では、*local.settings.json* ファイルは *.gitignore* ファイルのソース管理から除外されます。 この除外は、Azure からダウンロードされたシークレットがファイルに含まれている可能性があるためです。

1. 生成された `Dockerfile` を開き、基本イメージの `3.0` タグを見つけます。 `3.0` タグがある場合は、`3.0.15885` タグに置き換えます。 たとえば、JavaScript アプリケーションでは、`FROM mcr.microsoft.com/azure-functions/node:3.0.15885` を持つように Docker ファイルを変更する必要があります。 このバージョンの基本イメージでは、Azure Arc 対応の Kubernetes クラスターへのデプロイがサポートされています。 

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  

## <a name="build-the-container-image-and-test-locally"></a>コンテナー イメージを作成してローカルでテストする

プロジェクト ルートの Dockerfile には、コンテナーで関数アプリを実行するために必要な最小の環境が記述されています。 Azure Functions でサポートされている基本イメージの詳細な一覧については、[Azure Functions 基本イメージ ページ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。

1. ルート プロジェクト フォルダーで、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを実行し、名前に `azurefunctionsimage`、タグに `v1.0.0` を指定します。   

    次のコマンドでは、コンテナーの Docker イメージがビルドされます。

    ```console
    docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
    ```

    この例では、`<DOCKER_ID>` を Docker Hub アカウント ID に置き換えてください。 コマンドが完了すると、新しいコンテナーをローカルで実行できます。
    
1. ビルドをテストするために、ローカル コンテナーで [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してイメージを実行します。この場合、ポート引数 `-p 8080:80` を追加します。

    ```console
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```

    ここで再び、`<DOCKER_ID` を Docker ID に置き換え、ポート引数 `-p 8080:80` を追加します

1. ローカル コンテナーでイメージが実行状態になったら、`http://localhost:8080/api/HttpExample?name=Functions` に移動します。そこに、前と同じ "hello" メッセージが表示されます。 HTTP によってトリガーされる関数では匿名の承認が使用されるため、関数はコンテナーで実行されている場合でも呼び出すことができます。 関数のアクセス キー設定は、コンテナー内でローカルに実行されるときに強制されます。 関数の呼び出しに問題がある場合、[関数へのアクセス](functions-bindings-http-webhook-trigger.md#authorization-keys)が匿名になっていることを確認してください。  

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

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Azure リソースを作成する 

新しい App Service Kubernetes 環境にコンテナーをデプロイする前に、次の 2 つのリソースをさらに作成する必要があります。

- [ストレージ アカウント](../storage/common/storage-account-create.md)。現在、ツールに必要であり、環境には含まれていません。
- コンテナーの実行のためのコンテキストを提供する関数アプリ。 関数アプリは App Service Kubernetes 環境で実行され、ローカル関数プロジェクトにマップされます。 関数アプリを使用すると、リソースの管理、デプロイ、および共有を容易にするための論理ユニットとして関数をグループ化できます。

> [!NOTE]
> 関数アプリは、Dedicated (App Service) プランの App Service Kubernetes 環境で実行されます。 既存のプランを使用せずに関数アプリを作成すると、プランが自動的に作成されます。  

### <a name="create-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用して、リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

> [!NOTE]  
> ストレージ アカウントは、現在、Azure Functions ツールに必要です。 

前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。 値 `--location` は標準の Azure リージョンです。 

### <a name="create-the-function-app"></a>Function App の作成

[az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドを実行して、環境内に新しい関数アプリを作成します。

# <a name="c"></a>[C\#](#tab/csharp)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```
---

この例では、`<CUSTOM_LOCATION_ID>` を、App Service Kubernetes 環境に対して決定したカスタムの場所の ID に置き換えます。 また、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` をグローバルに一意の適切な名前に、`<DOCKER_ID>` を Docker Hub ID にそれぞれ置き換えます。 

*deployment-container-image-name* パラメーターでは、関数アプリに使用するイメージを指定します。 デプロイに使用されているイメージに関する情報は、[az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) コマンドを使用して表示できます。 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) コマンドを使用して、別のイメージからデプロイすることもできます。

関数アプリを初めて作成すると、Docker Hub から最初のイメージがプルされます。 また、Docker Hub から [Azure への継続的デプロイを有効にする](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure)こともできます。  

イメージの SSH を有効にする方法については、「[SSH 接続を有効にする](functions-create-function-linux-custom-image.md#enable-ssh-connections)」を参照してください。

### <a name="set-required-app-settings"></a>必須のアプリ設定を設定する

以下のコマンドを実行して、ストレージ アカウント接続文字列のためのアプリ設定を作成します。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv)
az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
```

このコードは、ユーザーのローカル コンピューター上の Cloud Shell または Bash のいずれかで実行する必要があります。 `<STORAGE_NAME>` をストレージ アカウントの名前に、`<APP_NAME>` を関数アプリ名に置き換えます。  

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

## <a name="next-steps"></a>次の手順

これで、Azure Arc 対応 App Service Kubernetes 環境のコンテナーで実行する関数アプリが作成されたので、Queue Storage 出力バインドを追加して Azure Storage に接続できます。

# <a name="c"></a>[C\#](#tab/csharp)  

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

# <a name="python"></a>[Python](#tab/python)  

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

---
