<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio の ASP.NET プロジェクトで Azure BLOB ストレージの使用を開始する方法" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="patshea123"/>

# Azure Storage の使用 (ASP.NET プロジェクト)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

>[AZURE.NOTE]この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用して ASP.NET app で Azure ストレージ アカウントを参照または作成した後に、Azure BLOB ストレージの使用を開始する方法について説明します。Azure テーブル ストレージの使用についてのより一般的な情報については、[「.NET から BLOB ストレージを使用する方法」](storage-dotnet-how-to-use-blobs.md)を参照してください。

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。1 つの BLOB は任意のサイズにできます。BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。

ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。ストレージ アカウントを作成した後、その内部に 1 つまたは複数のコンテナーを作成します。たとえば、"Scrapbook" という名前のストレージに、写真を格納するための "images" という名前の BLOB コンテナーと、音声ファイルを格納するための "audio" という名前の BLOB コンテナーを作成できます。コンテナーを作成すると、個々の BLOB ファイルをコンテナーにアップロードできるようになります。


この記事では、Azure BLOB ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは C# で記述され、.NET 用 Azure ストレージ クライアント ライブラリを使用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。

ASP.NET プロジェクトの詳細については、[「ASP.NET」](http://www.asp.net)を参照してください。

##Visual Studio サーバー エクスプローラーで BLOB コンテナーを作成する

[AZURE.INCLUDE [vs-create-blob-container-in-server-explorer](../../includes/vs-create-blob-container-in-server-explorer.md)]

##コードで BLOB コンテナーにアクセスする 

ASP.NET プロジェクトでプログラムを使用して BLOB にアクセスするには、次の項目を追加する必要があります (存在していない場合)。

1. プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;


2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    **注:** 上記のコードはすべて、以下のセクションに示すコードの前に使用してください。


3. ストレージ アカウント内の既存のコンテナーを参照する **CloudBlobClient** オブジェクトを取得します。

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

**注:** ASP.NET 5 で Azure ストレージへの呼び出しを実行する API の一部は非同期です。詳細については、[Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx)に関するページを参照してください。


## コードで BLOB コンテナーを作成する

**CloudBlobClient** を使用して、ストレージ アカウント内にコンテナーを作成することもできます。次のコードのようにして、`CreateIfNotExistsAsync()` への呼び出しを追加するだけです。

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “myNewContainer”.
    CloudBlobContainer container = blobClient.GetContainerReference("myNewContainer");

    // If “myNewContainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    





## コンテナーに BLOB をアップロードする

Azure BLOB Storage では、ブロック BLOB とページ BLOB がサポートされています。ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。BLOB の参照を取得したら、**UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、コンテナーの **ListBlobs** メソッドを使用して、その中の BLOB やディレクトリを取得できます。返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。次のコードは、`photos` コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    // Create the blob client. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

この例からわかるように、BLOB サービスにはコンテナー内のディレクトリの概念もあります。これは、BLOB をよりフォルダーに近い構造で整理できるようにするためです。たとえば、`photos` という名前のコンテナーに次の一連のブロック BLOB があったとします。

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

photos コンテナーの **ListBlobs** を呼び出すと (上の例を参照)、返されるコレクションには最上位レベルにあるディレクトリと BLOB を表す **CloudBlobDirectory** や **CloudBlockBlob** オブジェクトが含まれています。結果の出力は次のようになります。

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


必要に応じて、**ListBlobs** メソッドの **UseFlatBlobListing** パラメーターを **true** に設定することもできます。これで、ディレクトリに関係なく、すべての BLOB が **￼CloudBlockBlob￼￼** として返されるようになります。この場合、**ListBlobs** の呼び出しは次のようになります。

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

結果は次のようになります。

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

詳細については、[CloudBlobContainer.ListBlobs に関するページ][]を参照してください。

## BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。次の例は、**DownloadToStream** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

**DownloadToStream** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## BLOB を削除する

BLOB を削除するには、まず、BLOB の参照を取得し、次にその **Delete** メソッドを呼び出します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 


## BLOB をページで非同期に一覧表示する

多数の BLOB を一覧表示する場合や、1 回の一覧表示操作で返される結果の数を制御する場合には、BLOB の一覧を結果のページで表示できます。この例は、大きな結果のセットを返すために待機している間に実行がブロックされないように、結果をページで非同期に返す方法を示しています。

この例は、BLOB をフラットな一覧で表示しますが、**ListBlobsSegmentedAsync** メソッドの `useFlatBlobListing` パラメーターを `false` に設定することによって、階層化された一覧で表示することもできます。

サンプル メソッドは非同期メソッドを呼び出すため、その先頭を `async` キーワードにする必要があり、**Task** オブジェクトを返す必要があります。**ListBlobsSegmentedAsync** メソッドに対して指定された await キーワードは、一覧表示タスクが完了するまで、サンプル メソッドの実行を中断します。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 次のステップ

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]



  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 

<!---HONumber=July15_HO4-->