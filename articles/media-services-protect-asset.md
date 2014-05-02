<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="メディア サービスでアセットを暗号化する" pageTitle="メディア サービスでアセットを暗号化する方法 - Azure" metaKeywords="" description="Microsoft PlayReady Protection を使用してメディア サービスでアセットを暗号化する方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" metaCanonical="" services="media-services" documentationCenter="" title="方法: PlayReady Protection でアセットを保護する" authors="migree" solutions="" manager="" editor="" />





<h1><a name="playready"></a>方法: PlayReady Protection でアセットを保護する</h1>

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[ジョブの進行状況をチェックする方法に関するページ](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409)を参照してください。

Azure メディア サービスでは、Microsoft PlayReady Protection と連携してアセットを暗号化するジョブを送信できます。このセクションで紹介するコードは、入力フォルダーから複数のストリーミング ファイルを取得してタスクを作成し、PlayReady Protection を使ってそれらを暗号化します。

次の例は、PlayReady Protection を提供する単純なジョブを作成する方法を示しています。

   1. 構成データを取得します。サンプルの構成ファイルは「[Azure Media Encryptor のタスク プリセット](http://msdn.microsoft.com/ja-jp/library/hh973610.aspx)」トピックから入手できます。
   2. MP4 入力ファイルをアップロードします。
   3. MP4 ファイルをスムーズ ストリーミング アセットに変換します。
   4. アセットを PlayReady で暗号化します。
   5. ジョブを送信します。

これらの手順を実装したコード例を次に示します。

<pre><code>
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Create a storage-encrypted asset and upload the MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declare a new job to contain the tasks.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Set up the first task. 

    // Read the task configuration data into a string. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Get a media processor instance
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Create a task with the conversion details, using the configuration data 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Specify the input asset to be converted.
    task.InputAssets.Add(asset);

    // Add an output asset to contain the results of the job. We do not need 
    // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
    // parameter to false. 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Set up the encryption task. 

    // Read the configuration data into a string. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Get a media processor instance
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Create a second task, specifying a task name, the media processor, and configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Add the input asset, which is the smooth streaming output asset from the first task. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Add an output asset to contain the results of the job. Persist the output by setting 
    // the shouldPersistOutputOnCompletion param to true.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Use the following event handler to check job progress. 
    job.StateChanged += new
            EventHandler&lt;JobStateChangedEventArgs&gt;(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
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

</code></pre>
PlayReady Protection の詳細については、以下を参照してください。
<ul>
<li><a href="http://msdn.microsoft.com/ja-jp/library/dn189154.aspx">Microsoft PlayReady でのアセットの保護</a></li>
<li><a href="http://www.microsoft.com/PlayReady/">Microsoft PlayReady</a></li>
</ul>

<h2>次のステップ</h2>
これで、メディア サービスでアセットを保護する方法を学習できました。次は、「[メディア サービスのコンテンツの管理方法](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409)」トピックに進みます。

