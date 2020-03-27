---
title: カスタム イメージを使用して Linux 上で Azure Functions を作成する
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223729"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>カスタム コンテナーを使用して Linux で関数を作成する

このチュートリアルでは、Python コードを作成し、Linux の基本イメージを使用したカスタム Docker コンテナーとして Azure Functions にデプロイします。 カスタム イメージを使用するのは通常、特定の言語バージョン、特定の依存関係、または組み込みイメージで提供されない構成が関数に必要になるときです。

[Linux でホストされる初めての関数の作成](functions-create-first-azure-function-azure-cli-linux.md)に関するページで説明されている既定の Azure App Service コンテナーを使用することもできます。 Azure Functions でサポートされている基本イメージについては、[Azure Functions 基本イメージ リポジトリ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。

このチュートリアルでは、以下の内容を学習します。

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

このチュートリアルは、Windows、Mac OS、または Linux が動作している任意のコンピューターで実行できます。 このチュートリアルを完了すると、ご利用の Azure アカウントに数ドルのコストが発生します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.7.1846 以降
- [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.0.77 以降
- [Azure Functions 2.x ランタイム](functions-versions.md)
- 次の言語のランタイム コンポーネント:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x 以降](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 ビット](https://www.python.org/downloads/release/python-3610/)または [Python 3.7 - 64 ビット](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [Docker ID](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>前提条件のチェック

1. ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 2.7.1846 以降であることを確認します。
1. `az --version` を実行して、Azure CLI バージョンが 2.0.76 以降であることを確認します。
1. `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。
1. `docker login` を実行して Docker にサインインします。 Docker が動作していない場合、このコマンドは失敗します。その場合は、Docker を起動してコマンドを再試行してください。

## <a name="create-and-test-the-local-functions-project"></a>ローカル関数プロジェクトを作成してテストする

1. ターミナルまたはコマンド プロンプトで、このチュートリアル用のフォルダーを適切な場所に作成し、そのフォルダーに移動します。

1. 「[仮想環境を作成してアクティブにする](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv)」の手順に従って、このチュートリアルで使用する仮想環境を作成します。

1. 自分が選択した言語に合わせて次のコマンドを実行し、`LocalFunctionsProject` という名前のフォルダーに関数アプリ プロジェクトを作成します。 `--docker` オプションによって、プロジェクトの `Dockerfile` が生成されます。これにより、Azure Functions および選択されたランタイムで使用するための適切なカスタム コンテナーが定義されます。

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. プロジェクト フォルダーに移動します。

    ```
    cd LocalFunctionsProject
    ```
    
1. 次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. 関数をローカルでテストするために、*LocalFunctionsProject* フォルダーでローカルの Azure Functions ランタイム ホストを起動します。
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. 出力に `HttpExample` エンドポイントが表示されたら、`http://localhost:7071/api/HttpExample?name=Functions` に移動します。 "Hello, Functions" のようなメッセージがブラウザーに表示されます (選択したプログラミング言語によってわずかに異なります)。

1. **Ctrl** + **C** キーを使用してホストを停止します。

## <a name="build-the-container-image-and-test-locally"></a>コンテナー イメージを作成してローカルでテストする

1. (省略可) *LocalFunctionsProj* フォルダーの *Dockerfile" を確認します。 Dockerfile には、Linux 上で関数アプリを実行するために必要な環境が記述されています。 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Azure Functions でサポートされている基本イメージの詳細な一覧については、[Azure Functions 基本イメージ ページ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。
    
1. *LocalFunctionsProject* フォルダーで [docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを実行し、名前に `azurefunctionsimage`、タグに `v1.0.0` を指定します。 `<docker_id>` を Docker Hub アカウント ID で置換します。 このコマンドでは、コンテナーの Docker イメージがビルドされます。

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    コマンドが完了すると、新しいコンテナーをローカルで実行できます。
    
1. ビルドをテストするために、ローカル コンテナーで [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してイメージを実行します。この場合も、`<docker_id>` は実際の Docker ID に置き換え、ポート引数 `-p 8080:80` を追加してください。

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. ローカル コンテナーでイメージが実行状態になったら、ブラウザーで `http://localhost:8080` を開きます。以下に示したプレースホルダー画像が表示されます。 この時点で画像が表示されるということは、Azure で実行されるときと同じように、ローカル コンテナーで関数が実行されているということです。つまり、*function.json* に `"authLevel": "function"` プロパティで定義されたアクセス キーによって関数は保護されています。 ただし、Azure の関数アプリに対してまだコンテナーが発行されていないため、そのキーはまだ利用できません。 ローカルでテストしたい場合は、Docker を停止し、承認プロパティを `"authLevel": "anonymous"` に変更して、イメージをリビルドしてから Docker を再起動してください。 その後、*function.json* で `"authLevel": "function"` をリセットします。 詳細については、[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)に関するセクションを参照してください。

    ![コンテナーがローカルで実行されていることを示すプレースホルダー画像](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. コンテナー内の関数アプリを確認したら、**Ctrl** + **C** キーで Docker を停止します。

## <a name="push-the-image-to-docker-hub"></a>イメージを Docker Hub にプッシュする

Docker Hub は、イメージのホストとしてイメージ サービスとコンテナー サービスを提供するコンテナー レジストリです。 イメージを共有するには (Azure へのデプロイも含む)、それをレジストリにプッシュする必要があります。

1. まだ Docker にサインインしていない場合は、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドでサインインします。`<docker_id>` は、実際の Docker ID に置き換えてください。 このコマンドでは、ユーザー名とパスワードを入力するよう求められます。 "ログインに成功しました" のメッセージで、サインインしていることを確認します。

    ```
    docker login
    ```
    
1. サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。ここでも、`<docker_id>` は実際の Docker ID に置き換えてください。

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. ネットワーク速度によっては、イメージの初回プッシュに数分かかる場合があります (それ以降に行う変更のプッシュは、はるかに短い時間で済みます)。 待っている間、次のセクションに進んで、別のターミナルで Azure リソースを作成することができます。

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイするには、3 つのリソースを作成する必要があります。

- リソース グループ。関連リソースの論理コンテナーです。
- Azure ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
- Azure 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

Azure CLI コマンドを使用して、これらの項目を作成しましょう。 それぞれのコマンドからは、完了時に JSON 出力が返されます。

1. [az login](/cli/azure/reference-index#az-login) コマンドを使用して、Azure にサインインします。

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、`AzureFunctionsContainers-rg` という名前のリソース グループを `westeurope` リージョンに作成します。 (リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsContainers-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。
    
1. [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して、リソース グループとリージョン内に汎用ストレージ アカウントを作成します。 次の例の `<storage_name>` は適宜、グローバルに一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` では、標準的な汎用アカウントを指定します。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    このチュートリアルでは、ストレージ アカウントに関して数セントの料金が発生します。
    
1. コマンドを使用して、Azure Functions 用の Premium プランを `myPremiumPlan` という名前で作成します。価格レベルは **Elastic Premium 1** (`--sku EP1`)、リージョンは西ヨーロッパ (`-location westeurope`) または最寄りのリージョン、作成先は Linux コンテナー (`--is-linux`) とします。

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    カスタム関数コンテナーの Linux ホスティングは、[専用 (App Service) プラン](functions-scale.md#app-service-plan)および [Premium プラン](functions-premium-plan.md#features)でサポートされています。 ここでは、必要に応じてスケーリングできる Premium プランを使用します。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。 コストを計算するには、[Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

    また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>イメージを使用して Azure 上の関数アプリを作成、構成する

Azure 上の関数アプリでは、ホスティング プランで関数の実行を管理します。 このセクションでは、前のセクションの Azure リソースを使用して、Docker Hub 上のイメージから関数アプリを作成し、Azure Storage への接続文字列を使用してそれを構成します。

1. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して関数アプリを作成します。 次の例の `<storage_name>` は、前のセクションで使用したストレージ アカウントの名前に置き換えてください。 また、`<app_name>` は適宜グローバルに一意の名前に、`<docker_id>` は実際の Docker ID に置き換えます。

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    *deployment-container-image-name* パラメーターでは、関数アプリに使用するイメージを指定します。 デプロイに使用されているイメージに関する情報は、[az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) コマンドを使用して表示できます。 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) コマンドを使用して、別のイメージからデプロイすることもできます。

1. 作成したストレージ アカウントの接続文字列を [az storage account show-connection-string](/cli/azure/storage/account) コマンドで取得し、シェル変数 `storageConnectionString` に代入します。

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドを使用して、この設定を関数アプリに追加します。 次のコマンドで、`<app_name>` を関数アプリの名前に置き換え、`<connection_string>` を前の手順の接続文字列 ("DefaultEndpointProtocol=" で始まる長いエンコード文字列) に置き換えます。
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. 関数でこの接続文字列を使用してストレージ アカウントにアクセスできるようになりました。

> [!TIP]
> Bash では、クリップボードではなく、シェル変数を使用して接続文字列をキャプチャできます。 まず、次のコマンドを使用して、接続文字列の変数を作成します。
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> 次に、2 つ目のコマンドで変数を参照します。
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> カスタム イメージをプライベート コンテナー アカウントに発行する場合は、接続文字列に Dockerfile 内の環境変数を使用する必要があります。 詳細については、[ENV の手順](https://docs.docker.com/engine/reference/builder/#env)を参照してください。 また、変数 `DOCKER_REGISTRY_SERVER_USERNAME` および `DOCKER_REGISTRY_SERVER_PASSWORD` も設定する必要があります。 これらの値を使用するには、イメージをリビルドしてそのイメージをレジストリにプッシュした後、Azure で関数アプリを再起動する必要があります。

## <a name="verify-your-functions-on-azure"></a>Azure 上で関数を確認する

Azure 上の関数アプリにイメージをデプロイしたら、HTTP 要求を使用して関数を呼び出すことができます。 *function.json* 定義に `"authLevel": "function"` プロパティが存在するので、まずアクセス キー ("関数キー" とも呼ばれます) を取得して、それを URL パラメーターとしてエンドポイントへの要求に含める必要があります。

1. Azure portal を使用するか、または Azure CLI と `az rest` コマンドを使用して、アクセス (関数) キーを含む関数の URL を取得します。

    # <a name="portal"></a>[ポータル](#tab/portal)

    1. Azure portal にサインインし、ページの上部にある**検索**ボックスに関数アプリの名前を入力して、目的の関数アプリを検索します。 その結果から **App Service** リソースを選択します。

    1. 左側のナビゲーション パネルの **[関数 (読み取り専用)]** で、目的の関数の名前を選択します。

    1. 詳細パネルで **[</> 関数の URL の取得]** を選択します。
    
        ![Azure portal の [関数の URL の取得] コマンド](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. ポップアップで **[default (Function key)]\(既定 (関数キー)\)** を選択し、 **[コピー]** を選択します。 `?code=` に続く文字列がキーです

        ![Azure portal からの関数 URL のコピー](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > 関数アプリはコンテナーとしてデプロイされるため、ポータルでその関数コードを変更することはできません。 ローカル イメージでプロジェクトを更新して、再度イメージをレジストリにプッシュした後、Azure に再デプロイする必要があります。 後続のセクションで継続的デプロイを設定することができます。
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. 次の形式で URL 文字列を構成します。`<subscription_id>`、`<resource_group>`、`<app_name>` はそれぞれ、実際の Azure サブスクリプション ID、関数アプリのリソース グループ、関数アプリの名前に置き換えてください。

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        たとえば、URL は次のようなアドレスになります。

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > 便宜上、URL を環境変数に代入して `az rest` コマンドで使用することもできます。
    
    1. 次の `az rest` コマンド (Azure CLI バージョン 2.0.77 以降で利用可能) を実行します。`<uri>` は、直前の手順の URI 文字列 (引用符を含む) に置き換えてください。

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. コマンドの出力は関数キーです。 関数の完全な URL は `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` です。`<app_name>`、`<function_name>`、`<key>` は、お客様固有の値に置き換えてください。
    
        > [!NOTE]
        > ここで取得したキーは、関数アプリに含まれるすべての関数で使用できる "*ホスト*" キーです。一方、ポータル用に示した方法で取得されるのは、1 つの関数についてのキーだけです。

    ---

1. 関数 URL をブラウザーのアドレス バーに貼り付けます。この URL の末尾にパラメーター `&name=Azure` を追加してください。 "Hello Azure" といったテキストがブラウザーに表示されます。

    ![ブラウザーでの関数の応答。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. 承認をテストするために、URL から code= パラメーターを取り除き、関数から応答がないことを確認します。


## <a name="enable-continuous-deployment-to-azure"></a>Azure への継続的デプロイを有効にする

レジストリ内のイメージを更新するたびに、イメージのデプロイを Azure Functions に自動的に更新させることができます。

1. [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) コマンドを使用して継続的デプロイを有効にします。`<app_name>` は、実際の関数アプリの名前に置き換えてください。

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    このコマンドは、継続的デプロイを有効にして、デプロイの Webhook URL を返します。 (この URL は、後から [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) コマンドを使用していつでも取得できます。)

1. デプロイの Webhook URL をクリップボードにコピーします。

1. [[Docker Hub]](https://hub.docker.com/) を開いてサインインし、ナビゲーション バーの **[Repositories]\(リポジトリ\)** を選択します。 イメージを検索して選択し、 **[Webhooks]** タブを選択します。次に、**Webhook の名前**を指定して、 **[Webhook URL]** に URL を貼り付け、 **[Create]\(作成\)** を選択します。

    ![DockerHub リポジトリに Webhook を追加する](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Webhook の設定後は、Docker Hub でイメージを更新するたびに、それが Azure Functions によって再デプロイされます。

## <a name="enable-ssh-connections"></a>SSH 接続を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 SSH が有効になっている場合は、App Service Advanced Tools (Kudu) を使用してコンテナーに接続できます。 SSH を使用してコンテナーに簡単に接続できるように、Azure Functions には、SSH が既に有効になっている基本イメージが用意されています。 必要な作業は、Dockerfile を編集してから、イメージをリビルドして再デプロイするだけです。 その後、[高度なツール (Kudu)] を使用してコンテナーに接続することができます。

1. Dockerfile で、`FROM` 命令の基本イメージに文字列 `-appservice` を追加します。

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
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

    基本イメージ間の違いは、[App Services のカスタム Docker イメージに関するチュートリアル](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)で説明されています。

1. もう一度 `docker build` コマンドを使用してイメージをリビルドします。`<docker_id>` は、実際の Docker ID に置き換えてください。

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 更新済みのイメージを Docker Hub にプッシュします。アップロードする必要があるのは、イメージの更新済みセグメントのみであるため、初回プッシュ時よりもはるかに短時間で済みます。

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions によってイメージが自動的に関数アプリに再デプロイされます。この処理の所要時間は 1 分未満です。

1. ブラウザーで `https://<app_name>.scm.azurewebsites.net/` を開きます。`<app_name>` は、一意の名前に置き換えてください。 この URL は、関数アプリ コンテナーの [高度なツール (Kudu)] エンドポイントです。

1. 自分の Azure アカウントにサインインし、 **[SSH]** を選択して、コンテナーとの接続を確立します。 Azure によるコンテナー イメージの更新処理が進行中である場合、接続に少し時間がかかる場合があります。

1. コンテナーとの接続が確立されたら、`top` コマンドを実行して、現在実行中のプロセスを表示します。 

    ![SSH セッションで実行されている Linux top コマンド](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Azure Storage キューに書き込む

Azure Functions を使用すると、独自の統合コードを記述しなくても、他の Azure サービスやリソースに関数を接続できます。 これらの*バインド*は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

このセクションでは、関数と Azure Storage キューを統合する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

## <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage の接続文字列を取得する

先ほど、関数アプリで使用する Azure ストレージ アカウントを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 関数をローカルで実行するときは、*local.settings.json* ファイルに設定をダウンロードすることにより、その接続を使用して同じアカウントのストレージ キューへの書き込みを行うことができます。 

1. プロジェクトのルートから、次のコマンドを実行します。`<app_name>` は、前のクイックスタートの関数アプリ名に置き換えてください。 このコマンドを実行すると、ファイル内の既存の値はすべて上書きされます。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.json* を開き、`AzureWebJobsStorage` という名前の値を検索します。それがストレージ アカウントの接続文字列です。 `AzureWebJobsStorage` という名前と接続文字列は、この記事の他のセクションで使用します。

> [!IMPORTANT]
> *local.settings.json* には、Azure からダウンロードされたシークレットが含まれているため、このファイルは必ずソース管理から除外してください。 ローカル関数プロジェクトで作成される *.gitignore* ファイルからは、このファイルが既定で除外されます。

### <a name="add-an-output-binding-to-functionjson"></a>function.json に出力バインディングを追加する

Azure Functions では、バインドの型ごとに、`direction`、`type`、および固有の `name` が *function.json* ファイル内で定義されている必要があります。 *function.json* には既に、"httpTrigger" 型の入力バインディングと HTTP 応答の出力バインディングが含まれています。 ストレージ キューへのバインディングを追加するには、このファイルに変更を加えます。具体的には、"queue" 型の出力バインディングを追加します。これでコードに、キューが `msg` という名前の入力引数として現れます。 キュー バインディングには、使用するキューの名前 (このケースでは `outqueue`) と、接続文字列を保持する設定の名前 (このケースでは `AzureWebJobStorage`) が必要となります。

::: zone pivot="programming-language-csharp"

C# クラス ライブラリ プロジェクトでは、バインドは関数メソッドのバインド属性として定義されます。 それらの属性に基づいて、*function.json* ファイルが自動的に生成されます。

1. キュー バインディング用に、次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージを自分のプロジェクトに追加します。

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. *HttpTrigger.cs* ファイルを開き、次の `using` ステートメントを追加します。

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. 次のパラメーターを `Run` メソッドの定義に追加します。
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    `Run` メソッドの定義は、次のコードのようになります。
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

`msg` パラメーターは `ICollector<T>` 型です。これは、関数の完了時に出力バインドに書き込まれるメッセージのコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`StorageAccountAttribute` で設定されます。 この属性は、ストレージ アカウントの接続文字列を含む設定を示し、クラス、メソッド、パラメーター レベルで適用できます。 この例では、既定のストレージ アカウントを既に使用しているため、`StorageAccountAttribute` を省略してもかまいません。

::: zone-end

::: zone pivot="programming-language-javascript"

*function.json* を次のように更新します。HTTP バインディングの後にキュー バインディングを追加しましょう。

```json
{
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
      "name": "res"
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
::: zone-end

::: zone pivot="programming-language-powershell"

*function.json* を次のように更新します。HTTP バインディングの後にキュー バインディングを追加しましょう。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
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
::: zone-end

::: zone pivot="programming-language-python"

*function.json* を次のように更新します。HTTP バインディングの後にキュー バインディングを追加しましょう。

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
::: zone-end

::: zone pivot="programming-language-typescript"

*function.json* を次のように更新します。HTTP バインディングの後にキュー バインディングを追加しましょう。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
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
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>出力バインディングを使用するコードを追加する

バインディングを定義すると、そのバインディングの名前 (このケースでは `msg`) が関数コードに引数として (JavaScript と TypeScript では `context` オブジェクトに) 現れます。 その変数を使用して、メッセージをキューに書き込むことができます。 認証、キューの参照の取得、データの書き込みのためのコードは自分で記述する必要があります。 これらの統合タスクはすべて、Azure Functions ランタイムとキュー出力バインディングで処理され、手間がかかりません。

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
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
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>レジストリ内のイメージを更新する

1. ルート フォルダーで `docker build` コマンドを再度実行します。今回は、タグ内のバージョンを `v1.0.1` に更新します。 以前と同様に、`<docker_id>` を Docker Hub アカウント ID に置き換えてください。

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. `docker push` を使用して、更新済みのイメージをリポジトリにプッシュして戻します。

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 継続的デリバリーを構成したため、レジストリにあるイメージを再度更新すると、Azure にある関数アプリが自動的に更新されます。

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage キューのメッセージを確認する

ブラウザーで、これまでと同じ URL を使用して自分の関数を呼び出します。 以前と同じ応答がブラウザーに表示されるはずです。その部分については、関数コードに変更を加えていないためです。 ただし追加したコードでは、URL パラメーター `name` を使用して、`outqueue` ストレージ キューにメッセージを書き込みました。

キューは、[Azure portal](../storage/queues/storage-quickstart-queues-portal.md) または [Microsoft Azure Storage Explorer](https://storageexplorer.com/) で確認できます。 次の手順に従って、Azure CLI でキューを確認することもできます。

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
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
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
