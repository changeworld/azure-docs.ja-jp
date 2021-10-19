---
title: 'クイックスタート: Azure Arc で関数アプリを作成する'
description: 最初の関数アプリをデプロイすることによって、Azure Arc で Azure Functions の使用を開始します。
ms.topic: quickstart
ms.date: 05/10/2021
ms.openlocfilehash: 4fd5a7a2792e6e90748c60336d92d5ed80388267
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706999"
---
# <a name="create-your-first-function-on-azure-arc-preview"></a>Azure Arc で初めての関数を作成する (プレビュー)

このクイックスタートでは、Azure Functions プロジェクトを作成し、[Azure Arc 対応 Kubernetes クラスター](../azure-arc/kubernetes/overview.md)で実行される関数アプリにデプロイします。 詳細については、「[Azure Arc の App Service、Functions、および Logic Apps](../app-service/overview-arc-integration.md)」を参照してください。このシナリオでは、Linux で実行されている関数アプリのみがサポートされます。   

> [!NOTE]
> Azure Arc 対応 Kubernetes クラスターでの関数の実行は、現在プレビュー段階です。  
>  
> 現在、Azure Arc 対応 Kubernetes クラスターに PowerShell 関数プロジェクトを発行する操作はサポートされていません。 Azure Arc 対応 Kubernetes クラスターに PowerShell 関数をデプロイする必要がある場合は、[コンテナー内に関数アプリを作成](create-first-function-arc-custom-container.md)します。 

## <a name="prerequisites"></a>前提条件

ローカル コンピューター上:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) バージョン 12。 Node.js バージョン 10 もサポートされます。
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions でサポートされているバージョンの Python](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3245 以降。
+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する 1 つまたは複数の個別の関数に対する、デプロイと実行のユニットです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ *LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python には仮想環境が必要で、そのためのコマンドは、bash と Windows のコマンド ラインとで異なります。
    
     + bash: 

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
    func init LocalFunctionProj --python
    ```

    ---

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-develop-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 既定では、*local.settings.json* ファイルは *.gitignore* ファイルのソース管理から除外されます。 この除外は、Azure からダウンロードされたシークレットがファイルに含まれている可能性があるためです。

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Azure リソースを作成する 

新しい App Service Kubernetes 環境に関数コードをデプロイする前に、次の 2 つのリソースをさらに作成する必要があります。

- [ストレージ アカウント](../storage/common/storage-account-create.md)。現在、ツールに必要であり、環境には含まれていません。
- 関数アプリ。関数コードを実行するためのコンテキストとなります。 関数アプリは App Service Kubernetes 環境で実行され、ローカル関数プロジェクトにマップされます。 関数アプリを使用すると、リソースの管理、デプロイ、および共有を容易にするための論理ユニットとして関数をグループ化できます。

> [!NOTE]
> 関数アプリは、専用の (App Service) プランの App Service Kubernetes 環境で実行されます。 既存のプランを使用せずに関数アプリを作成すると、適切なプランが自動的に作成されます。  

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
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet 
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8
```
---

この例では、`<CUSTOM_LOCATION_ID>` を、App Service Kubernetes 環境に対して決定したカスタムの場所の ID に置き換えます。 また、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に置き換え、`<APP_NAME>` をグローバルに一意の適切な名前に置き換えます。 

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

Azure Arc 対応 Kubernetes クラスターで完全なデプロイが完了するには、時間がかかる場合があります。このため、次のコマンドを再び実行して、発行された関数を確認することをお勧めします。

```command
func azure functionapp list-functions
``` 

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
