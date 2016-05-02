<properties
	pageTitle="Azure Media Indexer 2 プレビューによるメディア ファイルのインデックス作成"
	description="Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。このトピックでは、Media Indexer 2 プレビューの使用方法について説明します。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/14/2016"   
	ms.author="adsolank;juliako;"/>


# Azure Media Indexer 2 プレビューによるメディア ファイルのインデックス作成

##概要

**Azure Media Indexer 2 プレビュー** メディア プロセッサー (MP) を使用して、メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックを生成できます。前のバージョンの [Azure Media Indexer](media-services-index-content.md) と比較すると、**Azure Media Indexer 2 プレビュー**は、インデックス作成が高速化され、より多くの言語をサポートします。英語、スペイン語、フランス語、ドイツ語、イタリア語、中国語、ポルトガル語、アラビア語などがサポートされています。

**Azure Media Indexer 2 プレビュー** MP は現在プレビュー段階です。

このトピックでは、**Azure Media Indexer 2 プレビュー**を使用してインデックス作成ジョブを作成する方法を示します。

>[AZURE.NOTE]次の考慮事項が適用されます。
>
>Indexer 2 は、Azure China と Azure Government ではサポートされません。
>
>このプレビューは最大 10 分の処理に制限されていますが、すべてのお客様が無料で使用できます。
>
>コンテンツのインデックスを作成する場合は、クリアな (バック グラウンド ミュージック、ノイズ、特殊効果、またはマイク ヒスノイズなどがない) 音声機能を持つメディア ファイルを使用してください。適切なコンテンツの例としては、記録された会議、講義またはプレゼンテーションなどがあります。ムービー、テレビ番組、混合音声とサウンド効果を含むもの、バックグラウンド ノイズ (ヒスノイズ) を含む記録状態が良好でないコンテンツは、インデックス作成に適しません。


ここでは、**Azure Media Indexer 2 プレビュー**の詳細を説明し、Media Services SDK for .NET でのその使用方法を示します。

##入力ファイルと出力ファイル

###入力ファイル

オーディオまたはビデオ ファイル

###出力ファイル

インデックス作成ジョブでは、クローズド キャプション ファイルを次の形式で生成できます。

- **SAMI**
- **TTML**
- **WebVTT**

これらの形式のクローズド キャプション (CC) ファイルを使用して、聴覚障がいを持つユーザーがオーディオ ファイルとビデオ ファイルにアクセスできるようにします。

##タスクの構成 (プリセット)

**Azure Media Indexer 2 プレビュー*でインデックス作成タスクを作成するときは、構成プリセットを指定する必要があります。

次の JSON は、使用可能なパラメーターを設定します。

	{
	  "version":"1.0",
	  "Features":
	    [
	       {
	       "Options": {
	            "Formats":["WebVtt","ttml"],
	            "Language":"enUs",
	            "Type":"RecoOptions"
	       },
	       "Type":"SpReco"
	    }]
	}

##サポートされている言語  

Azure Media Indexer 2 プレビューは、次の言語の音声をテキストに変換する機能をサポートします (タスク構成に言語を指定するときは、次に示すかっこ内の 4 文字のコードを使用します)。

- 英語 [EnUs]
- スペイン語 [EsEs]
- 中国語 [ZhCn]
- フランス語 [FrFr]
- ドイツ語 [DeDe]
- イタリア語 [ItIt]
- ポルトガル語 [PtBr]
- アラビア語 (エジプト語) [ArEg]


## サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
1. 次の JSON プリセットを含む構成ファイルに基づくインデックス作成タスクを持つジョブを作成します。
			
		{
		  "version":"1.0",
		  "Features":
		    [
		       {
		       "Options": {
		            "Formats":["WebVtt","ttml"],
		            "Language":"enUs",
		            "Type":"RecoOptions"
		       },
		       "Type":"SpReco"
		    }]
		}

1. 出力ファイルをダウンロードします。
	
		using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace IndexContent
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run indexing job.
		            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\Indexer\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
		        }
		
		        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Indexing Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Indexing Job");
		
		            // Get a reference to Azure Media Indexer 2 Preview.
		            string MediaProcessorName = "Azure Media Indexer 2 Preview";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
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
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
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
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
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
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		    }
		}


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## 関連リンク

[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0420_2016-->