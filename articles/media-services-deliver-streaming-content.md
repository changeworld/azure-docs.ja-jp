<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# 方法: ストリーミング コンテンツを配信する

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[「方法: ダウンロードによってアセットを配信する][]」を参照してください。

メディア コンテンツをメディア サービスからダウンロードする以外にも、アダプティブ ビットレート ストリーミングを使ってコンテンツを配信することができます。たとえば、直接の URL (ロケーター) を作成して、メディア サービスの配信元サーバー上のコンテンツをストリーミングすることができます。Microsoft Silverlight などのクライアント アプリケーションは、ロケーターからストリーミング コンテンツを直接再生できます。

以下の例は、ジョブによって生成された出力アセットの配信元ロケーターを作成する方法を示したものです。このコード例は、スムーズ ストリーミング ファイルを含んだアセットへの参照が取得済みであることを前提としています。コード中では、**assetToStream** という名前の変数で参照されます。

ストリーミング コンテンツの配信元ロケーターを作成するには、次の手順を実行します。

1.  アセット内のストリーミング マニフェスト ファイル (.ism) への参照を取得します。
2.  アクセス ポリシーを定義します。
3.  CreateLocator メソッドを呼び出して配信元ロケーターを作成します。
4.  マニフェスト ファイルの URL を構築します。

この手順を実装したコード例を次に示します。

    private static ILocator GetStreamingOriginLocator( string targetAssetID){ // Get a reference to the asset you want to stream. IAsset assetToStream = GetAsset(targetAssetID);
    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;

<p>
}
</code>

</pre>
</p>
アセットの配信の詳細については、以下を参照してください。

-   [Media Services SDK for .NET を使用して資産を配信する][]
-   [Media Services REST API を使って資産を配信する][]

</p>
## 次のステップ

これまで、Azure Storage からのダウンロードとスムーズ ストリーミングの使用によるメディアの配信について説明しました。次の [HLS コンテンツを配信する方法][]に関するトピックでは、Apple HTTP Live Streaming (HLS) を使用したストリーミング コンテンツの配信について説明します。

  [「方法: ダウンロードによってアセットを配信する]: http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409
  [Media Services SDK for .NET を使用して資産を配信する]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Media Services REST API を使って資産を配信する]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [HLS コンテンツを配信する方法]: http://go.microsoft.com/fwlink/?LinkId=301817
