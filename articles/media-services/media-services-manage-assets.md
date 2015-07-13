<properties 
	pageTitle="Media Services でアセットを管理する方法" 
	description="メディア サービスでアセットを管理する方法について説明します。ジョブ、タスク、アクセス ポリシー、ロケーターなども管理できます。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#方法: ストレージ内のアセットを管理する

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」や「[Media Services のライブ ストリーミングのワークフロー](media-services-live-streaming-workflow.md)」シリーズの一部です。


メディア アセットを作成すると、 サーバー上のアセットにアクセスし、管理できます。サーバー上には、それ以外にも、ジョブ、タスク、アクセス ポリシー、ロケーターなど、メディア サービスのさまざまなオブジェクトが存在しますが、それらのオブジェクトも管理対象にすることができます。

次の例は、assetId でアセットを照会する方法を示しています。

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

サーバーで利用可能なすべてのアセットを一覧表示するには、次のメソッドを使ってアセット コレクションを反復処理し、各アセットの詳細を表示できます。
	
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

次のコード スニペットは、メディア サービス アカウントからすべてのアセットを削除します。アセットがプログラムに関連付けられている場合は、最初にプログラムを削除する必要があります。

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}

 

<!---HONumber=July15_HO1-->