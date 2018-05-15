---
title: Azure Media Services を使用してビデオを分析する | Microsoft Docs
description: Azure Media Services を使用してビデオを分析するには、このチュートリアルの手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: a228443a787db2cc71a7700b978d8f52edb59e14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>チュートリアル: Azure Media Services を使用してビデオを分析する 

このチュートリアルでは、Azure Media Services を使用したビデオの分析について説明します。 記録されたビデオまたはオーディオ コンテンツに関する詳細な分析情報を得る必要のある多くのシナリオがあります。 たとえば、より高い顧客満足度を実現するため、組織は音声テキスト変換処理を行って、カスタマー サポートの記録をインデックスとダッシュボードを含む検索可能なカタログに変換できます。 その後、一般的なクレーム、そのようなクレームのソースの一覧など、ビジネスの分析情報を取得することができます。

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * Azure Cloud Shell を起動する
> * Media Services アカウントを作成する
> * Media Services API にアクセスする
> * サンプル アプリの構成
> * サンプル コードを詳しく調べる
> * アプリの実行
> * 出力を調べる
> * リソースのクリーンアップ

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Visual Studio がインストールされていない場合は、[Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手できます。

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>サンプル コードを詳しく調べる

このセクションでは、*AnalyzeVideos* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) ファイルで定義されている関数を調べます。

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 まず、トークンを取得し、返されたトークンから **ClientCredential** オブジェクトを作成する必要があります。 記事の先頭で複製したコードでは、**ArmClientCredential** オブジェクトを使用してトークンを取得しています。  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>ジョブの結果を格納する出力アセットを作成する 

出力アセットは、ジョブの結果を格納します。 プロジェクトで定義されている **DownloadResults** 関数は、この出力アセットから "output" フォルダーに結果をダウンロードするので、取得したものを確認できます。

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```
### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>ビデオを分析する変換とジョブを作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ**を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは**変換**と呼ばれます。 詳しくは、「[Transforms and jobs](transform-concept.md)」(変換とジョブ) をご覧ください。 このチュートリアルで説明されているサンプルでは、指定されたビデオを分析するレシピが定義されています。 

#### <a name="transform"></a>変換

新しい **Transform** インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように **TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この例では、**VideoAnalyzerPreset** プリセットが使われていて、言語 ("en-US") がコンストラクターに渡されています。 このプリセットを使用して、ビデオから複数の音声と画像の分析情報を抽出できます。 複数のオーディオ情報をビデオから抽出する必要がある場合は、**AudioAnalyzerPreset** プリセットを使用できます。 

**Transform** を作成するときは、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。  Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    Preset preset)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput(preset),
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>ジョブ

上で説明したように、**Transform** オブジェクトはレシピであり、**Job** は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。 ビデオの場所は、HTTPS URL、SAS URL、または Media Services アカウント内にあるアセットを使用して指定できます。 

この例では、入力ビデオは指定された HTTPS URL からアップロードされます。  

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName, string jobName, string outputAssetName)
{
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });
            
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
                resourceGroupName,
                accountName,
                transformName,
                uniqueJobName,
                new Job
                {
                    Input = jobInput,
                    Outputs = jobOutputs,
                });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 ジョブ完了の通知の取得にはさまざまなオプションがあります。 最も簡単なオプション (次に示すもの) は、ポーリングを使うものです。 

潜在的な待機時間により、ポーリングは運用アプリケーション用の推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。

Event Grid は、高可用性、一貫したパフォーマンス、および動的スケーリングを目的に設計されています。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 単純な HTTP ベースのリアクティブ イベント ハンドリングでは、インテリジェントなイベント フィルタリングやイベント ルーティングを使用して、効率的なソリューションを構築できます。 [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="download-the-result-of-the-job"></a>ジョブの結果をダウンロードする

 次の関数は出力アセットから "output" フォルダーに結果をダウンロードするので、ジョブの結果を調べることができます。 

```csharp
private static void DownloadResults(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
            resourceGroup,
            accountName,
            assetName,
            permissions: AssetContainerPermission.Read,
            expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
            );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);

    foreach (IListBlobItem blobItem in container.ListBlobs(null, true, BlobListingDetails.None))
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            blob.DownloadToFile(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

### <a name="clean-up-resource-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator などは保持します)。 実験後にアカウントをクリーンアップする場合は、再利用する予定がないリソースを削除する必要があります。 たとえば、次のコードはジョブを削除します。

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName, 
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

Ctrl + F5 キーを押して、*AnalyzeVideos* アプリケーションを実行します。

プログラムを実行すると、ジョブはビデオで検出された各顔のサムネイルを生成します。 insights.json ファイルも生成します。

## <a name="examine-the-output"></a>出力を調べる

ビデオ分析の出力ファイルは、insights.json という名前です。 このファイルには、ビデオに関する分析情報が含まれています。 json ファイルに含まれる要素の説明については、「[Media intelligence](intelligence-concept.md)」(メディア インテリジェンス) をご覧ください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。 **CloudShell** ツールを使うことができます。

**CloudShell** で、次のコマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>マルチスレッド

Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリケーションで使うときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)
