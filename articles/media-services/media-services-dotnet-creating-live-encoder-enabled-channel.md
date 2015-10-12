<properties 
	pageTitle=".NET SDK を使用して、シングル ビットレートからマルチ ビットレート ストリーミングへのライブ エンコーディングを実行するチャネルを作成する" 
	description="このチュートリアルでは、シングル ビットレートのライブ ストリームを受信してマルチ ビットレート ストリームにエンコードするチャネルを作成する手順について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/29/2015"
	ms.author="juliako"/>


#.NET SDK を使用して、シングル ビットレートからマルチ ビットレート ストリーミングへのライブ エンコーディングを実行するチャネルを作成する

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

##概要

このチュートリアルでは、シングル ビットレートのライブ ストリームを受信してマルチ ビットレート ストリームにエンコードする**チャネル**を作成する手順について説明します。

>[AZURE.NOTE]ライブ エンコードが有効になっているチャネルに関連する概念的な情報については、「[シングル ビットレートからアダプティブ ビットレート ストリームへのライブ エンコードを実行するチャネルの作成](media-services-manage-live-encoder-enabled-channels.md)」を参照してください。

>[AZURE.NOTE]Media Services .NET SDK バージョン 3.2.0.0 以降を使用する必要があります。

##一般的なライブ ストリーミング シナリオ

次の手順では、一般的なライブ ストリーミング アプリケーションの作成に関連するタスクを示します。

1. ビデオ カメラをコンピューターに接続します。オンプレミスのライブ エンコーダーを起動して構成します。このエンコーダーはシングル ビットレート ストリームを RTMP、スムーズ ストリーミング、RTP (MPEG-TS) のいずれかで出力できます。詳しくは、「[Azure Media Services RTMP サポートおよびライブ エンコーダー](http://go.microsoft.com/fwlink/?LinkId=532824)」をご覧ください。

この手順は、チャネルを作成した後でも実行できます。

1. チャネルを作成し、起動します。

1. チャネルの取り込み URL を取得します。

取り込み URL は、ライブ エンコーダーがチャネルにストリームを送信する際に使用されます。1.チャネルのプレビュー URL を取得します。

この URL を使用して、チャネルがライブ ストリームを正常に受信できることを確認します。

2. アセットを作成します。
3. 再生時にアセットを動的に暗号化する場合は、次の手順を実行します。

1. 	コンテンツ キーを作成します。
1. 	コンテンツ キー承認ポリシーの構成
1. アセット配信ポリシーを構成します (動的パッケージと動的暗号化で使用)。
3. プログラムを作成し、作成したアセットを使用するよう指定します。
1. OnDemand ロケーターを作成して、プログラムに関連付けられたアセットを発行します。

コンテンツをストリームするストリーミング エンドポイントに少なくとも 1 つのストリーミング予約ユニットがあることを確認します。1.ストリーミングとアーカイブの開始を準備するときにプログラムを開始します。2.必要に応じて、ライブ エンコーダーは、広告の開始を信号通知できます。広告が出力ストリームに挿入されます。1.イベントのストリーミングとアーカイブを停止するときにプログラムを停止します。1.プログラムを削除し、資産を削除 (オプション) します。

##このトピックの内容

このトピックでは Media Services .NET SDK を使用したチャネルとプログラムでさまざまな操作を実行する方法を示します。多くの操作は実行時間が長いため、長時間の操作を管理する .NET API が使用されます。

このトピックではその方法を示します。

1. チャネルを作成し、起動します。実行時間の長い API が使用されます。
1. チャネルの取り込み (入力) エンドポイントを取得します。このエンドポイントは、シングル ビットレートのライブ ストリームを送信できるエンコーダーに提供される必要があります。
1. プレビューのエンドポイントを取得します。このエンドポイントを使用して、ストリームをプレビューします。
1. コンテンツを格納するために使用するアセットを作成します。この例に示すように、アセットの配信のポリシーを構成する必要もあります。
1. プログラムを作成し、以前作成したアセットを使用するよう指定します。プログラムを起動します。実行時間の長い API が使用されます。
1. アセットのロケーターを作成し、コンテンツがパブリッシュされて、クライアントにストリームできるようにします。
1. スレートを表示および非表示にします。広告を起動および停止します。実行時間の長い API が使用されます。
1. チャネルと関連付けられているすべてのリソースをクリーンアップします。

>[AZURE.NOTE]ライブ イベントの最大推奨時間は 8 時間です。チャネルを長時間実行する必要がある場合は、amslived@microsoft.com にお問い合わせください。

##前提条件
チュートリアルを完了するには次のものが必要です。

- このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](azure.microsoft.com)を参照してください。
- Media Services アカウント。Media Services アカウントを作成するには、「[アカウントの作成](media-services-create-account.md)」を参照してください。
- Visual Studio 2010 SP1 以降。
- シングル ビットレートのライブ ストリームを送信できる Web カメラとエンコーダー。

##With Media Services SDK for .NET を使用して開発を行うためにセットアップします。

1. Visual Studio を使用して、コンソール アプリケーションを作成します。
1. Media Services NuGet パッケージを使用して、コンソール アプリケーションに .NET 用 Media Services SDK を追加します。

##Media Services への接続
ベスト プラクティスとして、Media Services 名とアカウント キーを格納するために app.config ファイルを使用する必要があります。

>[AZURE.NOTE]名前とキーの値を確認するには、Azure ポータルに移動して、Media Services アカウントを選択し、ポータル ウィンドウの下にある [キーの管理] アイコンをクリックします。各テキスト ボックスの横にあるアイコンをクリックすると、値がシステム クリップボードにコピーされます。

app.config ファイルに appSettings セクションを追加し、Media Services のアカウント名とアカウント キーに値を設定します。


<?xml version="1.0"?> <configuration> <appSettings> <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" /> <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" /> </appSettings> </configuration>
	 
	

##コード例

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Newtonsoft.Json.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
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
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // Get a thumbnail preview of a live feed.
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Encoding = CreateChannelEncoding()
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("SlateTest.jpg");
	            assetFile.Upload("SlateTest.jpg");
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), 0, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation();
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}
	



##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

 

<!---HONumber=Oct15_HO1-->