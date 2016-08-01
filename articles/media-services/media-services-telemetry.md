<properties pageTitle=".NET での Azure Media Services テレメトリ | Microsoft Azure" 
	description="この記事では、Azure Media Services テレメトリを使用する方法について説明します。" 
	services="" 
	documentationCenter=""
	authors="juliako" />

# .NET での Azure Media Services テレメトリ

## 概要

Media Services テレメトリ/監視により、Media Services の顧客が、そのサービスのメトリック データにアクセスできます。現在のバージョンでは、"Channel" エンティティと "StreamingEndpoint" エンティティのテレメトリ データがサポートされています。テレメトリはコンポーネント レベルの粒度で構成でき、"Normal" と "Verbose" の 2 つの詳細レベルがあります。現在のバージョンでは "Normal" のみがサポートされます。

テレメトリが書き込まれるのは、顧客によって提供される Azure Storage アカウントです (ストレージ アカウントは Media Services アカウントに接続されている必要があります)。指定されたストレージ アカウントの Azure Storage テーブルにテレメトリが書き込まれます。テレメトリ システムでは、00:00 UTC に基づいて日が変わるごとに個別のテーブルが作成されます。たとえば、"TelemetryMetrics20160321" の "20160321" はテーブルが作成された日付です。つまり、日ごとに個別のテーブルが存在することになります。

テレメトリ システムには、データ保持も、古いレコードの自動削除も用意されていません。そのため、古いレコードを管理および削除する必要があります。日ごとに個別のテーブルがあると、古いレコードを簡単に削除できます。古いテーブルをただ削除するだけです。

このトピックでは、指定された AMS サービスのテレメトリを有効にする方法と、.NET を使用してメトリックに対してクエリを実行する方法について説明します。

## Media Services アカウントのテレメトリの構成

テレメトリを有効にするには、次の手順を実行する必要があります。

- Media Services アカウントに接続されたストレージ アカウントの資格情報を取得します。
- 通知エンドポイントを作成します。**EndPointType** は **AzureTable** に設定し、endPontAddress はストレージ テーブルを指すように指定します。

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 監視するサービスの監視構成設定を作成します。許可される監視構成設定は 1 つだけです。
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });


## StreamingEndpoint ログ

###使用可能なメトリック

次の StreamingEndPoint のメトリックに対してクエリを実行できます。

- **PartitionKey** は、レコードのパーティション キーを取得します。
- **RowKey** は、レコードの行キーを取得します。
- **AccountId** は、Media Services アカウント ID を取得します。
- **AccountId** は、Media Services ストリーミング エンドポイント ID を取得します。
- **ObservedTime** は、メトリックの監視時間を取得します。
- **HostName** は、ストリーミング エンドポイントのホスト名を取得します。
- **StatusCode** は、状態コードを取得します。
- **ResultCode** は、結果コードを取得します。
- **RequestCount** は、要求数を取得します。
- **BytesSent** は、送信バイト数を取得します。
- **ServerLatency** は、サーバーの待機時間を取得します。
- **EndToEndLatency** は、エンド ツー エンドの要求時間を取得します。

###ストリーミング エンドポイントのクエリ結果の例

![ストリーミング エンドポイント クエリ](media/media-services-telemetry/media-services-telemetry01.png)


## ライブ チャネル ハートビート

###使用可能なメトリック

次のライブ チャネル メトリックに対してクエリを実行できます。

- **PartitionKey** は、レコードのパーティション キーを取得します。
- **RowKey** は、レコードの行キーを取得します。
- **AccountId** は、Media Services アカウント ID を取得します。
- **ChannelId** は、Media Services チャネル ID を取得します。
- **ObservedTime** は、メトリックの監視時間を取得します。
- **CustomAttributes** は、カスタム属性を取得します。
- **TrackType** は、トラックの種類を取得します。
- **TrackName** は、トラック名を取得します。
- **Bitrate** は、ビットレートを取得します。
- **IncomingBitrate** は、受信ビットレートを取得します。
- **OverlapCount** は、重複数を取得します。
- **DiscontinuityCount** は、不連続性の数を取得します。
- **LastTimestamp** は、最後のタイムスタンプを取得します。
 
###ライブ チャネルのクエリ結果の例

![ストリーミング エンドポイント クエリ](media/media-services-telemetry/media-services-telemetry01.png)

## StreamingEndpoint メトリックの例
		
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##次のステップ
 
Azure Media Services のラーニング パスをご覧ください。AMS で提供される優れた機能の学習に役立ちます。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0720_2016-->