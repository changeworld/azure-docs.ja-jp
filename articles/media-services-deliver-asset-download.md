<properties 
	pageTitle="メディア アセットを配信する方法 - Azure" 
	description="Azure でメディア サービスにアップロードされたメディア アセットを配信するオプションについて説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>

#方法: ダウンロードによってアセットを配信する

この記事は、[メディア サービスのビデオ オンデマンド ワークフロー](media-services-video-on-demand-workflow.md) シリーズの一部です。  

このトピックでは、メディア サービスにアップロードされたメディア アセットを配信するためのオプションについて説明します。メディア サービスのコンテンツ配信には、さまざまな方法を適用できます。メディア アセットをダウンロードするか、ロケーターを使用してアクセスできます。メディア コンテンツは、別のアプリケーションまたは別のコンテンツ プロバイダーに送ることができます。パフォーマンスとスケーラビリティを高めるために、コンテンツ配信ネットワーク (CDN) を使用してコンテンツを配信することもできます。

この例では、メディア アセットを Media Services からローカル コンピューターにダウンロードする方法を紹介しています。このコードは、ジョブ ID によって Media Service アカウントに関連付けられたジョブのクエリを実行し、**OutputMediaAssets** コレクション (ジョブの実行の結果である 1 つまたは複数の出力メディア アセットのセット) にアクセスします。この例では、ジョブから出力メディア アセットをダウンロードしていますが、同じ方法で他のアセットをダウンロードすることもできます。

	
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
	    ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
	
	    BlobTransferClient blobTransfer = new BlobTransferClient
	    {
	        NumberOfConcurrentTransfers = 20,
	        ParallelTransferThreadCount = 20
	    };
	
	    var downloadTasks = new List&lt;Task&gt;();
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
  

<!--HONumber=52-->