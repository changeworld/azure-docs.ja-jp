<properties urlDisplayName="How to Encode an Asset" pageTitle="Media Services 用にアセットをエンコードする方法 - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#方法:アセットをエンコードする
この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、「[方法: メディア プロセッサを取得する](../media-services-get-media-processor/)」を参照してください。

Azure メディア エンコーダーを使用すると、さまざまなメディア エンコーディングと各種の形式でサーバー上のメディア コンテンツをエンコードできます。メディア サービスのパートナーから提供されているエンコーダーを利用することもできます。サードパーティのエンコーダーは [Azure Marketplace][] から入手できます。エンコーディング タスクの詳細を指定するには、[エンコーダー プリセット][]文字列を使用するか、構成ファイルを使用します。 

##MP4 アダプティブ ビットレートへのエンコード
mezzanine ファイルを MP4 アダプティブ ビットレート セットにエンコードしてから、動的パッケージを使用してコンテンツを配信することをお勧めします。詳細については、「[Media Services SDK for .NET によるエンコード ジョブの作成](http://msdn.microsoft.com/ja-jp/library/azure/dn282273.aspx)」、「[動的パッケージ](http://msdn.microsoft.com/ja-jp/library/azure/jj889436.aspx)」、および「[コンテンツの配信](http://msdn.microsoft.com/ja-jp/library/azure/hh973618.aspx)」を参照してください。

##MP4 へのエンコード
次のメソッドは、1 つのアセットをアップロードし、"H264 Broadband 720p" プリセットを使用してそのアセットを MP4 にエンコードするジョブを作成します。このプリセットでは、H264 エンコーディングを 720p 解像度で使用して 1 つの MP4 を作成します。
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

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
</code></pre>
<h2>スムーズ ストリーミングへのエンコード</h2>
ビデオをスムーズ ストリーミングにエンコードする場合、次の 2 つのオプションがあります。
<ul>
<li> スムーズ ストリーミングに直接エンコードする </li>
<li> MP4 にエンコードしてからスムーズ ストリーミングに変換する</li>
</ul>

スムーズ ストリーミングに直接エンコードするには、先に示したコードを使用しますが、スムーズ ストリーミング エンコーダー プリセットの 1 つを使用します。エンコーダー プリセットの完全な一覧については、「[Azure Media Encoder 用のタスク プリセット文字列](http://msdn.microsoft.com/ja-jp/library/jj129582.aspx)」を参照してください。 

MP4 に変換してからスムーズ ストリーミングに変換するには、Azure Media Packager を使用します。Azure Media Packager では、文字列プリセットがサポートされないため、構成オプションを XML で指定する必要があります。MP4 をスムーズ ストリーミングに変換するために必要な XML については、[Azure Media Packager 用のタスクのプリセット][]」を参照してください。XML をコピーし、プロジェクト内の MediaPackager_MP4ToSmooth.xml という名前のファイルに貼り付けます。次のコードは、MP4 アセットをスムーズ ストリーミングに変換する方法を示しています。次のメソッドに既存のアセットを指定すると、そのアセットが変換されます。 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

アセットの処理の詳細については、以下を参照してください。
<ul>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129580.aspx">Media Services SDK for .NET を使って資産を処理する</a></li>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129574.aspx">Media Services REST API を使って資産を処理する</a></li>
</ul>

##次のステップ
これで、ジョブを作成してアセットをエンコードする方法を学習できました。次は、「[方法: ジョブの進行状況をチェックする](../media-services-check-job-progress/) 」に進みます。

[Azure Marketplace]: https://datamarket.azure.com/
[エンコーダー プリセット]: http://msdn.microsoft.com/ja-jp/library/dn619392.aspx
[方法: メディア プロセッサ インスタンスを取得する]:http://go.microsoft.com/fwlink/?LinkId=301732
[方法: Upload an Encrypted Asset (暗号化されたアセットをアップロードする)]:http://go.microsoft.com/fwlink/?LinkId=301733
[方法: ダウンロードによってアセットを配信する]:http://go.microsoft.com/fwlink/?LinkId=301734
[ジョブの進行状況をチェックする]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager 用のタスクのプリセット]:http://msdn.microsoft.com/ja-jp/library/windowsazure/hh973635.aspx

<!--HONumber=35_1-->
