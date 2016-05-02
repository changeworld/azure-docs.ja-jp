<properties
	pageTitle="Azure Media Analytics で動作を検出する"
	description="Azure Media Motion Detector メディア プロセッサ (MP) を使用すると、長くて動きの少ないビデオから注目すべき部分を効率よく識別できます。"
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/14/2016"   
	ms.author="milanga;juliako;"/>
 
# Azure Media Analytics で動作を検出する

##概要

**Azure Media Motion Detector** メディア プロセッサ (MP) を使用すると、長くて動きの少ないビデオから注目すべき部分を効率よく識別できます。モーション検出は、ビデオでモーションが発生したセクションを識別するために、静止カメラ映像で使用できます。この機能は、イベントが発生したタイムスタンプと境界範囲のメタデータを含む JSON ファイルを生成します。

セキュリティ ビデオ フィードを対象とするこのテクノロジは、関連するイベントと、影や照明の変化などの誤検出に、モーションを分類できません。これにより、いつまでも続く関係のないイベントに煩わされることなくカメラ フィードからセキュリティ アラートを生成することができ、非常に長い監視ビデオから関心のある瞬間を抽出できます。

**Azure Media Motion Detector** MP は現在プレビュー段階です。

ここでは、**Azure Media Motion Detector** の詳細を説明し、Media Services SDK for .NET でのその使用方法を示します。

##Motion Detector の入力ファイル

ビデオ ファイルです。現在サポートされている形式は MP4、MOV、WMV です。

##Motion Detector の出力ファイル

モーション検出ジョブは、ビデオ内のモーション アラートおよびそのカテゴリが記述されている JSON ファイルを出力資産で返します。このファイルには、ビデオで検出されたモーションの時間と継続時間に関する情報が含まれます。

現時点では、モーション検出は一般的なモーション カテゴリのみをサポートしており、出力ファイルでは***タイプ 2*** と示されています。

X 座標と Y 座標およびサイズは、0.0 ～ 1.0 に正規化された浮動小数点数を使用して表記されます。この値にビデオの高さと幅の解像度を掛けることで、検出されたモーションの領域の境界ボックスを取得できます。

各出力はフラグメントに分割され、それがさらに間隔に分割されて、ビデオ内のデータが定義されます。フラグメントの長さは一定である必要はなく、モーションがまったく検出されないときは長時間続くこともあります。

Motion Detector API は、固定背景ビデオ (監視ビデオなど) 内に動く対象物があると、インジケーターを提供します。Motion Detector は、照明と影の変化などの誤アラームが減るようにトレーニングされます。アルゴリズムの現在の制限事項としては、暗視ビデオ、半透明の対象物、小さな対象物などがあります。

###<a id="output_elements"></a>出力 JSON ファイルの要素

出力 JSON ファイルの要素を次の表に示します。

要素|説明
---|---
バージョン|Video API のバージョンを示します。
タイムスケール|ビデオの 1 秒あたりの "ティック数" です。
Offset|タイムスタンプの時間オフセットです ("ティック数")。Video API のバージョン 1.0 では、これは常に 0 になります。今後サポートされるシナリオでは、変更される可能性があります。
Framerate|ビデオの 1 秒あたりのフレーム数です。
Width、Height|ビデオの幅と高さです (ピクセル)。
開始|"ティック数" での開始タイムスタンプです。
時間|イベントの長さです ("ティック数")。
間隔|イベント内の各エントリの間隔です ("ティック数")。
イベント|各イベント フラグメントには、その期間内で検出されたモーションが含まれます。
型|現在のバージョンでは、これは常に一般モーションを示す "2" です。このラベルにより、Video API は将来のバージョンでモーションを柔軟に分類できます。
RegionID|前述のように、このバージョンではこの値は常に 0 です。このラベルにより、Video API は将来のバージョンでさまざまな領域のモーションを柔軟に検出できるます。
地域|モーションに注意するビデオ内の領域を示します。現在のバージョンの Video API では、領域を指定できません。代わりに、ビデオ全体でモーションが検出されます。<br/>- ID は領域の範囲を表します。現バージョンでは ID 0 だけです。<br/>- Rectangle (四角形) はモーションに注意する領域の形状を表します。このバージョンでは常に四角形です。<br/>- 領域には、X、Y 座標と幅、高さの寸法があります。X および Y 座標は、0.0 ～ 1.0 に正規化されたスケール内の領域の左上の XY 座標を表します。幅と高さは、0.0 ～ 1.0 に正規化されたスケール内の領域のサイズを表します。現バージョンでは、X、Y、幅と高さは常に 0、0、1、1 の固定値です。<br/>- Fragments: メタデータはフラグメントと呼ばれる異なるセグメントに分割されます。各フラグメントには、開始、継続時間、間隔数、およびイベントが含まれます。イベントのないフラグメントは、その開始時間と継続時間中にモーションが検出されなかったことを意味します。
角かっこ|各角かっこは、イベント内の 1 つの間隔を表します。その間隔の角かっこが空の場合、モーションが検出されなかったことを意味します。
 

##タスクの構成 (プリセット)

**Azure Media Motion Detector** でタスクを作成するときは、構成プリセットを指定する必要があります。現時点では、Azure Media Motion Detector の構成プリセットではオプションを設定できません。次の最小構成プリセットを提供する必要があります。

	{"version":"1.0"}

##ビデオと Motion Detector 出力の例

###実際のモーションがある例

[実際のモーションがある例](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###JSON 出力

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###誤検出の例

[誤検出 (証明の変化) がある例:](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###JSON 出力

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##制限事項

- サポートされている入力ビデオ形式は、MP4、MOV、WMV です。
- モーション検出は、固定背景のビデオに最適化されています。アルゴリズムは、照明の変化や影などの誤ったアラームを減らすことに焦点が当てられています。
- 暗視ビデオ、半透明の対象物、小さな対象物など、技術的な問題のために検出できない可能性のあるモーションがあります。


## サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
1. 次の JSON プリセットを含む構成ファイルに基づくビデオ モーション検出タスクのジョブを作成します。 
					
		{
		    "version": "1.0"
		}

1. 出力 JSON ファイルをダウンロードします。
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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

##関連リンク

[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0420_2016-->