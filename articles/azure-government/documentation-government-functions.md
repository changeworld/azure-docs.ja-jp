---
title: "Azure 関数の政府の |Microsoft ドキュメント"
description: "これにより Azure 政府機関向けに関数を使用する、一連のクイック スタート"
services: azure-government
cloud: gov
documentationcenter: 
author: yujhongmicrosoft
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 9/6/2017
ms.author: yujhong
ms.openlocfilehash: 1000c686e1c0bd400d81400de698df7645178216
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="azure-functions-on-azure-government"></a>Azure 政府機関向けの azure 関数
一連のクイック スタートの下に役立つ Azure 政府機関向けの Azure 関数の使用を開始します。 Azure 政府機関向けの Azure 関数を使用で Azure の商用プラットフォームで使用すると似ています、[いくつかの例外](documentation-government-compute.md#azure-functions)です。

Azure の機能についての詳細を表示をクリックして[ここ](../azure-functions/functions-overview.md)です。 
## <a name="create-function---azure-cli"></a>関数の作成 - Azure CLI

### <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

+ アクティブな [GitHub](https://github.com) アカウント。 
+ アクティブな Azure 政府のサブスクリプション。
Azure 政府サブスクリプションを持っていない場合は、作成、[アカウントを無料](https://azure.microsoft.com/overview/clouds/government/)開始する前にします。

+ インストールされている[CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)です。 

> [!NOTE]
> このトピックでは、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。 
>
>

### <a name="launch-azure-cli-20"></a>Azure CLI 2.0 を起動します。

まずに従って Azure 政府機関向け Azure コマンド ライン インターフェイス (CLI) とに接続する必要があります[手順](documentation-government-get-started-connect-with-cli.md)です。 

CLI を通じて Azure 政府機関に接続するには、クラウドを設定します。

```azurecli-interactive
az cloud set --name AzureUSGovernment
```

クラウドを設定すると後でのログ記録を続行することができます。

```azurecli-interactive
az login
```
### <a name="create-a-resource-group-with-cli"></a>CLI でリソース グループを作成します。

> [!NOTE]
> 既存のリソース グループを使用する場合は、このセクションを省略できます。 使用してリソース グループを作成することも、 [Azure 政府機関向けポータル](https://portal.azure.us)CLI を使用する代わりにします。 
> 
> 

[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_create) でリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例は、という名前のリソース グループを作成`myResourceGroup`設定された場所で`usgovvirginia`です。
CLI を使用していない場合を使用して最初のサインイン、`az login`上に示したです。

```azurecli-interactive
az group create --name myResourceGroup --location usgovvirginia
```
### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

> [!NOTE]
> 既存のストレージ アカウントを使用する場合は、このセクションを省略できます。 使用してストレージ アカウントを作成することも、 [Azure 政府機関向けポータル](https://portal.azure.us)CLI を使用する代わりにします。 
> 
> 

Functions は、関数に関する状態その他の情報を維持するために Azure Storage アカウントを使用します。 [az storage account create](../storage/common/storage-azure-cli.md#create-a-new-storage-account) コマンドを使用して作成したリソース グループ内にストレージ アカウントを作成します。

次のコマンドでは、`<storage_name>` プレースホルダーをグローバルに一意なストレージ アカウント名で置き換えます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。

```azurecli-interactive
az storage account create --name <storage_name> --location usgovvirginia --resource-group myResourceGroup --sku Standard_LRS
```

ストレージ アカウントの作成後に、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "usgovvirginia",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.usgovcloudapi.net/",
    "file": "https://myfunctionappstorage.file.core.usgovcloudapi.net/",
    "queue": "https://myfunctionappstorage.queue.core.usgovcloudapi.net/",
    "table": "https://myfunctionappstorage.table.core.usgovcloudapi.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
### <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App は、関数コードのサーバーレス実行の環境を提供します。 これにより、管理が簡単に、展開、およびリソースの共有論理ユニットとして関数をグループ化します。 Function App の作成には、[az functionapp create](https://docs.microsoft.com/cli/azure/functionapp?view=azure-cli-latest#az_functionapp_create) コマンドを使用します。

関数アプリを作成する前に作成する必要があります、 [App Service プラン](../azure-functions/functions-scale.md#app-service-plan)関数アプリをホストします。 これは、"testPlan"をという名前のプランを作成する次のコマンドで実行できます。

```azurecli-interactive
az appservice plan create --resource-group myResourceGroup --name testPlan
```

次のコマンドで表示されている固有の関数アプリ名に置き換えて、`<app_name>`プレース ホルダーとストレージ アカウント名を`<storage_name>`です。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan testPlan
```
>[!NOTE] 
>既定では、App Service プランは、Vm を専用の方法は、App Service アプリに割り当てられ、関数ホストが常に実行されていると、関数のアプリが作成されます。
App Service プランの詳細については[ここをクリックして](../azure-functions/functions-scale.md#app-service-plan)です。 
>
>

Function App が作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.us",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.us",
    "quickstart.scm.azurewebsites.us"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

関数アプリがある場合は、これから実際の関数のコードを展開することができます、[サンプルの GitHub リポジトリ](https://github.com/Azure-Samples/functions-quickstart)です。

### <a name="deploy-your-function-code"></a>関数コードをデプロイする
 接続するこのクイック スタート、[サンプル GitHub リポジトリ](https://github.com/Azure-Samples/functions-quickstart)関数のサンプルを格納しています。 前回と同様、次のコードで `<app_name>` プレースホルダーを作成済みの Function App の名前に置き換えます。 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
デプロイ ソースの設定後に、次の例のような情報が Azure CLI に表示されます (読みやすくするために null 値は削除してあります)。
```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "USGov Virginia",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```
### <a name="test-the-function"></a>関数をテストする

CURL を使用して、Mac または Linux コンピューターに展開された関数をテストするか、バッシュを使用して、Windows では、ことができます[インストール](https://msdn.microsoft.com/commandline/wsl/install_guide)です。  

次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```bash
curl http://<app_name>.azurewebsites.us/api/HttpTriggerJS1?name=<yourname>
```
 
![関数の応答をブラウザーに表示されます。](./media/documentation-government-function2.png)  

コマンド ラインで使用可能な cURL がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを関数アプリの名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。 

    http://<app_name>.azurewebsites.us/api/HttpTriggerJS1?name=<yourname>

![ブラウザーに表示された関数の応答。](./media/documentation-government-function3.png)  

## <a name="create-function---visual-studio"></a>関数の作成 - Visual Studio 

を開始する前にまずことを確認して、Visual Studio がである[Azure 政府環境に接続して](documentation-government-get-started-connect-with-vs.md)です。 

確認が、Azure 政府機関向けに Visual Studio を使用して関数を作成する手順は基本的に、作成方法と同じ Azure のいずれかの商用は[ここ](../azure-functions/functions-create-your-first-function-visual-studio.md)です。 

### <a name="test-your-function-in-azure-government"></a>Azure 政府機関向けで、機能をテストします。 

関数は上記の前の手順で展開した後、関数をテストします。 HTTP によってトリガーされる関数を呼び出す URL は、次のようになります。

        http://<functionappname>.azurewebsites.us/api/<functionname>?name=<yourname> 

HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 次の図は、関数によって返されるリモートの GET 要求をブラウザーで応答を示しています。 

   ![ブラウザーでの関数の応答](./media/documentation-government-functions-createvs.png)

## <a name="create-trigger-functions---visual-studio"></a>トリガー関数 - Visual Studio を作成します。
Visual Studio を使用して Azure 政府でトリガー関数を作成する方法を説明します。 

### <a name="prerequisites"></a>前提条件

* Visual Studio がインストールされていることを確認します。
    -   [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。
    
    >[!NOTE] 
    > インストールまたは Visual Studio 2017 15.3 のバージョンにアップグレードした後、Azure 関数の Visual Studio 2017 ツールを手動で更新する必要もあります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** >          **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 
    >
    >
* 関数アプリの Visual Studio で実行されています。
    - 1 つを作成するには、上記のクイック スタート セクションを完了[Visual Studio を使用して関数を作成する](documentation-government-functions.md#create-function-using-visual-studio)です。 
* インストールされている[CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)です。 

> [!NOTE]
> このトピックでは、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。 
>
>

### <a name="create-trigger-function"></a>トリガー関数を作成します。

1. Visual Studio の関数のアプリを開き、ソリューションではなく、アプリ自体を右クリックします。 [追加] ボタン移動し、「新しい項目の」次のようにをクリックします。
    
    ![triggerfunctioncreate](./media/documentation-government-function4.png)
2. "Azure Function"をクリックし、この画面が表示されます。
    
    ![triggerfunctioncreate1](./media/documentation-government-function5.png)
    
    わかるようには、タイマー、汎用 GitHub Webhook、Blob などを作成する、キューを関数がトリガーの複数の種類があります。 このチュートリアルのために、タイマー トリガー関数を作成します。 
    
3. タイマー トリガーをクリックし、作成して、Visual Studio で新しい関数を表示することができます。 
4. 右側の ソリューション エクスプ ローラーを参照してくださいして"local.settings.json"ファイルを開くことができます。

    ![triggerfunctioncreate2](./media/documentation-government-function6.png)

    トリガー関数を Azure 政府機関に接続するためには、このファイルで定義されている接続プロパティ アプリ設定を定義する必要があります。 次の手順ではこの値がわかります。 
5. 移動して、 [Azure 政府機関向けポータル](https://portal.azure.us)「ストレージ アカウント」で、左側のサイド バーからをクリックします。 

    ![triggerfunctioncreate3](./media/documentation-government-function7.png)
    
    この関数のアプリに対応するストレージ アカウントをクリックし、「設定」、「アクセス キー」セクションが表示されます。
    このセクションに移動した後は、次の 2 つの既定のキーを表示することができます。

    ![triggerfunctioncreate4](./media/documentation-government-function8.png)
6. Key1 の接続文字列のコピー"local.settings.json"ファイルに戻って、"Values"パラメーターに 3 つの値の各文字列を貼り付けます。 確認して"AccountName"を行うことも、ストレージ アカウント名を = です。 

    ![triggerfunctioncreate5](./media/documentation-government-function9.png)
    
7. CLI を通じて実行されます。 このアプリケーションの設定に、Azure ストレージ アカウントに接続文字列ようになりました設定する必要があります。 
    >[!NOTE] 
    > CLI は前の「Azure CLI 2.0 を起動する」セクションに従って Azure 政府機関に接続されていることを確認してください。 
    >
    >

    次のコマンドでは、アプリ情報に置き換えます"resourceGroupName"と"FunctionAppname"タグを"connectionString"のタグの代わりに、上記の手順 6. から接続文字列を貼り付けます。 Azure CLI を使用してコマンドを実行します。

```azurecli-interactive
az functionapp config appsettings set --resource-group <resourceGroupName> --name <FunctionAppname> --settings AzureWebJobsmyconnection= "<connectionString>"

```

これが設定されるとことができます、タイマー トリガー関数ローカルで実行します。 Azure 政府機関、展開するためにプロセスを使用して、同じように定義されている[上](documentation-government-functions.md#create-function-using-visual-studio)です。

## <a name="use-azure-queues-for-output-bindings"></a>出力バインドに Azure キューを使用します。

このチュートリアルは、接続し、Visual Studio で、関数から、Azure キュー ストレージにメッセージを送信する出力バインドを追加して既存の関数を更新する方法を説明します。 

### <a name="prerequisites"></a>前提条件

* Visual Studio がインストールされていることを確認します。

   -   [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。
    
    >[!NOTE] 
    > インストールまたは Visual Studio 2017 15.3 のバージョンにアップグレードした後、Azure 関数の Visual Studio 2017 ツールを手動で更新する必要もあります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** >          **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 
    >
    >

* このチュートリアルでは、関数の実行中のアプリが必要です。 1 つないと、前のクイック スタート セクション「関数 Visual Studio の作成」というタイトルが行うことができます。 
* 完全な**上の「トリガー関数を作成する」手順から手順 4 ~ 7 です。** 
* このチュートリアルは、できるように、次のいずれかを作成していない場合も、Azure キューを必要[手順](../storage/queues/storage-dotnet-how-to-use-queues.md)です。

### <a name="update-the-function-code"></a>関数コードを更新する 
関数を出力に接続するために、キューの出力バインドを作成する必要があります。 
1. Visual Studio で、関数を開きます
2.  C# の場合、関数の出力を追加するには、次のように、関数定義を更新キュー ストレージのバインド パラメーター (置換、<QueueName>キューのプレース ホルダーを出力の名前)。 JavaScript 関数の場合は、この手順をスキップします。
    
    ```csharp
    public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req,TraceWriter log,[Queue("<QueueName>", Connection = "myconnection")] ICollector<string> <QueueName>)
    {
    …
    }
            
    ```
3. 出力に書き込むためにメソッドが戻る前にのみ、関数に次のコードを追加キュー。 関数の言語に合ったスニペットを使用します。

    ```javascript
    context.bindings.<QueueName> = "Name passed to the function: " + (req.query.name || req.body.name);
    ```
    
    ```cs
    <QueueName>.Add("Name passed to the function: " + name);     
    ```
    
### <a name="test-your-function"></a>関数をテストする

1. Visual Studio で、関数を実行します。
2. Azure 政府に移動[ポータル](https://portal.azure.us)[左側のメニューからストレージ アカウント] をクリックします。 
3. [概要] ページは異なるキューを確認するために「キュー」ボタンをクリックします。 

    ![triggerfunctioncreate6](./media/documentation-government-function10.png)
4. [キュー] をクリックし、関数の出力を表示することができます。

    ![triggerfunctioncreate7](./media/documentation-government-function11.png)
5. ローカルでのバインドの出力をテストすることは、同じ手順で学習した展開用を通過、[前のセクション](documentation-government-functions.md#create-function-using-visual-studio)です。 

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成されたすべてのリソースを削除するのにには、次のコマンドを使用します。

```azurecli-interactive
az group delete --name myResourceGroup
```
確認を求められたら「`y`」と入力します。

## <a name="next-steps"></a>次の手順
* サブスクライブ、 [Azure 政府機関向けブログ](https://blogs.msdn.microsoft.com/azuregov/)
* 使用して、スタック オーバーフローに関するヘルプを表示、"[azure gov](https://stackoverflow.com/questions/tagged/azure-gov)"タグ
* フィードバックのお願いまたは経由での新機能を要求、 [Azure 政府フィードバック フォーラム](https://feedback.azure.com/forums/558487-azure-government)
