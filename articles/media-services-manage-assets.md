<properties urlDisplayName="Manage Assets in Media Services" pageTitle="Media Services でアセットを管理する方法 - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />




<h1>方法:ストレージ内のアセットを管理する</h1>

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、「[方法: アセットを保護する](../media-services-protect-asset/)」を参照してください。

メディア アセットを作成してメディア サービスにアップロードした後、サーバー上のそのアセットにアクセスして管理することができます。サーバー上には、それ以外にも、ジョブ、タスク、アクセス ポリシー、ロケーターなど、メディア サービスのさまざまなオブジェクトが存在しますが、それらのオブジェクトも管理対象にすることができます。

次の例は、assetId でアセットを照会する方法を示しています。 
<pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre> 

サーバーで利用可能なすべてのアセットを一覧表示するには、次のメソッドを使ってアセット コレクションを反復処理し、各アセットの詳細を表示できます。
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
次のコード スニペットは、メディア サービス アカウントからすべてのアセットを削除します。
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

アセットの管理の詳細については、以下を参照してください。
<ul>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129589.aspx">Media Services SDK for .NET で資産を管理する</a></li>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129583.aspx">Media Services REST API を使って資産を管理する</a></li></ul>


<h2>次のステップ</h2>
これで、アセットの管理方法を学習できました。次は、「[How to: Deliver an Asset by Download (方法: ダウンロードによってアセットを配信する)](../media-services-deliver-asset-download/) 」に進みます。

<!--HONumber=35_1-->
