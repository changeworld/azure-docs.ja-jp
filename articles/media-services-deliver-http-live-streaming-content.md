<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# 方法: Apple HLS ストリーミング コンテンツを配信する

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[「方法: ストリーミング コンテンツを配信する][]」を参照してください。

このトピックでは、ロケーターを作成して、メディア サービスの配信元サーバー上にある Apple HTTP ライブ ストリーミング (HLS) コンテンツをストリーミングする方法について説明します。その方法を使用すると、Apple HLS コンテンツの URL を構築して Apple iOS デバイスに提供し、再生することができます。ロケーター URL を構築する方法は基本的には変わりません。配信元サーバー上の Apple HLS ストリーミング アセット パスに対するロケーターを構築した後、ストリーミング コンテンツのマニフェストにリンクした完全な URL を構築します。

以下のコード例は、HLS ストリーミング アセットへの参照が取得済みであることを前提としています。コード中では、**assetToStream** という名前の変数で参照されます。このコードを実行してアセットの配信元ロケーターを生成した後、その結果として得られた URL を iOS デバイス (iPad、iPhone など) で使用し、ストリーミング コンテンツを再生することができます。

Apple HLS ストリーミング コンテンツへのロケーターを構築するには、次の手順を実行します。

1.  アセット内のマニフェスト ファイルへの参照を取得します。
2.  アクセス ポリシーを定義します。
3.  CreateLocator を呼び出して元のロケーターを作成します。
4.  マニフェスト ファイルの URL を構築します。

この手順を実装したコード例を次に示します。

    static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
    {
        // Get a reference to the asset you want to stream.
        IAsset assetToStream = GetAsset(targetAssetID);

        // Get a reference to the HLS streaming manifest file from the  
        // collection of files in the asset. 
        var theManifest =
                            from f in assetToStream.AssetFiles
                            where f.Name.EndsWith(".ism")
                            select f;

        // Cast the reference to a true IAssetFile type. 
        IAssetFile manifestFile = theManifest.First();
        IAccessPolicy policy = null;
        ILocator originLocator = null;

        // Create a 30-day readonly access policy. 
        policy = _context.AccessPolicies.Create("Streaming HLS Policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

        // Create a URL to the HLS streaming manifest file. Use this for playback
        // in Apple iOS streaming clients.
        string urlForClientStreaming = originLocator.Path
            + manifestFile.Name + "/manifest(format=m3u8-aapl)";
        Console.WriteLine("URL to manifest for client streaming: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine();

        // Return the locator. 
        return originLocator;
    }

アセットの配信の詳細については、以下を参照してください。

-   [Media Services SDK for .NET を使用して資産を配信する][]
-   [Media Services REST API を使って資産を配信する][]

</p>
## 次のステップ

このトピックで、Azure メディア サービスの使用に関するトピックは終了します。メディア サービス開発用のコンピューターのセットアップ、および一般的なプログラミング タスクの実行について説明しました。メディア サービスのプログラミングの詳細については、次のリソースを参照してください。

-   [Azure メディア サービスのドキュメントに関するページ][]
-   [Media Services SDK for .NET の概要に関するページ][]
-   [Media Services SDK for .NET を使ったアプリケーション構築に関するページ][]
-   [Azure メディア サービス REST API を使用したアプリケーション構築に関するページ][]
-   [メディア サービス フォーラム][]
-   [メディア サービス アカウントの監視方法に関するページ][]
-   [メディア サービスのコンテンツの管理方法に関するページ][]

  [「方法: ストリーミング コンテンツを配信する]: http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409
  [Media Services SDK for .NET を使用して資産を配信する]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Media Services REST API を使って資産を配信する]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Azure メディア サービスのドキュメントに関するページ]: http://go.microsoft.com/fwlink/?linkid=245437
  [Media Services SDK for .NET の概要に関するページ]: http://go.microsoft.com/fwlink/?linkid=252966
  [Media Services SDK for .NET を使ったアプリケーション構築に関するページ]: http://go.microsoft.com/fwlink/?linkid=247821
  [Azure メディア サービス REST API を使用したアプリケーション構築に関するページ]: http://go.microsoft.com/fwlink/?linkid=252967
  [メディア サービス フォーラム]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [メディア サービス アカウントの監視方法に関するページ]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/
  [メディア サービスのコンテンツの管理方法に関するページ]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/
