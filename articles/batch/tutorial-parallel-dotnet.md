---
title: 並列ワークロードの実行 - Azure Batch .NET
description: チュートリアル - Batch .NET クライアント ライブラリを使用して、Azure Batch で ffmpeg を使用してメディア ファイルをトランスコードします。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a9772ae9ac346daa205c146263a4632a641ee038
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722815"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>チュートリアル: .NET API を使用して Azure Batch で並列ワークロードを実行する

Azure Batch を使用すると、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のバッチ ジョブを Azure で効率的に実行することができます。 このチュートリアルでは、Batch を使用して並列ワークロードを実行する C# の例を紹介します。 一般的な Batch アプリケーション ワークフローのほか、Batch および Storage のリソースをプログラムで操作する方法を学習します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーション パッケージを Batch アカウントに追加する
> * Batch アカウントおよびストレージ アカウントで認証する
> * Storage に入力ファイルをアップロードする
> * アプリケーションを実行するコンピューティング ノードのプールを作成する
> * 入力ファイルを処理するジョブとタスクを作成する
> * タスクの実行を監視する
> * 出力ファイルを取得する

このチュートリアルでは、[ffmpeg](http://ffmpeg.org/) オープンソース ツールを使用して複数の MP4 メディア ファイルを並行して MP3 形式に変換します。 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017](https://www.visualstudio.com/vs)。 

* Batch アカウントおよびリンクされた Azure ストレージ アカウント。 これらのアカウントを作成するには、[Azure Portal](quick-create-portal.md) または [Azure CLI](quick-create-cli.md) を使用した Batch のクイック スタートを参照してください。

* [Windows 64 ビット版の ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (.zip)。 この ZIP ファイルをローカル コンピューターにダウンロードします。 このチュートリアルで必要なのは、この ZIP ファイルのみです。 ファイルを解凍したり、ローカルにインストールしたりする必要はありません。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。


## <a name="add-an-application-package"></a>アプリケーション パッケージの追加

Azure Portal を使用して、ffmpeg を[アプリケーション パッケージ](batch-application-packages.md)として Batch アカウントに追加します。 アプリケーション パッケージは、タスク アプリケーションと、プール内のコンピューティング ノードへのそのアプリケーションのデプロイを管理する際に役立ちます。 

1. Azure Portal で、**[その他のサービス]** > **[Batch アカウント]** の順にクリックし、Batch アカウントの名前をクリックします。
3. **[アプリケーション]** > **[追加]** の順にクリックします。
4. **[アプリケーション ID]** に「*ffmpeg*」と入力し、パッケージのバージョン「*3.4*」と入力します。 前にダウンロードした ffmpeg の ZIP ファイルを選択し、**[OK]** をクリックします。 ffmpeg アプリケーション パッケージが Batch アカウントに追加されます。

![アプリケーション パッケージの追加](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>サンプルのダウンロードと実行

### <a name="download-the-sample"></a>サンプルのダウンロード

GitHub から[サンプル アプリをダウンロードまたは複製](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)します。 Git クライアントを使用してサンプル アプリ リポジトリを複製するには、次のコマンドを使用します。

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Visual Studio ソリューション ファイル `BatchDotNetFfmpegTutorial.sln` が含まれているディレクトリに移動します。

Visual Studio でソリューション ファイルを開き、`program.cs` 内の資格情報文字列を、お使いのアカウントに関して取得した値で更新します。 例: 

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

また、ソリューション内の ffmpeg アプリケーション パッケージの参照が Batch アカウントにアップロードした ffmpeg パッケージの ID およびバージョンと一致することを確認します。

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>サンプル プロジェクトのビルドと実行

* ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューションのビルド]** をクリックします。 

* メッセージに従って NuGet パッケージの復元を確認します。 不足しているパッケージをダウンロードする必要がある場合は、[NuGet Package Manager](https://docs.nuget.org/consume/installing-nuget) がインストールされていることを確認します。

次に、それを実行します。 サンプル アプリケーションを実行すると、コンソールの出力は次のようになります。 実行中、プールのコンピューティング ノードを開始する際に、`Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` で一時停止が発生します。 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


プール、コンピューティング ノード、ジョブ、タスクを監視するには、Azure Portal で Batch アカウントに移動します。 たとえば、プール内のコンピューティング ノードのヒート マップを確認するには、**[プール]** > *WinFFmpegPool* の順にクリックします。

タスクが実行されていると、ヒート マップは次のようになります。

![プールのヒート マップ](./media/tutorial-parallel-dotnet/pool.png)


既定の構成でアプリケーションを実行する場合、通常の実行時間は約 **10 分間**です。 プールの作成に最も時間がかかります。

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>コードの確認

以降のセクションでは、サンプル アプリケーションを、Batch サービスでワークロードを処理するために実行する複数の手順に分けます。 サンプルのすべてのコード行について説明しているわけではないので、この記事の残りの部分を読む際は Visual Studio で開いているソリューションを参照してください。

### <a name="authenticate-blob-and-batch-clients"></a>BLOB クライアントと Batch クライアントの認証

リンクされているストレージ アカウントを操作するために、アプリでは .NET 用 Azure Storage クライアント ライブラリを使用します。 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) を使用してアカウントへの参照を作成し、共有キー認証を使用して認証します。 その後、[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) を作成します。

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

このアプリは [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) オブジェクトを作成して、Batch サービスでプール、ジョブ、タスクを作成および管理します。 このサンプルの Batch クライアントでは共有キー認証を使用します。 Batch は、個々のユーザーまたは自動アプリケーションを認証するために、[Azure Active Directory](batch-aad-auth.md) による認証もサポートしています。

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>入力ファイルのアップロード

アプリは、`blobClient` オブジェクトを `CreateContainerIfNotExist` メソッドに渡して、入力ファイル (MP4 形式) 用のストレージ コンテナーとタスク出力用のコンテナーを作成します。

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

その後、ファイルは、ローカルの `InputFiles` フォルダーから入力用コンテナーにアップロードされます。 ストレージ内のファイルは、Batch の [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) オブジェクトとして定義されており、Batch が後でコンピューティング ノードにダウンロードできます。 

`Program.cs` 内の 2 つのメソッドは、ファイルのアップロードに関連しています。

* `UploadResourceFilesToContainer`: ResourceFile オブジェクトのコレクションを返し、内部的に `UploadResourceFileToContainer` を呼び出して、`filePaths` パラメーターで渡される各ファイルをアップロードします。
* `UploadResourceFileToContainer`: 各ファイルを BLOB として入力用コンテナーにアップロードします。 ファイルのアップロード後、BLOB の Shared Access Signature (SAS) を取得し、それを表す ResourceFile オブジェクトを返します。 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

.NET を使用してファイルを BLOB としてストレージ アカウントにアップロードする方法の詳細については、「[.NET を使用して Azure Blob Storage を使用する](../storage/blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。

### <a name="create-a-pool-of-compute-nodes"></a>コンピューティング ノードのプールの作成

次に、`CreatePoolIfNotExist` が呼び出されて、コンピューティング ノードのプールが Batch アカウントに作成されます。 この定義済みのメソッドは、[BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) メソッドを使用してノードの数、VM のサイズ、プールの構成を設定します。 ここで、[VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) オブジェクトでは、[ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) に、Azure Marketplace で公開されている Windows Server イメージを指定します。 Batch は、Azure Marketplace のさまざまな VM イメージだけでなく、カスタム VM イメージもサポートしています。

ノードの数と VM のサイズは、定義済みの定数を使用して設定されます。 Batch では専用ノードと[低優先度ノード](batch-low-pri-vms.md)がサポートされているため、ご利用のプールではそのいずれかまたは両方を使用できます。 専用ノードは、プール用に予約されています。 低優先度ノードは、Azure の VM の余剰容量から割引価格で提供されます。 低優先度ノードは、Azure に十分な容量がない場合に使用できなくなります。 このサンプルは、既定で、サイズ *Standard_A1_v2* の低優先度ノードが 5 つだけ含まれているプールを作成します。 

ffmpeg アプリケーションは、プールの構成に [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) を追加することで、コンピューティング ノードにデプロイされます。 

[Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) メソッドは、プールを Batch サービスに送信します。

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>ジョブを作成する

Batch ジョブでは、タスクの実行対象となるプールと、作業の優先順位やスケジュールなどのオプションの設定を指定します。 このサンプルでは、`CreateJobIfNotExist` の呼び出しを使用してジョブを作成します。 この定義済みのメソッドは、[BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) メソッドを使用してプールにジョブを作成します。 

[Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) メソッドは、ジョブを Batch サービスに送信します。 最初、ジョブにはタスクがありません。

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>タスクの作成

このサンプルでは、`AddTasks` メソッドの呼び出しを使用してジョブにタスクを作成します。これにより、[CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) オブジェクトの一覧が作成されます。 各 `CloudTask` は、[CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline) プロパティを使用することで、ffmpeg を実行して入力の `ResourceFile` オブジェクトを処理します。 ffmpeg は、以前にプールが作成されたときに各ノードにインストールされています。 ここでは、コマンド ラインで ffmpeg を実行して、各入力 MP4 (ビデオ) ファイルを MP3 (オーディオ) ファイルに変換します。

このサンプルでは、コマンド ラインの実行後に MP3 ファイルの [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) オブジェクトを作成します。 各タスクの出力ファイル (この場合は 1 つ) は、タスクの [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) プロパティを使用して、リンクされているストレージ アカウントのコンテナーにアップロードされます。

その後、このサンプルは、[AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask) メソッドを使用してジョブにタスクを追加します。これにより、タスクは、コンピューティング ノードで実行するためにキューに登録されます。 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>タスクの監視

Batch によってタスクがジョブに追加されると、関連付けられたプール内のコンピューティング ノードで実行するために自動的にキューに登録され、スケジュールが設定されます。 指定した設定に基づいて、Batch は、タスクのキューへの登録、スケジュール設定、再試行など、タスク管理作業すべてを処理します。 

タスクの実行を監視する方法は多数ありますが、 このサンプルでは、完了と、タスクの失敗または成功の状態のみをレポートするよう `MonitorTasks` メソッドを定義します。 `MonitorTasks` コードでは、[ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) を指定して、タスクについて最小限の情報のみを効率的に選択します。 その後、[TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) を作成します。これは、タスクの状態を監視するためのヘルパー ユーティリティを提供します。 `MonitorTasks` で、サンプルはすべてのタスクが制限時間内に `TaskState.Completed` に達するまで待ちます。 その後、ジョブを終了し、完了してもゼロ以外の終了コードなどのエラーが発生している可能性があるタスクに関するレポートを作成します。

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

タスクの実行後、自動的に、作成された入力用ストレージ コンテナーが削除され、Batch プールとジョブを削除するためのオプションが表示されます。 BatchClient の [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) クラスと [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) クラスの両方に対応する削除メソッドがあります。このメソッドは、削除を確定すると呼び出されます。 ジョブとタスク自体は課金対象ではありませんが、コンピューティング ノードは課金対象です。 そのため、必要な場合にのみプールを割り当てることをお勧めします。 プールを削除すると、ノード上のタスク出力はすべて削除されます。 ただし、入力ファイルと出力ファイルはストレージ アカウントに残ります。

リソース グループ、Batch アカウント、ストレージ アカウントは、不要になったら削除します。 Azure Portal でこれを行うには、Batch アカウントのリソース グループを選択し、**[リソース グループの削除]** をクリックしてください。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーション パッケージを Batch アカウントに追加する
> * Batch アカウントおよびストレージ アカウントで認証する
> * Storage に入力ファイルをアップロードする
> * アプリケーションを実行するコンピューティング ノードのプールを作成する
> * 入力ファイルを処理するジョブとタスクを作成する
> * タスクの実行を監視する
> * 出力ファイルを取得する

.NET API を使用して Batch ワークロードのスケジュール設定と処理を行う他の例については、GitHub のサンプルを参照してください。

> [!div class="nextstepaction"]
> [Batch C# のサンプル](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
