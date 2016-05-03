<properties 
	pageTitle="Media Services からストリーミング コンテンツを配信する方法" 
	description="ストリーミング URL の構築に使用するロケーターを作成する方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	authors="juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016"
	ms.author="juliako"/>


#方法: ストリーミング コンテンツを配信する
 
> [AZURE.SELECTOR]
- [REST ()](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [ポータル](media-services-manage-content.md#publish)

##概要

オンデマンド ストリーミング ロケーターを作成してストリーミング URL を構築することで、アダプティブ ビットレート MP4 セットをストリーミングできます。[資産をエンコードする](media-services-encode-asset.md)トピックで、アダプティブ ビットレート MP4 セットへのエンコード方法を説明しています。

>[AZURE.NOTE]コンテンツが暗号化されている場合は、ロケーターを作成する前に、資産配信ポリシーを構成します ([この](media-services-dotnet-configure-asset-delivery-policy.md)トピックをご覧ください)。

また、オンデマンド ストリーミング ロケーターを使って、プログレッシブ ダウンロードができる MP4 ファイルの URL を作成できます。

このトピックでは、オンデマンド ストリーミング ロケーターを作成して資産を発行し、 Smooth、MPEG DASH、HLS ストリーミング URL を作成する方法について説明します。また、プログレッシブ ダウンロードを行う URL を作成する方法についても説明します。
  	 
##オンデマンド ストリーミング ロケーターを作成する

オンデマンド ストリーミング ロケーターを作成して URL を取得するには、次の手順に従います。

   1. コンテンツが暗号化されている場合は、アクセス ポリシーを定義します。
   2. オンデマンド ストリーミング ロケーターを作成します。
   3. ストリーミングする場合は、資産のストリーミング マニフェスト ファイル (.ism) を取得します。 
   		
	プログレッシブ ダウンロードをする場合は、資産内の MP4 ファイルの名前を取得します。  
   4. マニフェスト ファイルまたは MP4 ファイルへの URL を作成します。 
   

###Media Services .NET SDK を使用する 

ストリーミング URL を構築する

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
      	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
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
	

>[AZURE.NOTE]SSL 接続経由でコンテンツのストリーミングもできます。そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。

プログレッシブ ダウンロード URL を作成します。

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


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##関連項目

[資産をダウンロードする](media-services-deliver-asset-download.md)[資産配信ポリシーを構成する](media-services-dotnet-configure-asset-delivery-policy.md)

<!---HONumber=AcomDC_0420_2016-->