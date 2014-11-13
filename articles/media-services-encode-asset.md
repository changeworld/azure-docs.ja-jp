<properties urlDisplayName="How to Encode an Asset" pageTitle="Media Services 用にアセットをエンコードする方法 - Azure" metaKeywords="" description="Azure メディア エンコーダーを使用して Media Services でメディア コンテンツをエンコードする方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" metaCanonical="" services="media-services" documentationCenter="" title="方法: アセットをエンコードする" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# 方法: アセットをエンコードする

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[メディア プロセッサを取得する方法][メディア プロセッサを取得する方法]に関するページを参照してください。

Azure メディア エンコーダーを使用すると、さまざまなメディア エンコーディングと各種の形式でサーバー上のメディア コンテンツをエンコードできます。メディア サービスのパートナーから提供されているエンコーダーを利用することもできます。サードパーティのエンコーダーは [Azure Marketplace][Azure Marketplace] から入手できます。エンコーディング タスクの詳細を指定するには、[エンコーダー プリセット][エンコーダー プリセット]文字列を使用するか、構成ファイルを使用します。

## MP4 へのエンコード

次のメソッドは、1 つのアセットをアップロードし、"H264 Broadband 720p" プリセットを使用してそのアセットを MP4 にエンコードするジョブを作成します。このプリセットでは、H264 エンコーディングを 720p 解像度で使用して 1 つの MP4 を作成します。

     static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder) { //Create an encrypted asset and upload to storage. IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);
        // Declare a new job.

        IJob job = _context.Jobs.Create("My encoding job");

        // Get a reference to the Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-JobId.txt file 
        // in your output folder.
        LogJobDetails(job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nExiting method due to job error.");
            return job;
        }

        // Perform other tasks. For example, access the assets that are the output of a job, 
        // either by creating URLs to the asset on the server, or by downloading. 
        return job;
    }

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
            break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
            case JobState.Error:

                // Cast sender as a job.
                IJob job = (IJob)sender;

                // Display or log error details as needed.
                LogJobStop(job.Id);
                break;
            default:
                break;
        }
    }

<p>
</code>

</pre>

## スムーズ ストリーミングへのエンコード

ビデオをスムーズ ストリーミングにエンコードする場合、次の 2 つのオプションがあります。

-   スムーズ ストリーミングに直接エンコードする
-   MP4 にエンコードしてからスムーズ ストリーミングに変換する

</p>
スムーズ ストリーミングに直接エンコードするには、先に示したコードを使用しますが、スムーズ ストリーミング エンコーダー プリセットの 1 つを使用します。エンコーダー プリセットの完全な一覧については、「[Media Services Encoder システムのプリセット][Media Services Encoder システムのプリセット]」を参照してください。

MP4 に変換してからスムーズ ストリーミングに変換するには、Azure Media Packager を使用します。Azure Media Packager では、文字列プリセットがサポートされないため、構成オプションを XML で指定する必要があります。MP4 をスムーズ ストリーミングに変換するために必要な XML については、「[Azure Media Packager のタスク プリセット][Azure Media Packager のタスク プリセット]」を参照してください。XML をコピーし、プロジェクト内の MediaPackager\_MP4ToSmooth.xml という名前のファイルに貼り付けます。次のコードは、MP4 アセットをスムーズ ストリーミングに変換する方法を示しています。次のメソッドに既存のアセットを指定すると、そのアセットが変換されます。

    private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath) { // Declare a new job to contain the tasks IJob job = _context.Jobs.Create("Convert to Smooth Streaming job"); // Set up the first Task to convert from MP4 to Smooth Streaming. // Read in task configuration XML string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml")); // Get a media packager reference IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager"); // Create a task with the conversion details, using the configuration data ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task", processor, configMp4ToSmooth, TaskOptions.None); // Specify the input asset to be converted. task.InputAssets.Add(assetToConvert); // Add an output asset to contain the results of the job. task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None); // Use the following event handler to check job progress. // The StateChange method is the same as the one in the previous sample job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged); // Launch the job. job.Submit(); // Check job execution and wait for job to finish. Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None); progressJobTask.Wait(); // Get a refreshed job reference after waiting on a thread. job = GetJob(job.Id); // Check for errors if (job.State == JobState.Error) { Console.WriteLine("\nExiting method due to job error."); } return job;}

</p>
アセットの処理の詳細については、以下を参照してください。

-   [Media Services SDK for .NET を使って資産を処理する][Media Services SDK for .NET を使って資産を処理する]
-   [Media Services REST API を使って資産を処理する][Media Services REST API を使って資産を処理する]

</p>
## 次のステップ

これで、ジョブを作成してアセットをエンコードする方法を学習できました。次は、[メディア サービスでジョブの進行状況をチェックする方法][メディア サービスでジョブの進行状況をチェックする方法]に関するトピックに進みます。

  [メディア プロセッサを取得する方法]: ../media-services-get-media-processor/
  [Azure Marketplace]: https://datamarket.azure.com/
  [エンコーダー プリセット]: http://msdn.microsoft.com/ja-jp/library/hh973610.aspx
  [Media Services Encoder システムのプリセット]: http://msdn.microsoft.com/ja-jp/library/jj129582.aspx
  [Azure Media Packager のタスク プリセット]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh973635.aspx
  [Media Services SDK for .NET を使って資産を処理する]: http://msdn.microsoft.com/ja-jp/library/jj129580.aspx
  [Media Services REST API を使って資産を処理する]: http://msdn.microsoft.com/ja-jp/library/jj129574.aspx
  [メディア サービスでジョブの進行状況をチェックする方法]: ../media-services-check-job-progress/
