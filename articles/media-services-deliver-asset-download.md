<properties urlDisplayName="Delivering Media Assets" pageTitle="メディア アセットを配信する方法 - Azure" metaKeywords="" description="Azure で Media Services にアップロードされたメディア アセットを配信するオプションについて説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>方法:ダウンロードによってアセットを配信する</h1>
この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、「[方法: ストレージ内のアセットを管理する](../media-services-manage-assets/)」を参照してください。

このトピックでは、メディア サービスにアップロードされたメディア アセットを配信するためのオプションについて説明します。メディア サービスのコンテンツ配信には、さまざまな方法を適用できます。メディア アセットをダウンロードするか、ロケーターを使用してアクセスすることができます。メディア コンテンツは、別のアプリケーションまたは別のコンテンツ プロバイダーに送ることができます。パフォーマンスとスケーラビリティを高めるために、Azure CDN などのコンテンツ配信ネットワーク (CDN: Content Delivery Network) を使用してコンテンツを配信することもできます。

この例では、メディア アセットをメディア サービスからダウンロードする方法を紹介しています。このコードは、ジョブ ID によってメディア サービス アカウントに関連付けられたジョブのクエリを実行し、**OutputMediaAssets** コレクション (ジョブの実行の結果である 1 つまたは複数の出力メディア アセットのセット) にアクセスします。この例では、ジョブから出力メディア アセットをダウンロードしていますが、同じ方法で他のアセットをダウンロードすることもできます。

<pre><code> 
// Download the output asset of the specified job to a local folder.
static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);

    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

	// Create a SAS locator to download the asset
    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 20,
        ParallelTransferThreadCount = 20
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>

アセットの配信の詳細については、以下を参照してください。
<ul>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129575.aspx">Media Services SDK for .NET を使用して資産を配信する</a></li>
<li><a href="http://msdn.microsoft.com/ja-jp/library/jj129578.aspx">Media Services REST API を使って資産を配信する</a></li>
</ul>

<h2>次のステップ</h2>
このトピックでは、Azure ストレージからのアセットのダウンロードについて説明しました。アセットを配信する他の方法の情報については、「[方法: ストリーミング コンテンツを配信する](../media-services-deliver-streaming-content/) 」トピックを参照してください。

<!--HONumber=35.1-->
