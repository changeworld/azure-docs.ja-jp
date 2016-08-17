<properties pageTitle=".NET での Azure Media Services テレメトリ | Microsoft Azure" 
	description="この記事では、Azure Media Services テレメトリを使用する方法について説明します。" 
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
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# .NET での Azure Media Services テレメトリ
 
## 概要

Media Services テレメトリ/監視により、Media Services の顧客が、そのサービスのメトリック データにアクセスできます。現在のバージョンでは、"Channel" エンティティと "StreamingEndpoint" エンティティのテレメトリ データがサポートされています。テレメトリはコンポーネント レベルの粒度で構成でき、"Normal" と "Verbose" の 2 つの詳細レベルがあります。現在のバージョンでは "Normal" のみがサポートされます。

テレメトリが書き込まれるのは、顧客によって提供される Azure ストレージ アカウントのストレージ テーブルです (ストレージ アカウントは Media Services アカウントに接続されている必要があります)。テレメトリ システムでは、00:00 UTC に基づいて日が変わるごとに個別のテーブルが作成されます。たとえば、"TelemetryMetrics20160321" の "20160321" はテーブルが作成された日付です。つまり、日ごとに個別のテーブルが存在することになります。

テレメトリ システムではデータのリテンション期間を管理していないことに注意してください。古いテレメトリ データを削除するには、ストレージ テーブルを削除します。

テレメトリ データは、次のいずれかの方法で使用できます。

- Azure Table Storage から直接データを読み取ります (Storage SDK の使用など)。テレメトリのストレージ テーブルの説明については、[こちら](https://msdn.microsoft.com/library/mt742089.aspx)のトピックの「**Consuming telemetry information (テレメトリ情報の使用)**」を参照してください。

または

- Media Services .NET SDK のサポートを利用してストレージ データを読み取ります。このトピックでは、指定した AMS アカウントのテレメトリを有効にする方法と、Azure Media Services .NET SDK を使用してメトリックにクエリを実行する方法を説明します。

## Media Services アカウントのテレメトリの構成

テレメトリを有効にするには、次の手順を実行する必要があります。

- Media Services アカウントに接続されたストレージ アカウントの資格情報を取得します。
- 通知エンドポイントを作成します。**EndPointType** は **AzureTable** に設定し、endPointAddress はストレージ テーブルを指すように指定します。

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

## テレメトリ情報の使用

テレメトリは、Media Services アカウントのテレメトリを構成するときに指定したストレージ アカウントの Azure Storage テーブルに書き込まれます。テレメトリ システムでは、00:00 UTC に基づいて日が変わるごとに個別のテーブルが作成されます。たとえば、"TelemetryMetrics20160321" の "20160321" はテーブルが作成された日付です。つまり、日ごとに個別のテーブルが存在することになります。

次のメトリック情報について、テレメトリ テーブルにクエリを実行できます。このトピックで後ほど紹介する例では、Media Services .NET SDK を使用してメトリックにクエリを実行する方法を説明します。

### StreamingEndpoint ログ

次の StreamingEndPoint のメトリックに対してクエリを実行できます。

プロパティ|説明|値の例
---|---|---
**PartitionKey**|レコードのパーティション キーを取得します。|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|レコードの行キーを取得します。|00959\_00000
**AccountId**|Media Services アカウント ID を取得します。|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Media Services ストリーミング エンドポイント ID を取得します。|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|メトリックの監視時間を取得します。|1/20/16 23:44:01
**HostName**|ストリーミング エンドポイントのホスト名を取得します。|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|状態コードを取得します。|200
**ResultCode**|結果コードを取得します。|S\_OK
**RequestCount**|要求数を取得します。|3
**BytesSent**|送信バイト数を取得します。|2987358
**ServerLatency**|サーバーの待機時間 (ストレージを含む) を取得します。|129
**EndToEndLatency**|エンド ツー エンドの要求時間を取得します。|250


### ライブ チャネル ハートビート

次のライブ チャネル メトリックに対してクエリを実行できます。

プロパティ|説明|値の例
---|---|---
**PartitionKey**|レコードのパーティション キーを取得します。|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|レコードの行キーを取得します。|13872\_00005
**AccountId**|Media Services アカウント ID を取得します。|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Media Services チャネル ID を取得します。|
**ObservedTime**|メトリックの監視時間を取得します。|1/21/2016 20:08:49
**CustomAttributes**|カスタム属性を取得します。|
**TrackType**|トラックの種類を取得します。|video
**TrackName**|トラック名を取得します。|video
**Bitrate**|ビットレートを取得します。|785000
**IncomingBitrate**|受信ビットレートを取得します。|784548
**OverlapCount**|重複数を取得します。|0
**DiscontinuityCount**|不連続性の数を取得します。|0
**LastTimestamp**|最後のタイムスタンプを取得します。|1800488800
 
## 例  
	
次の例では、指定した AMS アカウントのテレメトリを有効にする方法と、Azure Media Services .NET SDK を使用してメトリックにクエリを実行する方法を説明します。

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
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

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

<!---HONumber=AcomDC_0803_2016-->