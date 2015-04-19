<properties 
	pageTitle="Azure メディア エンコーダーを使用してアセットをエンコードする方法" 
	description="Azure メディア エンコーダーを使用してメディア サービスでメディア コンテンツをエンコードする方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Azure メディア エンコーダーを使用してアセットをエンコードする方法

この記事は、[Media Services Video on Demand workflow (メディア サービス ビデオ オンデマンド ワークフロー)](media-services-video-on-demand-workflow.md) シリーズの一部です。 

##概要
インターネット経由でデジタル ビデオを配信するには、メディアを圧縮する必要があります。デジタル ビデオ ファイルはサイズがとても大きく、インターネット経由で配信したり、顧客の端末でうまく表示したりできない可能性があります。エンコードはビデオやオーディオを圧縮するプロセスです。圧縮することでメディア コンテンツが見やすくなります。

エンコード ジョブはメディア サービスで最も一般的な処理の 1 つです。エンコード ジョブを作成することで、メディア ファイルをあるエンコードから別のエンコードに変換できるようになります。エンコードには、メディア サービスの組み込みメディア エンコーダーを使用できます。メディア サービスのパートナーから提供されているエンコーダーを使うこともできます。サードパーティのエンコーダーは Azure Marketplace から入手できます。エンコーディング タスクの詳細を指定するには、エンコーダー用に定義されたプリセット文字列を使用するか、プリセット構成ファイルを使用します。使用できるプリセットの種類を表示するには、Azure メディア サービスのタスク プリセットをご覧ください。サード パーティのエンコーダーを使用した場合は、[ファイルを検証](https://msdn.microsoft.com/ja-jp/library/azure/dn750842.aspx)する必要があります。

中間ファイルは常にアダプティブ ビットレート MP4 セットにエンコードして、その後 [動的パッケージ](https://msdn.microsoft.com/ja-jp/library/azure/jj889436.aspx) を使用して目的の形式に変換することをお勧めします。


##1 つのエンコード タスクを持つジョブの作成 

Azure メディア エンコーダーでエンコードするときは、[こちら](https://msdn.microsoft.com/ja-jp/library/azure/dn619389.aspx) で指定されているタスク構成のプリセットを使うことができます。

###Media Services SDK for .NET の使用  

次の **EncodeToAdaptiveBitrateMP4Set** メソッドは、エンコード ジョブを作成し、ジョブにエンコード タスクを 1 つ追加します。このタスクは、"Azure メディア エンコーダー" を使用して、"H264 アダプティブ ビットレート MP4 セット 720p"へのエンコードを行います。 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

###Media Services SDK for .NET Extensions の使用

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

##チェーン タスクによるジョブの作成 

アプリケーション シナリオの多くで、一連のタスク処理が開発者によって作成されます。メディア サービスでは、一連のチェーン タスクを作成できます。各タスクはそれぞれ異なる処理手順を実行し、異なるメディア プロセッサを使うことができます。チェーン タスクでは、あるタスクから別のタスクにアセットを渡しながら一連のタスクを順番に実行できます。ただし、ジョブで実行されるタスクが必ず順番に実行されなければならないということではありません。チェーン タスクを作成するときは、一連の **ITask** オブジェクトは 1 つの **IJob** オブジェクト内で作成されます。

>[AZURE.NOTE] 現時点では、ジョブごとに設定できるタスクは 30 までです。30 以上のタスクをつなげる必要がある場合、別のジョブを作成してタスクを設定します。

次の **CreateChainedTaskEncodingJob** メソッドは、2 つのチェーン タスクが含まれるジョブを作成します。そのため、このメソッドは、2 つの出力アセットを含むジョブを返します。

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


##次のステップ
これで、ジョブを作成してアセットをエンコードする方法を学習できました。次は、[メディア サービスでジョブの進行状況をチェックする方法](media-services-check-job-progress.md) に関するトピックに進みます。

[Azure Marketplace]: https://datamarket.azure.com/
[エンコーダー プリセット]: http://msdn.microsoft.com/library/dn619392.aspx
[方法: メディア プロセッサ インスタンスを取得する]:http://go.microsoft.com/fwlink/?LinkId=301732
[方法: 暗号化されたアセットをアップロードする]:http://go.microsoft.com/fwlink/?LinkId=301733
[方法: ダウンロードによってアセットを配信する]:http://go.microsoft.com/fwlink/?LinkId=301734
[方法: ジョブの進行状況をチェックする]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager のタスク プリセット]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=45--> 
