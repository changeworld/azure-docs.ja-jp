<properties pageTitle="メディア サービスからストリーミング コンテンツを配信する方法-Azure" 
	description="ストリーミング URL の構築に使用するロケーターの作成方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>


<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="juliako"/>


#方法:ストリーミング コンテンツを配信する


この記事は、[Media Services Video on Demand workflow (メディア サービス ビデオ オン デマンドのワークフロー)](../media-services-video-on-demand-workflow) と [Media Services Live Streaming workflow (メディア サービス ライブ ストリーミングのワークフロー)](../media-services-live-streaming-workflow) シリーズの一部です。  

##概要

オンデマンド ストリーミング ロケーターを作成してストリーミング URI を構築することで、アダプティブ ビットレート MP4 セットをストリーミングできます。「[アセットをエンコードする](../media-services-encode-asset)」トピックでは、アダプティブ ビットレート MP4 セットのエンコード方法を説明しています。ロケーターを作成する前に、[この](../media-services-dotnet-configure-asset-delivery-policy)トピックにある手順でアセットの配信ポリシーを構成する必要があります。 

また、オンデマンド ストリーミング ロケーターを使用して、プログレッシブ ダウンロード可能な MP4 ファイルを指す URL を構築できます。  

このトピックでは、アセットを発行して、スムーズ、MPEG、DASH、HLS ストリーミングの URL を構築するためのオンデマンド ストリーミング ロケーターの作成方法を示します。また、プログレッシブ ダウンロード URL を構築する方法についても説明します。 
  	 
##オンデマンド ストリーミング ロケーターを作成する

オンデマンド ストリーミング ロケーターを作成して URL を取得するには、次が必要です。

   1. アクセス ポリシーを定義する。
   2. オンデマンド ストリーミング ロケーターを作成する。
   3. ストリーミングを予定している場合は、アセットのストリーミング マニフェスト ファイル (.ism) を取得します。 
   		
	プログレッシブ ダウンロードを予定している場合は、アセットの MP4 の名前を取得します。  
   4. マニフェスト ファイルまたは MP4 ファイルへの URL を構築します。 
   

###Media Services .NET SDK を使用する 

ストリーミング URL を構築する 

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

コードの出力:
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

プログレッシブ ダウンロードの URL を構築する 

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

コードの出力:
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

###Media Services .NET SDK Extensions の使用

次のコードでは、ロケーターを作成してアダプティブのスムーズ ストリーミング、HLS、MPEG-DASH URL を生成する.NET SDK Extensions のメソッドを呼び出します。

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);

<!--HONumber=45--> 
