---
title: Azure Content Moderator - ビデオのモデレート | Microsoft Docs
description: ビデオのモデレートを使用して、成人向けのわいせつなコンテンツの可能性があるものをスキャンします。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377693"
---
# <a name="video-moderation"></a>ビデオのモデレート

現在、人気のある、または地域のソーシャル メディア Web サイトでは、オンライン ビューアーによって十億単位のビデオ表示が生成され、その数は増加し続けています。 成人向けのわいせつなコンテンツの可能性があるものを予測する機械学習ベースのサービスを適用することにより、モデレーション作業にかかるコストを削減できます。

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Content Moderator メディア プロセッサ (パブリック プレビュー) にサインアップします。

### <a name="create-a-free-azure-account"></a>無料の Azure アカウントを作成する

まだ持っていない場合は、[まずこちらで](https://azure.microsoft.com/free/)、無料の Azure アカウントを作成します。

### <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

Content Moderator モデレーターのビデオ機能は、Azure Media Services (AMS) のパブリック プレビュー **メディア プロセッサ**として無料で利用できます。

Azure サブスクリプションで [Azure Media Services アカウントを作成](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account)します。

### <a name="get-azure-active-directory-credentials"></a>Azure Active Directory の資格情報を取得する

   1. Azure Portal を使用して Azure AD 認証資格情報を取得するには、[Azure Media Services ポータル](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)に関する記事を参照してください。
   1. .NET SDK で Azure Active Directory 資格情報を使用する方法については、[Azure Media Services .NET ](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) に関する記事を参照してください。

   > [!NOTE]
   > このクイック スタートのサンプル コードでは、上記の両方の記事に記載されている**サービス プリンシパル認証**方法を使用します。

AMS 資格情報を取得したら、2 つの方法で、Content Moderator メディア プロセッサを試してみることができます。

## <a name="use-azure-media-services-explorer"></a>Azure Media Services エクスプローラーを使用する

対話型の[Azure Media Services (AMS) エクスプローラー](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)を使用することで、AMS アカウントを参照し、ビデオをアップロードし、Content Moderator メディア プロセッサを使用したスキャンを実行します。 それを GitHub から[ダウンロードしてインストール](https://github.com/Azure/Azure-Media-Services-Explorer/releases)します。[ソース コードを参照](http://github.com/Azure/Azure-Media-Services-Explorer)して、AMS SDK の使用について詳細を確認します。

![Azure Media Services エクスプローラーと Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Visual Studio および C# での .NET のクイック スタート

1. お使いのソリューションに新しい**コンソール アプリ (.NET Framework)** プロジェクトを追加します。

   サンプル コードで、プロジェクト **VideoModeration** に名前を付けます。

1. このプロジェクトをお使いのソリューションのシングル スタートアップ プロジェクトとして選択します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

[NuGet](https://www.nuget.org/) で入手可能な次の NuGet パッケージをインストールします。

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

プログラムの using ステートメントを修正します。

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>アプリケーション固有の設定を初期化する

次の静的フィールドを Program.cs 内の **Program** クラスに追加します。

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>事前設定ファイル (json) を作成する

バージョン番号が含まれる現在のディレクトリ内で JSON ファイルを作成します。

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>次のコードを main メソッドに追加する

main メソッドでは、まず Azure メディア コンテキストを作成し、次にビデオが BLOB ストレージ内にある場合は Azure Storage コンテキストを作成します。
残りのコードでは、ローカル フォルダー、BLOB、または Azure ストレージ コンテナー内の複数の BLOB からのビデオをスキャンします。
コードの他の行をコメント化することにより、すべてのオプションを試すことができます。

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Azure メディア コンテキストを作成するコードを追加する

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Azure Storage コンテキストを作成するコードを追加する
Storage 資格情報から作成されたストレージ コンテキストを使用して、BLOB ストレージにアクセスします。

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>ローカル ファイルと BLOB から Azure メディア資産を作成するコードを追加する
Content Moderator メディア プロセッサは、Azure Media Services プラットフォーム内の**資産**に対してジョブを実行します。
以下のメソッドでは、ローカル ファイルまたは関連付けられた BLOB から資産が作成されます。

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>コンテナー内の (BLOB としての) ビデオのコレクションをスキャンするコードを追加する

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Content Moderator ジョブを実行するメソッドを追加する

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>2 つのヘルパー関数を追加する

これらのメソッドは、Azure Media Services 資産から Content Moderator 出力ファイル (JSON) をダウンロードすると共に、プログラムが実行状態をコンソールに記録できるようにモデレーション ジョブの状態の追跡を支援します。

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力を確認する

Content Moderation ジョブが完了したら、JSON 応答を分析します。 以下の要素で構成されます。

- ビデオ情報の概要
- "**フラグメント**" としての **Shots**
- "**イベント**" としての **Key frames** と、**Adult** スコアおよび **Racy** スコアに基づく **reviewRecommended"** (= true または false)" フラグ
- **start**、**duration**、**totalDuration**、**timestamp** は "ティック" 単位です。 秒単位で表される数値を得るために、**timescale** で割り算します。
 
> [!NOTE]

> - `adultScore` は、特定の状況で、性的に露骨な表現または成人向けの表現であると考えられるコンテンツの存在の可能性および予測スコアを表します。
> - `racyScore` は、特定の状況で、性的に挑発的または成熟した表現であると考えられるコンテンツの存在の可能性および予測スコアを表します。
> - `adultScore` と `racyScore` の値は 0 から 1 の範囲です。 スコアが高いほど、モデルによる予測においてカテゴリが該当する可能性は高くなります。 このプレビューは、手動でコード化された結果ではなく、統計モデルに依存します。 独自のコンテンツでテストを行うことで、各カテゴリを要件に合わせて設定する方法を決定することをお勧めします。
> - `reviewRecommended` の値は true または false のいずれかであり、内部スコアのしきい値に応じて決まります。 顧客は、この値を使用するか、それとも独自のコンテンツ ポリシーに基づくカスタムしきい値を決定するかを評価する必要があります。
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>次の手順

モデレーション出力から[ビデオ レビュー](video-reviews-quickstart-dotnet.md)を生成する方法について説明する。

ビデオ レビューに[トランスクリプト モデレーション](video-transcript-moderation-review-tutorial-dotnet.md)を追加する。

[完全なビデオおよびトランスクリプト モデレーション ソリューション](video-transcript-moderation-review-tutorial-dotnet.md)をビルドする方法に関する詳細なチュートリアルを確認する。

.NET 用のこのクイック スタートや他の Content Moderator のクイックスタートの [Visual Studio ソリューションをダウンロードする](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。
