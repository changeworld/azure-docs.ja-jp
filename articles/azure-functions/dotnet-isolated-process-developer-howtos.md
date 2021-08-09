---
title: Azure Functions を使用した .NET 5 関数の開発と発行
description: .NET 5.0 を使用して C# 関数を作成およびデバッグしてから、Azure Functions でサーバーレス ホスティングにローカル プロジェクトをデプロイする方法について説明します。
ms.date: 05/03/2021
ms.topic: how-to
recommendations: false
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 6521c02686da55142d9a9d9f1faf569d584ef593
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111590004"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Azure Functions を使用した .NET 5 関数の開発と発行 

この記事では、.NET 5.0 を使用して C# 関数を操作する方法について説明します。この C# 関数は、Azure Functions ランタイムからプロセス外で実行されます。 これらの .NET 分離プロセス関数を作成してローカルでデバッグし、Azure に発行する方法について説明します。 Azure では、これらの関数は、.NET 5.0 をサポートする分離プロセスで実行されます。 詳細については、「[Azure において関数を .NET 5.0 で実行するためのガイド](dotnet-isolated-process-guide.md)」を参照してください。

.NET 5.0 をサポートしたり関数をアウトプロセスで実行したりする必要がない場合は、代わりに [C# クラス ライブラリ関数を作成](functions-create-your-first-function-visual-studio.md)することをお勧めします。

## <a name="prerequisites"></a>前提条件

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="development-environment-vscode,development-environment-cli"  
+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3381 以降のバージョン。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.20 移行のバージョン。  
::: zone-end  
::: zone pivot="development-environment-vscode"  
+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。  

+ Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)バージョン 1.3.0 以降。
::: zone-end  
::: zone pivot="development-environment-vs"  
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) バージョン 16.10 以降。 インストールには、**Azure 開発** または **ASP.NET および Web 開発** のいずれかのワークロードを含める必要 があります。  
::: zone-end  

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

::: zone pivot="development-environment-vscode"  
1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`C#`] を選択します。

    + **.NET ランタイムを選択してください**: [`.NET 5 isolated`] を選択します。

    + **Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)** : [`HTTP trigger`] を選択します。

    + **Provide a function name (関数名を指定してください):** 「`HttpExample`.

    + **Provide a namespace (名前空間を指定してください)** : 「`My.Functions`.

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

1. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。
::: zone-end  
::: zone pivot="development-environment-cli"  

1. 次のように `func init` コマンドを実行して、*LocalFunctionProj* という名前のフォルダーに関数プロジェクトを作成します。  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    `dotnetisolated` を指定すると、.NET 5.0 で実行するプロジェクトが作成されます。


1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) などの構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

1. 次のコマンドを使用して、関数をプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前 (HttpExample) で、`--template` 引数は関数のトリガー (HTTP) を指定します。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` によって、HttpExample.cs コード ファイルが作成されます。
::: zone-end  

::: zone pivot="development-environment-vs"

1. Visual Studio メニューで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** の検索ボックスに「*functions*」と入力し、**Azure Functions** テンプレートを選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトの **プロジェクト名** を入力し、 **[作成]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[新しい Azure Functions アプリケーションの作成]** 設定で、次の表の値を使用します。

    | 設定      | 値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET のバージョン** | **.NET 5 (Isolated)** | この値により、分離プロセスで .NET 5.0 上で実行される関数プロジェクトが作成されます。   |
    | **関数テンプレート** | **HTTP トリガー** | この値は、HTTP 要求によってトリガーされる関数を作成します。 |
    | **ストレージ アカウント (AzureWebJobsStorage)**  | **ストレージ エミュレーター** | Azure の関数アプリにはストレージ アカウントが必要であるため、プロジェクトを Azure に発行する際に割り当てられるか、作成されます。 HTTP トリガーによって、Azure Storage アカウントの接続文字列が使用されることはありません。その他のすべてのトリガーの種類には、有効な Azure Storage アカウントの接続文字列が必要です。  |
    | **承認レベル** | **Anonymous** | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」と [HTTP と Webhook のバインド](functions-bindings-http-webhook.md)に関するページをご覧ください。 |
    
    
    ![Azure Functions プロジェクトの設定](./media/dotnet-isolated-process-developer-howtos/functions-project-settings.png)

    **[承認レベル]** を **[匿名]** に設定していることを確認します。 **関数** の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](functions-bindings-http-webhook-trigger.md#authorization-keys)を提示する必要があります。

1. **[作成]** を選択して、関数プロジェクトと HTTP トリガー関数を作成します。

Visual Studio によってプロジェクトとクラスが作成されます。クラスの中には、HTTP トリガー関数型のスケルトン コードが含まれています。 定型コードは、"Welcome to Azure Functions!" を送信します HTTP 応答。 `HttpTrigger`属性は、関数が HTTP 要求によってトリガーされることを指定します。 

## <a name="rename-the-function"></a>フォルダーの名前を変更する

`FunctionName` メソッド属性は、関数の名前を設定します。これは、既定では `Function1` として生成されます。 このツールでは、プロジェクトを作成するときに既定の関数名をオーバーライドすることはできないため、ここで関数クラス、ファイル、およびメタデータに対してより適切な名前を指定します。

1. **エクスプローラー** で Function1.cs ファイルを右クリックし、`HttpExample.cs` という名前に変更します。

1. コードで、Function1 クラスの名前を `HttpExample` に変更します。

1. `Run` という名前 `HttpTrigger` メソッドで、`FunctionName` メソッド属性の名前を `HttpExample`、および `GetLogger` メソッドに渡された値に変更します。

関数の定義は次のコードのようになります。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs" range="9-15":::
 
関数の名前の変更が済んだので、この関数をローカル コンピューターでテストできるようになりました。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

完全な Azure Functions ランタイムを使用してローカルで関数をテストできるように、Visual Studio は Azure Functions Core Tools と統合されます。  

1. 関数を実行するには、Visual Studio で <kbd>F5</kbd> キーを押します。 ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合があります。 承認レベルは、ローカルで関数を実行するときには適用されません。

1. Azure Functions のランタイムの出力から、関数の URL をコピーし、要求を実行します。 関数が正常に実行され、ログがランタイム出力に書き込まれると、Functions へようこそメッセージが表示されます。 

1. デバッグを停止するには、Visual Studio で <kbd>Shift</kbd>+<kbd>F5</kbd> キーを押します。

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

::: zone-end

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。

- [リソース グループ](../azure-resource-manager/management/overview.md)。関連リソースの論理コンテナーです。
- [ストレージ アカウント](../storage/common/storage-account-create.md)。関数についての情報 (状態など) を維持する目的で使用されます。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) コマンドで Azure アカウントにサインインします。

1. `westeurope` リージョンに `AzureFunctionsQuickstart-rg` という名前のリソース グループを作成します。

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドでストレージ アカウントを作成します。 

    前の例の `<STORAGE_NAME>` は、適宜、Azure Storage 内で一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` は汎用アカウントを指定します。これは [Functions でサポート](storage-considerations.md#storage-account-requirements)されています。

1. Azure に関数アプリを作成します。
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) コマンドで Azure に関数アプリを作成します。 
    
    前の例では、`<STORAGE_NAME>` を前の手順で使用したアカウントの名前に、`<APP_NAME>` を適宜グローバルに一意の名前に置き換えてください。 `<APP_NAME>` は、関数アプリの既定の DNS ドメインでもあります。 
    
    このコマンドでは、[Azure Functions 従量課金プラン](consumption-plan.md)で .NET 5.0 を実行する関数アプリが作成されます。 このプランでは、この記事で発生する使用が無料になります。 また、関連付けられた Azure Application Insights インスタンスも同じリソース グループ内にプロビジョニングされます。 このインスタンスを使用して関数アプリを監視し、ログを表示します。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

:::zone pivot="development-environment-vs"

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリが存在する必要があります。 初めてプロジェクトを発行するときに、Visual Studio の発行機能によって、関数アプリが自動的に作成されます。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Azure 内で関数を確認する

1. Cloud Explorer では、新しい関数アプリが選択されているはずです。 そうでない場合は、自分のサブスクリプション > **[App Services]** の順に展開して、新しい関数アプリを選択します。

1. 関数アプリを右クリックし、 **[ブラウザーで開く]** を選択します。 これにより、関数アプリのルートが既定の Web ブラウザーで開かれ、関数アプリが実行されていることを示すページが表示されます。 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="実行されている関数アプリ":::

1. ブラウザーのアドレス バー内で、ベース URL にパス `/api/HttpExample` を追加し、要求を実行します。

1. この URL にアクセスすると、ローカルでの実行時と同じ応答がブラウザー内に表示されます。

:::zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。


1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    - **Select folder (フォルダーを選択してください)** : ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。 既に有効な関数アプリが開いているときは、このプロンプトは表示されません。

    - **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみのときは、このプロンプトは表示されません。

    - **Select function app in Azure (Azure で関数アプリを選択してください)** : [`- Create new Function App`] を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)
      
    - **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。
    
    - **ランタイム スタックを選択してください**: [`.NET 5 (non-LTS)`] を選択します。 
    
    - **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。 
    
    通知領域には、Azure で作成された個々のリソースの状態が表示されます。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure リソース作成の通知":::
    
1.  完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事を完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 

::: zone pivot="development-environment-cli"  
追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [.NET 分離関数の詳細情報](dotnet-isolated-process-guide.md)

