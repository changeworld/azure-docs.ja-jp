<properties 
	pageTitle="Azure Media Indexer によるメディア ファイルのインデックス作成" 
	description="Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。このトピックでは、Media Indexer の使用方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/07/2015"   
	ms.author="juliako"/>


# Azure Media Indexer によるメディア ファイルのインデックス作成

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。バッチ内の 1 つのメディア ファイルまたは複数のメディア ファイルを処理できます。

>[AZURE.IMPORTANT]コンテンツのインデックスを作成する場合は、クリアな (バック グラウンド ミュージック、ノイズ、特殊効果、またはマイク ヒスノイズなどがない) 音声機能を持つメディア ファイルを使用してください。適切なコンテンツの例としては、記録された会議、講義またはプレゼンテーションなどがあります。ムービー、テレビ番組、混合音声とサウンド効果を含むもの、バックグラウンド ノイズ (ヒスノイズ) を含む記録状態が良好でないコンテンツは、インデックス作成に適しません。


インデックス作成ジョブでは、ファイルの各インデックス作成で 4 つの出力が生成されます。

- SAMI 形式のクローズド キャプション ファイル。
- Timed Text Markup Language (TTML) 形式のクローズド キャプション ファイル。

	SAMI と TTML には「見分け」と呼ばれるタグが含まれ、ソース ビデオ内の音声がどれくらい認識可能であるかに基づいて、インデックス作成ジョブを評価します。見分けの値を使用して、出力ファイルの使いやすさを検査します。低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。
- キーワード ファイル (XML)。
- SQL サーバーで使用する Audio Indexing BLOB (AIB) ファイル。
	
	詳細については、「[AIB ファイルを Azure Media Indexer および SQL Server で使用する](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)」をご覧ください。


このトピックでは、**アセットのインデックス**と**複数のファイルのインデックス**のインデックス作成ジョブを作成する方法について説明します。

Azure Media Indexer の最新の更新プログラムについては、[Media Services のブログ](http://azure.microsoft.com/blog/topics/media-services/)をご覧ください。

##タスクのインデックスを作成するための構成とマニフェスト ファイルの使用

タスクの構成を使用して、インデックス作成のタスクの詳細を指定できます。たとえば、メディア ファイルに使用するメタデータを指定できます。このメタデータは、言語エンジンによって、そのボキャブラリの展開に使用され、音声認識の精度が大幅に向上します。

マニフェスト ファイルを使用して一度に複数のメディア ファイルを処理できます。

詳細については、「[Azure Media Indexer 用のタスク プリセット](https://msdn.microsoft.com/library/azure/dn783454.aspx)」をご覧ください。

##アセットのインデックス作成

次のメソッドは、アセットとしてメディア ファイルをアップロードし、ジョブを作成してアセットのインデックスを作成します。

構成ファイルが指定されていない場合、メディア ファイルは既定のすべての設定を使用してインデックスが作成されます。
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
###<a id="output_files"></a>出力ファイル

ジョブのインデックスを作成すると、次の出力ファイルを生成します。ファイルは、最初の出力アセットに格納されます。


<table border="1">
<tr><th>ファイル名</th><th>説明</th></tr>
<tr><td>InputFileName.aib </td>
<td>オーディオ インデックス BLOB ファイル。<br/><br/>
オーディオ インデックス BLOB (AIB) ファイルは、フルテキスト検索を使用して Microsoft SQL server で検索できるバイナリ ファイルです。AIB ファイルは、各単語に豊富な代替候補を含み、より充実した検索を実現しているため、単純なキャプション ファイルよりも強力です。
<br/>
<br/>
Microsoft SQL server 2008 以降を実行しているマシンに Indexer SQL アドオンのインストールする必要があります。Microsoft SQL サーバーのフル テキスト検索を使用して、AIB を検索すると、WAMI によって生成されたクローズド キャプション ファイルを検索するよりも正確な検索結果が得られます。これは、クローズド キャプション ファイルがオーディオの各セグメントに信頼度が最上位の単語を含んでいるのに対し、AIB には代替候補として似ているサウンドの単語が含まれているためです。話された単語の検索が最も重要な場合は、AIB を Microsoft SQL Server と組み合わせて使用することをお勧めします。
<br/><br/>
アドオンをダウンロードするには、<a href="http://aka.ms/indexersql">[Azure Media Indexer SQLアドオン]</a> をクリックします。
<br/><br/>
Apache Lucene/Solr などの他の検索エンジンを使用してクローズド キャプションとキーワードの XML ファイルに基づいたビデオをインデックスすることも可能ですが、検索精度は低くなります。</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>SAMI と TTML 形式のクローズド キャプション (CC) ファイル
<br/><br/>
オーディオとビデオ ファイルを聴覚障がいを持つユーザーにアクセスできるようにするために使用できます。
<br/><br/>
SAMI と TTML には、<b>見分け</b>と呼ばれるタグが含まれ、ソース ビデオ内の音声がどれくらい認識可能であるかに基づいて、インデックス作成ジョブを評価します。<b>見分け</b>の値を使用して、出力ファイルの使いやすさを検査します。低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>キーワードのファイルです。
<br/><br/>
キーワードのファイルは、頻度とオフセットの情報を含む、音声コンテンツから抽出されたキーワードを含む XML ファイルです。
<br/><br/>
このファイルは、音声分析を実行する、Bing、Google または Microsoft SharePoint などの検索エンジンに公開してメディア ファイルをより検索しやすくする、または関連性の高い広告を配信するなど、さまざまな目的で使用できます。</td></tr>
</table>

すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。詳細については、「[エラー コード](#error_codes)」をご覧ください。

##複数のファイルのインデックス作成

次のメソッドは、アセットとして複数のメディア ファイルをアップロードし、ジョブを作成してバッチ内のすべてのファイルのインデックスを作成します。

.Lst 拡張子のマニフェスト ファイルが作成され、アセットにアップロードします。マニフェスト ファイルには、すべてのアセットファイルの一覧が含まれています。詳細については、「[Azure Media Indexer 用のタスク プリセット](https://msdn.microsoft.com/library/azure/dn783454.aspx)」をご覧ください。
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###出力ファイル

1 つ以上の入力メディア ファイルがある場合は WAMI は、ジョブの出力に 'JobResult.txt' という名前のマニフェスト ファイルを生成します。各入力メディアのファイルでは、結果として得られる AIB、SAMI、TTML、キーワード ファイルは、次に示すように順に番号が振られます。

出力ファイルについては、「[出力ファイル](#output_files)」をご覧ください。


<table border="1">
<tr><th>ファイル名</th><th>説明</th></tr>
<tr><td>JobResult.txt</td>
<td>出力マニフェスト
<br/><br/>出力のマニフェスト ファイル (JobResult.txt) の形式を次に示します。
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>エイリアス</th><th>MediaLength</th><th>エラー</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
各行は 1 つの入力メディア ファイルを表します。
<br/><br/>
InputFile: アセットのファイル名または入力メディア ファイルの URL。
<br/><br/>
エイリアス: 対応する出力ファイルの名前。
<br/><br/>
MediaLength: 秒単位の入力メディア ファイルの長さ。Can be 0 は、この入力で発生するエラーです。
<br/><br/>
エラー: このメディア ファイルが正常にインデックスを作成するかどうかを示します。正常に作成された場合は 0、それ以外の場合は失敗です。具体的なエラーについては、<a href="#error_codes">エラー コード</a>をご覧ください。
</td></tr>
<tr><td>Media_1.aib </td>
<td>File #0 - Audio indexing blob file.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>File #0 - SAMI と TTML 形式のクローズド キャプション (CC) ファイル。</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>File #0 - キーワードのファイル</td></tr>
<tr><td>Media_2.aib </td>
<td>File #1 - オーディオ インデックス BLOB ファイル。</td></tr>
</table>

すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。詳細については、「[エラー コード](#error_codes)」をご覧ください。

###部分的に成功したジョブ

すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。詳細については、「[エラー コード](#error_codes)」をご覧ください。


(成功したジョブの場合) と同じ出力が生成されます。エラー列の値に基づいて、出力マニフェスト ファイルで、入力ファイルが失敗したかどうかを参照します。失敗した入力ファイルでは、結果として得られる AIB、SAMI、TTML、キーワード ファイルは生成されません。


### <a id="error_codes"></a>エラー コード


<table border="1">
<tr><th>コード</th><th>名前</th><th>考えられる原因</th></tr>
<tr><td>2000</td><td>構成が無効です</td><td>構成が無効です</td></tr>
<tr><td>2001</td><td>無効な入力アセット</td><td>入力アセット、または空のアセットがありません。</td></tr>
<tr><td>2002</td><td>マニフェストが無効です</td><td>マニフェストが空か、マニフェストに無効な項目が含まれています。</td></tr>
<tr><td>2003</td><td>メディア ファイルのダウンロードに失敗しました</td><td>マニフェスト ファイルでの URL が無効です。</td></tr>
<tr><td>2004</td><td>サポートされていないプロトコル</td><td>メディア URL のプロトコルはサポートされません。</td></tr>
<tr><td>2005</td><td>サポートされていないファイルの種類</td><td>入力メディア ファイルの種類がサポートされていません。</td></tr>
<tr><td>2006</td><td>入力のファイルが多すぎます</td><td>入力のマニフェストに 10 個を超えるファイルがあります。</td></tr>
<tr><td>3000</td><td>メディア ファイルのデコードに失敗しました</td>
<td>サポートされていないメディア コーデックです。
<br/>または<br/>
破損したメディア ファイルです。
<br/>または<br/>
入力メディアのオーディオ ストリームがありません。</td></tr>
<tr><td>4000</td><td>バッチのインデックス作成一部成功しました</td><td>一部の入力メディア ファイルは、インデックスを付けるできませんでした。詳細については、<a href="output_files">出力ファイル</a>をご覧ください。</td></tr>
<tr><td>その他の</td><td>内部エラー</td><td>サポート チームにお問い合わせください。</td></tr>
</table>


##<a id="supported_languages"></a>サポートされている言語

現時点では、英語とスペイン語の言語がサポートされています。詳細については、「[Azure Media Indexer Spanish](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)」をご覧ください。


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##関連リンク

[AIB ファイルを Azure Media Indexer および SQL Server で使用する](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=Sept15_HO2-->