<properties urlDisplayName="Get Started with Media Services" pageTitle="Media Services の使用 - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# <a name="getting-started"></a>メディア サービスの使用

このチュートリアルでは、Azure のメディア サービスを使用した開発の基礎について説明します。メディア サービスの基本的なワークフローを示し、メディア サービス開発に必要となる一般的なプログラミング オブジェクトおよびタスクについても紹介します。このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、およびダウンロードして再生できるようになります。サーバーでエンコード済みのアセットを参照して再生することもできます。 

このチュートリアルで使用するコードが格納されている Visual Studio の C# プロジェクトは、こちらからダウンロードできます。[ダウンロード](http://go.microsoft.com/fwlink/?linkid=253275)。

このチュートリアルでは、次の基本的な手順について説明します。

* [プロジェクトのセットアップ](#Step1)
* [メディア サービス サーバー コンテキストの取得](#Step2)
* [アセットの作成と、アセットに関連付けられたファイルのメディア サービスへのアップロード](#Step3)
* [アセットのエンコードと、出力されたアセットのダウンロード](#Step4)

## 前提条件
次の前提条件は、Azure Media Services SDK に基づくチュートリアルおよび開発で必要になります。

- 新規または既存の Azure サブスクリプションで作成した Media Services アカウント。詳細については、「[メディア サービス アカウントの作成方法](http://go.microsoft.com/fwlink/?LinkId=256662)」を参照してください。
- オペレーティング システム:Windows 7、Windows 2008 R2、Windows 8 またはそれ以降。
- .NET Framework 4.5 または .NET Framework 4。
- Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate、Express)、またはそれ以降。
- **windowsazure.mediaservices Nuget** パッケージを使用して、**Azure SDK for .NET**、**Azure Media Services SDK for .NET**、および[WCF Data Services 5.0 for OData V3 ライブラリ](http://nuget.org/packages/windowsazure.mediaservices) をインストールし、プロジェクトに参照を追加します。次のセクションでは、インストールの方法と参照の追加方法を示します。

>[WACOM.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、 <a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 無料評価版</a>を参照してください。

<h2><a id="Step1"></a>プロジェクトのセットアップ</h2>

1. Visual Studio 2012 または Visual Studio 2010 SP1 で、C# の新しいコンソール アプリケーションを作成します。**[名前]**、**[場所]**、および **[ソリューション名]** を入力し、[OK] をクリックします。 

2. System.Configuration アセンブリへの参照を追加します。

	**[参照の管理]** ダイアログを使用して参照を追加するには、次の操作を行います。ソリューション エクスプローラーで **[参照]** ノードを**右クリックし**、**[参照の追加]** を選択します。**[参照の管理]** ダイアログで、適切なアセンブリ (ここでは System.Configuration) を選択します。

3. windowsazure.mediaservices Nuget パッケージを使用して、**Azure SDK for .NET**.(Microsoft.WindowsAzure.StorageClient.dll)、**Azure Media Services SDK for .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll)、および **WCF Data Services 5.0 for OData V3** (Microsoft.Data.OData.dll) の各ライブラリへの参照を追加します <a href="http://nuget.org/packages/windowsazure.mediaservices">(まだこの作業を行っていない場合)</a> 。 

	Nuget を使用して参照を追加するには、次の操作を行います。Visual Studio のメイン メニューで、[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール] の順に選択します。コンソール ウィンドウで、「 <i>Install-Package [package name]</i> 」と入力して Enter キーを押します (この場合は  <i>Install-Package windowsazure.mediaservices</i> コマンドを使用します)。

4. **app.config** ファイルに *appSettings* セクションを追加し、Azure メディア サービスのアカウント名とアカウント キーに値を設定します。メディア サービスのアカウント名とアカウント キーは、アカウント設定プロセスで取得済みです。Visual Studio プロジェクトに含まれる app.config ファイル内で、各設定の value 属性にこれらの値を追加します。

	> [WACOM.NOTE]
	Visual Studio 2012 では、App.config ファイルが既定で追加されます。Visual Studio 2010 では、アプリケーション構成ファイルを手動で追加する必要があります。

	<pre><code>
	<configuration>
  	. . . 
  	<appSettings>
    	<add key="accountName" value="Add-Media-Services-Account-Name" />
    	<add key="accountKey" value="Add-Media-Services-Account-Key" />
  	</appSettings>
	</configuration>
	</code></pre>

5. ローカル コンピューターに新しいフォルダーを作成して、supportFiles という名前を付けます (この例では、MediaServicesGettingStarted プロジェクト ディレクトリ内に supportFiles があります)。次のコードは、既存のユーザーの電話番号を変更します。 <a href="http://go.microsoft.com/fwlink/?linkid=253275">このチュートリアル用の</a> プロジェクトには、supportFiles ディレクトリが含まれています。このディレクトリの内容を自分の supportFiles フォルダーにコピーして使用できます。

6. Program.cs ファイルの先頭にある既存のステートメントを次のコードで上書きします。

		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure;
		using Microsoft.WindowsAzure.MediaServices.Client;


7. 次のクラスレベル パス変数を追加します。**_supportFiles** パスは、先ほどの手順で作成したフォルダーを指す必要があります。 

		// Base support files path.  Update this field to point to the base path  
		// for the local support files folder that you create. 
		private static readonly string _supportFiles =
		            Path.GetFullPath(@"../..\supportFiles");
		
		// Paths to support files (within the above base path). You can use 
		// the provided sample media files from the "supportFiles" folder, or 
		// provide paths to your own media files below to run these samples.
		private static readonly string _singleInputFilePath =
		    Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
		private static readonly string _outputFilesFolder =
		    Path.GetFullPath(_supportFiles + @"\outputfiles");
		
8. 認証設定と接続設定を取得できるように、次のクラスレベル パス変数を追加します。これらは、メディア サービスへの接続、認証、およびトークンの取得に必要な設定であり、App.Config ファイルからプルされ、サーバー コンテキストへのアクセスに使用されます。プロジェクトのコードでは、サーバー コンテキストのインスタンスを作成するために、これらの変数が参照されます。
	
		private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
		private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9. 次のクラスレベル パス変数を追加します。これは、サーバー コンテキストへの静的な参照として使用されます。

		// Field for service context.
		private static CloudMediaContext _context = null;
		
<h2><a id="Step2"></a>メディア サービス コンテキストを取得する</h2>

メディア サービス コンテキスト オブジェクトには、メディア サービス プログラミングでアクセスする基本的なオブジェクトおよびコレクションがすべて含まれます。コンテキストには、ジョブ、アセット、ファイル、アクセス ポリシー、ロケーター、その他のオブジェクトなど、重要なコレクションへの参照が含まれています。ほとんどのメディア サービス プログラミング タスクでは、サーバー コンテキストの取得が必要になります。

Program.cs ファイルで、**Main** メソッド内の最初のアイテムとして次のコードを追加します。このコードでは、app.config ファイルに指定されているメディア サービス アカウント名とアカウント キーの値を使用して、サーバー コンテキストのインスタンスを作成しています。このインスタンスが、クラス レベルで作成した **_context** 変数に割り当てられています。

	// Get the service context.
	_context = new CloudMediaContext(_accountName, _accountKey);
	
<h2><a id="Step3"></a>アセットの作成とファイルのアップロード</h2>

このセクションのコードでは、次のことが行われます。 

1. 空のアセットを作成します。<br/>
アセットを作成する際には、これらの暗号化に関する 3 つのオプションを指定できます。 

	- **AssetCreationOptions.None**: 暗号化されません。暗号化されていないアセットを作成するには、このオプションを設定する必要があります。
	- **AssetCreationOptions.CommonEncryptionProtected**: Common Encryption Protected (CENC) ファイル用。既に PlayReady で暗号化されている一連のファイルは、その例です 
	- **AssetCreationOptions.StorageEncrypted**: ストレージ暗号化。Azure ストレージへのアップロードの前にクリア入力ファイルを暗号化します。

		<div class="dev-callout"> 
	<strong>注</strong> 
	<p>メディア サービスでは、Digital Rights Manager (DRM) のようにネットワーク経由ではなく、オンディスクのストレージ暗号化を提供します。</p> 
	</div>

2. アセットに関連付ける AssetFile インスタンスを作成します。
3. アセットへのアクセス許可とアクセス期間を定義する AccessPolicy インスタンスを作成します。
4. アセットへのアクセスを提供する Locator インスタンスを作成します。
5. 単一のメディア ファイルをメディア サービスにアップロードします。作成とアップロードのプロセスのことを "アセットの取り込み" ともいいます。

次のメソッドをクラスに追加します。

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                        AccessPermissions.Write | AccessPermissions.List);

    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

</code></pre>

メソッドの呼び出しを Main メソッド内の **\_context = new CloudMediaContext(_accountName, _accountKey);** 行の後に追加します。 

	IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

<h2><a id="Step4"></a>サーバー上のアセットのエンコードと、出力されたアセットのダウンロード</h2>

メディア サービスでは、エンコード、暗号化、形式変換など、複数の方法でメディア コンテンツを処理するジョブを作成できます。メディア サービスのジョブには、処理作業の詳細を指定したタスクが必ず 1 つ以上含まれています。このセクションでは、基本的なエンコード タスクを作成し、Azure メディア エンコーダーを使用してこのタスクを行うジョブを実行します。このタスクでは、プリセットの文字列を使用して、実行するエンコードの種類を指定します。使用できるプリセットのエンコード値を確認するには、「[Azure Media Encoder 用のタスク プリセット文字列](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj129582.aspx) 」を参照してください。メディア サービスでは、Microsoft Expression Encoder と同じメディア ファイル入力形式および出力形式をサポートされています。サポートされている形式の一覧については、「[Supported File Types for Media Services (メディア サービスでサポートされるファイルの種類)](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh973634.aspx)」を参照してください。

<ol>
<li>
次の <strong>CreateEncodingJob</strong> メソッド定義をクラスに追加します。このメソッドは、エンコード ジョブに必要ないくつかのタスクを実行する方法を示しています。
<ul>
<li>
Declare a new job.
</li>
<li>
そのジョブを扱うメディア プロセッサを宣言します。メディア プロセッサは、エンコード、暗号化、形式変換など、関連する処理のジョブを扱うするコンポーネントです。使用可能なメディア プロセッサには、いくつかの種類があります (_context.MediaProcessors を使用して、これらをすべて反復処理することができます)。後で紹介する GetLatestMediaProcessorByName メソッドは、Azure メディア エンコーダー プロセッサを返します。
</li>
<li>
新しいタスクを宣言します。すべてのジョブには 1 つ以上のタスクが含まれています。タスクには、フレンドリ名、メディア プロセッサ インスタンス、タスク構成文字列、およびタスク作成オプションを渡します。構成文字列ではエンコード設定を指定します。この例では <strong>H264 Broadband 720p</strong> 設定を使用しています。このプリセットでは、単一の MP4 ファイルが生成されます。このプリセットおよびその他のプリセットの詳細については、「<a href="http://msdn.microsoft.com/library/windowsazure/jj129582.aspx">Azure Media Encoder 用のタスク プリセット文字列」を参照してください。</a>.
</li>
<li>
入力アセットをタスクに追加します。この例の入力アセットは、前のセクションで作成済みです。
</li>
<li>
出力アセットをタスクに追加します。出力アセットに対して、表示名、ジョブ完了後に出力をサーバーに保存するかどうかを示すブール値、およびストレージと転送用の出力が暗号化されていることを示す  <strong>AssetCreationOptions.None:</strong> 値を指定します。 
</li>
<li>
ジョブを送信します。<br/>
ジョブの送信は、エンコード ジョブを行うために必要な最後の手順です。
</li>
</ul>
このメソッドでは、ジョブの進行状況の追跡や、エンコード ジョブで作成されたアセットへのアクセスなど、他の便利な (ただし省略可能な) タスクの実行方法についても示しています。
<pre><code>
static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("My encoding job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

    // Create a task with the encoding details, using a string preset.
    ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "H264 Broadband 720p",
        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

    // Specify the input asset to be encoded.
    task.InputAssets.Add(asset);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    // Use the following event handler to check job progress.  
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
    // in your output folder.
    LogJobDetails(job.Id);

    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // **********
    // Optional code.  Code after this point is not required for 
    // an encoding job, but shows how to access the assets that 
    // are the output of a job, either by creating URLs to the 
    // asset on the server, or by downloading. 
    // **********

    // Get an updated job reference.
    job = GetJob(job.Id);

    // If job state is Error the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }

    // Get a reference to the output asset from the job.
    IAsset outputAsset = job.OutputMediaAssets[0];
    IAccessPolicy policy = null;
    ILocator locator = null;

    // Declare an access policy for permissions on the asset. 
    // You can call an async or sync create method. 
    policy =
        _context.AccessPolicies.Create("My 30 days readonly policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

    // Create a SAS locator to enable direct access to the asset 
    // in blob storage. You can call a sync or async create method.  
    // You can set the optional startTime param as 5 minutes 
    // earlier than Now to compensate for differences in time  
    // between the client and server clocks. 

    locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

    // Build a list of SAS URLs to each file in the asset. 
    List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

    // Write the URL list to a local file. You can use the saved 
    // SAS URLs to browse directly to the files in the asset.
    if (sasUrlList != null)
    {
        string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
        StringBuilder fileList = new StringBuilder();
        foreach (string url in sasUrlList)
        {
            fileList.AppendLine(url);
            fileList.AppendLine();
        }
        WriteToFile(outFilePath, fileList.ToString());

        // Optionally download the output to the local machine.
        DownloadAssetToLocal(job.Id, outputFolder);
    }

    
    return job;
}

</code></pre>
</li>
<li>
前に追加した行の後に、Main メソッド内の  <strong>CreateEncodingJob</strong>  メソッドの <strong>呼び出しを</strong> 追加します。
<pre><code>
CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
</code></pre>
</li>
<li>
次のヘルパー メソッドをクラスに追加します。これらは、 <strong>CreateEncodingJob</strong> メソッドをサポートするために必要です。各ヘルパー メソッドの概要を次に示します。
<ul>
<li>
次のコードは、既存のユーザーの電話番号を変更します。 <strong>GetLatestMediaProcessorByName</strong> メソッドは、エンコード、暗号化など、関連する処理タスクを扱う適切なメディア プロセッサを返します。メディア プロセッサを作成するには、作成するプロセッサの適切な文字列名を使用します。mediaProcessor パラメーターに対してメソッドに渡す可能性のある文字列は、 <strong>Azure メディア エンコーダー</strong>、 <strong>Windows Azure Media Packager</strong>、 <strong>Windows Azure Media Encryptor</strong>、 <strong>Storage Decryption</strong> です。
<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    // The possible strings that can be passed into the 
    // method for the mediaProcessor parameter:
    //   Azure Media Encoder
    //   Windows Azure Media Packager
    //   Windows Azure Media Encryptor
    //   Storage Decryption

    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

</code></pre>
</li>
<li>
ジョブを実行する際には、多くの場合、ジョブの進行状況を追跡する手段が必要になります。次のコード例では、StateChanged イベント ハンドラーを定義しています。このイベント ハンドラーはジョブの進行状況を追跡し、状態によってステータスを更新します。このコードでは、LogJobStop メソッドも定義しています。このヘルパー メソッドは、エラー詳細のログ記録を行います。

<pre><code>
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
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

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static void LogJobDetails(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nJob ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);

    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobDetails-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}
        
private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
</li>
<li>
WriteToFile メソッドは、指定された出力フォルダーにファイルを書き込みます。
<pre><code>
static void WriteToFile(string outFilePath, string fileContent)
{
    StreamWriter sr = File.CreateText(outFilePath);
    sr.Write(fileContent);
    sr.Close();
}

</code></pre>
</li>
<li>
メディア サービスでアセットをエンコードした後、エンコード ジョブの結果として出力されたアセットにアクセスできます。このチュートリアルでは、エンコード ジョブの出力にアクセスする方法を 2 つ示します。
<ul>
<li>
サーバー上のアセットに対する SAS URL を作成する。 
</li>
<li>
出力アセットをサーバーからダウンロードする。
</li>
</ul>
GetAssetSasUrlList メソッドは、アセット内のすべてのファイルに対する SAS URL のリストを作成します。 
<pre><code>
static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
{
    // Declare a list to contain all the SAS URLs.
    List<String> fileSasUrlList = new List<String>();

    // If the asset has files, build a list of URLs to 
    // each file in the asset and return. 
    foreach (IAssetFile file in asset.AssetFiles)
    {
        string sasUrl = BuildFileSasUrl(file, locator);
        fileSasUrlList.Add(sasUrl);
    }

    // Return the list of SAS URLs.
    return fileSasUrlList;
}

// Create and return a SAS URL to a single file in an asset. 
static string BuildFileSasUrl(IAssetFile file, ILocator locator)
{
    // Take the locator path, add the file name, and build 
    // a full SAS URL to access this file. This is the only 
    // code required to build the full URL.
    var uriBuilder = new UriBuilder(locator.Path);
    uriBuilder.Path += "/" + file.Name;

    // Optional:  print the locator.Path to the asset, and 
    // the full SAS URL to the file
    Console.WriteLine("Locator path: ");
    Console.WriteLine(locator.Path);
    Console.WriteLine();
    Console.WriteLine("Full URL to file: ");
    Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
    Console.WriteLine();


    //Return the SAS URL.
    return uriBuilder.Uri.AbsoluteUri;
}

</code></pre>
</li>
<li>
次のコードは、既存のユーザーの電話番号を変更します。 <strong>DownloadAssetToLocal</strong>  メソッドは、アセット内の各ファイルをローカル フォルダーにダウンロードします。この例では、アセットが 1 つの入力メディア ファイルを使用して作成されているため、出力アセット ファイルのコレクションに含まれているファイルはエンコード メディア ファイル (.mp4 ファイル) とアセットに関するメタデータが記述された .xml ファイルの 2 つです。このメソッドは、両方のファイルをダウンロードします。
<pre><code>
static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);
    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 10,
        ParallelTransferThreadCount = 10
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>
</li>
<li>
GetJob および GetAsset ヘルパー メソッドは、指定の ID を持つジョブ オブジェクトとアセット オブジェクトへの参照を照会して返します。これと似たタイプの LINQ クエリを使用して、サーバー上にある他のメディア サービス オブジェクトへの参照を返すこともできます。
<pre><code>
static IJob GetJob(string jobId)
{
    // Use a Linq select query to get an updated 
    // reference by Id. 
    var jobInstance =
        from j in _context.Jobs
        where j.Id == jobId
        select j;
    // Return the job reference as an Ijob. 
    IJob job = jobInstance.FirstOrDefault();

    return job;
}
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre>
</li>
</ul>
</li>
</ol>

## コードのテスト
プログラムを実行します (F5 キーを押します)。次のような出力がコンソールに表示されます。

<pre><code>
Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
Time created: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Job Name: My encoding job
Job submitted (client UTC time): 11/14/2012 10:09:39 PM
Media Services account name: Add-Media-Services-Account-Name
Media Services account location: Add-Media-Services-account-location-name

Job(My encoding job) state: Queued.
Please wait...

Job(My encoding job) state: Processing.
Please wait...

********************
Job(My encoding job) is finished.
Please wait while local tasks or downloads complete...
********************

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Downloads are in progress, please wait.

File download path:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.

</code></pre>

1. このアプリケーションを実行した結果は次のようになります。

2. .wmv ファイルがメディア サービスにアップロードされます。 

3. Azure メディア エンコーダーの **H264 Broadband 720p** プリセットを使用して、**ファイルがエンコードされます**。

4. \supportFiles\outputFiles フォルダー内に FileSasUrlList.txt ファイルが作成されます。このファイルには、エンコード済みアセットの URL が含まれています。 

	メディアファイルを再生するには、アセットの URL をテキスト ファイルからコピーし、URL をブラウザーに貼り付けます。 

5. .mp4 メディア ファイルと _metadata.xml ファイルが outputFiles フォルダーにダウンロードされます。

>[WACOM.NOTE]
> メディア サービス オブジェクト モデルのアセットとは、1 対多のファイルを表すメディア サービス コンテンツのコレクション オブジェクトです。Azure Storage 内にあるこのアセットへのベース パスである Azure BLOB URL が、ロケーター パスによって提供されます。アセット内の特定のファイルにアクセスするには、ベース ロケーター パスにファイル名を追加します。

<h2>次のステップ</h2>
このチュートリアルでは、単純なメディア サービス アプリケーションを作成するための一連のプログラミング タスクについて説明しました。サーバー コンテキストの取得、アセットの作成、サーバー上のアセットのダウンロードまたはアクセスなど、メディア サービスの基本的なプログラミング タスクを学習しました。次の手順およびさらに高度な開発タスクについては、次のリソースを参照してください。

- <a href="http://azure.microsoft.com/ja-jp/develop/media-services/resources/">メディア サービスを使用する方法</a>
- <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/hh973618.aspx">Windows Azure Media Services REST API を使用したアプリケーション構築</a>

<h2>その他のリソース</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Azure Media Services 101 - 今すぐビデオをオンラインにしましょう!)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Azure Media Services 102 - 動的パッケージ化機能とモバイル デバイス)</a>

<!-- Anchors. -->
[モバイル サービスの使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps


<!--HONumber=35_1-->
