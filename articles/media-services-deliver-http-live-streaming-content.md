<properties 
	pageTitle="Apple HTTP ライブ ストリーミング (HLS) を配信する方法 - Azure" 
	description="メディア サービスの配信元サーバー上にある Apple HTTP ライブ ストリーミング (HLS) コンテンツへのロケーターを作成する方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1>方法: Apple HLS ストリーミング コンテンツを配信する</h1>

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、「[方法: ストリーミング コンテンツを配信する](media-services-deliver-streaming-content.md).」をご覧ください

このトピックでは、ロケーターを作成して、メディア サービスの配信元サーバー上にある Apple HTTP ライブ ストリーミング (HLS) コンテンツをストリーミングする方法について説明します。その方法を使用すると、Apple HLS コンテンツの URL を構築して Apple iOS デバイスに提供し、再生できます。ロケーター URL を構築する方法は基本的には変わりません。配信元サーバー上の Apple HLS ストリーミング アセット パスに対するロケーターを構築した後、ストリーミング コンテンツのマニフェストにリンクした完全な URL を構築します。

以下のコード例は、HLS ストリーミング アセットへの参照が取得済みであることを前提としています。コード中では、**assetToStream** という名前の変数で参照されます。このコードを実行してアセットの配信元ロケーターを生成した後、その結果として得られた URL を iOS デバイス (iPad、iPhone など) で使用し、ストリーミング コンテンツを再生できます。

Apple HLS ストリーミング コンテンツへのロケーターを構築するには、次の手順を実行します。

   1. アセット内のマニフェスト ファイルへの参照を取得します。
   2. アクセス ポリシーを定義します。
   3. CreateLocator を呼び出して元のロケーターを作成します。
   4. マニフェスト ファイルの URL を構築します。

この手順を実装したコード例を次に示します。

<pre><code>
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
</code></pre>

アセットの配信の詳細については、以下をご覧ください。
<ul>
<li><a href="http://msdn.microsoft.com/library/jj129575.aspx">Media Services SDK for .NET によるアセットの配信</a></li>
<li><a href="http://msdn.microsoft.com/library/jj129578.aspx">Media Services REST API を使用したアセットの配信</a></li>
</ul>

<h2>次のステップ</h2>

このトピックで、Azure メディア サービスの使用に関するトピックは終了します。メディア サービス開発用のコンピューターのセットアップ、一般的なプログラミング タスクの実行について説明しました。メディア サービスのプログラミングの詳細については、次のリソースをご覧ください。

-   [Azure メディア サービスのドキュメント][]
-   [Media Services SDK for .NET の概要][]
-   [Media Services SDK for .NET によるアプリケーション構築][]
-   [Azure Media Services REST API によるアプリケーション構築][]
-   [Media Services フォーラム][]
-	[Media Services アカウントの監視方法](media-services-monitor-services-account.md)
-	[メディア コンテンツの管理方法](media-services-manage-content.md)

[Azure メディア サービスのドキュメント]: http://go.microsoft.com/fwlink/?linkid=245437
[Media Services SDK for .NET の概要]: http://go.microsoft.com/fwlink/?linkid=252966
[Azure Media Services REST API によるアプリケーション構築]: http://go.microsoft.com/fwlink/?linkid=252967
[Media Services SDK for .NET によるアプリケーション構築]: http://go.microsoft.com/fwlink/?linkid=247821
[Media Services フォーラム]: http://social.msdn.microsoft.com/Forums/MediaServices/threads


<!--HONumber=52-->