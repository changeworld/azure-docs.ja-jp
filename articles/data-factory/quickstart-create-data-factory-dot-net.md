---
title: .NET を使用して Azure データ ファクトリを作成する | Microsoft Docs
description: Azure データ ファクトリを作成して、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 3d1d77e585ae8d608a8f9a4e3de0943315d897af
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41919960"
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>.NET SDK を使用してデータ ファクトリとパイプラインを作成する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-dot-net.md)

このクイックスタートでは、.NET SDK を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure BLOB ストレージ内のあるフォルダーから別のフォルダーに**コピー**します。 Azure Data Factory を使用してデータを**変換**する方法のチュートリアルについては、[Spark を使用したデータ変換のチュートリアル](transform-data-using-spark.md)を参照してください。 

> [!NOTE]
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**ロールまたは**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にある**ユーザー名**をクリックし、**[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加するサンプル手順については、[ロールの追加](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

### <a name="azure-storage-account"></a>Azure Storage アカウント
このクイックスタートでは、**ソース** データ ストアと**コピー先**データ ストアの両方に汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」をご覧ください。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このクイックスタートでは、Azure Storage アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーを起動して [Azure Portal](https://portal.azure.com) にアクセスします。 Azure のユーザー名とパスワードを使用してログインします。 
2. 左側のメニューの **[その他のサービス >]** をクリックし、"**ストレージ**" というキーワードでフィルター処理して、**[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、**該当するストレージ アカウント**を選択します。 
4. **[ストレージ アカウント]** ページのメニューから **[アクセス キー]** を選択します。

    ![ストレージ アカウントの名前とキーを取得](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. **[ストレージ アカウント名]** フィールドと **[Key1]** フィールドの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。  

#### <a name="create-input-folder-and-files"></a>入力フォルダーとファイルの作成
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure BLOB ストレージに作成します。 次に、そのコンテナーに **input** という名前のフォルダーを作成し、input フォルダーにサンプル ファイルをアップロードします。 

1. **[ストレージ アカウント]** ページで **[概要]** に切り替え、**[BLOB]** をクリックします。 

    ![BLOB オプションを選択する](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. **[Blob service]** ページのツール バーで、**[+ コンテナー]** をクリックします。 

    ![コンテナーの追加ボタン](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、**[OK]** をクリックします。 

    ![コンテナー名を入力する](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. コンテナーの一覧で、**[adftutorial]** をクリックします。 

    ![コンテナーを選択する](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. **[コンテナー]** ページのツール バーで、**[アップロード]** をクリックします。  

    ![[アップロード] ボタン](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. **[BLOB のアップロード]** ページで、**[詳細設定]** をクリックします。

    ![詳細設定リンクをクリックする](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. **メモ帳**を開き、以下の内容を含む **emp.txt** という名前のファイルを作成します。それを **c:\ADFv2QuickStartPSH** フォルダーに保存します。**ADFv2QuickStartPSH** フォルダーがない場合は作成します。
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal の **[BLOB のアップロード]** ページの **[ファイル]** フィールドで、**emp.txt** ファイルを探して選択します。 
9. **[アップロード先のフォルダー]** フィールドの値として、「**input**」と入力します。 

    ![BLOB のアップロードの設定](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. フォルダーが **input** で、ファイルが **emp.txt** であることを確認し、**[アップロード]** をクリックします。
11. 一覧に **emp.txt** ファイルとアップロードの状態が表示されます。 
12. 隅の **[X]** をクリックして、**[BLOB のアップロード]** ページを閉じます。 

    ![BLOB のアップロード ページを閉じる](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. **[コンテナー]** ページを開いたままにしておきます。 このクイックスタートの最後で、このページを使用して出力を確認します。

### <a name="visual-studio"></a>Visual Studio
この記事のチュートリアルでは、Visual Studio 2017 を使用します。 Visual Studio 2013 または 2015 を使用することもできます。

### <a name="azure-net-sdk"></a>Azure .NET SDK
[Azure .NET SDK](http://azure.microsoft.com/downloads/) をマシンにダウンロードしてインストールします。

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory にアプリケーションを作成する
[こちらの記事](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)のセクションの手順に従って、以下のタスクを行ってください。 

1. **Azure Active Directory アプリケーションを作成します**。 このチュートリアルで作成している .NET アプリケーションを表すアプリケーションを Azure Active Directory に作成します。 サインオン URL については、この記事に示されているようにダミーの URL (`https://contoso.org/exampleapp`) を指定できます。
2. **アプリケーション ID** と **認証キー**を取得し、それらの値をメモしておいてください。このチュートリアルで後ほど使用します。 
3. **テナント ID** を取得し、その値をメモしておいてください。このチュートリアルで後ほど使用します。
4. アプリケーションがサブスクリプションにデータ ファクトリを作成できるように、サブスクリプション レベルでアプリケーションを**共同作成者**ロールに割り当てます。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

Visual Studio 2013/2015/2017 を使用して、C# .NET コンソール アプリケーションを作成します。

1. **Visual Studio** を起動します。
2. **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。
3. 右側にあるプロジェクトの種類の一覧で、**[Visual C#]** -> **[コンソール アプリ (.NET Framework)]** の順に選択します。 .NET バージョン 4.5.2 以降が必要です。
4. 名前として「**ADFv2QuickStart**」と入力します。
5. **[OK]** をクリックしてプロジェクトを作成します。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

1. **[ツール]** -> **[NuGet パッケージ マネージャー]** -> **[パッケージ マネージャー コンソール]** の順にクリックします。
2. **パッケージ マネージャー コンソール**で、次のコマンドを実行してパッケージをインストールします。

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>データ ファクトリ クライアントを作成する

1. **Program.cs** を開き、次のステートメントを挿入して、名前空間への参照を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 変数を設定する次のコードを **Main** メソッドに追加します。 プレースホルダーを独自の値に置き換えます。 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. **DataFactoryManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成します。 また、このオブジェクトを使用して、パイプラインの実行の詳細を監視します。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

**データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

**Azure Storage のリンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、コピー ソースとシンク ストアの両方のために、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、サンプルでは "AzureStorageLinkedService" という名前です。

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>データセットを作成する

**Azure BLOB データセット**を作成する次のコードを **Main** メソッドに追加します。

ソースからシンクにコピーするデータを表すデータセットを定義します。 この例のこの BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。 データセットは、データセットを使用するアクティビティで設定された値を持つパラメーターを受け取ります。 パラメーターは、データの存在/格納場所を指す "folderPath" を構築するために使用されます。

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

**コピー アクティビティが含まれているパイプライン**を作成する次のコードを **Main** メソッドに追加します。

この例では、このパイプラインには 1 つのアクティビティが含まれており、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取ります。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを参照します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

**パイプラインの実行をトリガーする**次のコードを **Main** メソッドに追加します。

また、このコードは、パイプラインで指定された **inputPath** と **outputPath** パラメーターの値を、ソースおよびシンクの BLOB パスの実際の値に設定します。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters: parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>パイプラインの実行を監視する

1. データのコピーが完了するまで状態を継続的にチェックするために、次のコードを **Main** メソッドに追加します。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得する次のコードを **Main** メソッドに追加します。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、パイプラインの実行を確認します。

コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 その後、パイプラインの実行状態が確認されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、変数で指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。

### <a name="sample-output"></a>サンプル出力: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure Portal の **adftutorial** コンテナー ページで **[最新の情報に更新]** をクリックして出力フォルダーを表示します。 
    
    ![更新](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. フォルダー一覧の **[output]** をクリックします。 
2. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    ![更新](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
プログラムによって実行するには、データ ファクトリを削除し、次のコード行をプログラムに追加します。 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
