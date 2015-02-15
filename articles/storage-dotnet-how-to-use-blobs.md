<properties 
	pageTitle=".NET から BLOB ストレージを使用する方法 | Azure" 
	description="Microsoft Azure BLOB ストレージを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法について説明します。サンプルは C# で記述されています。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# .NET から BLOB ストレージを使用する方法

このガイドでは、Azure Blob ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。例は C\# で記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、「[次のステップ][]」のセクションを参照してください。

> [AZURE.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンは、ストレージ クライアント ライブラリ 4.x です。これは、[NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) から、または [Azure SDK for .NET](/ja-jp/downloads/) の一部として提供されています。ストレージ クライアント ライブラリの入手方法の詳細については、[方法: プログラムで BLOB ストレージにアクセスする][] を参照してください。

##目次

-   [BLOB ストレージとは][]
-   [概念][]
-   [Azure のストレージ アカウントの作成][]
-   [ストレージ接続文字列の設定][]
-   [方法:プログラムで BLOB ストレージにアクセスする][]
-   [方法:コンテナーを作成する][]
-   [方法:コンテナーに BLOB をアップロードする][]
-   [方法:コンテナー内の BLOB を一覧表示する][]
-   [方法:BLOB をダウンロードする][]
-   [方法:BLOB を削除する][]
-   [方法:BLOB をページで非同期に一覧表示する][]
-   [次のステップ][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>方法:プログラムで BLOB ストレージにアクセスする

###アセンブリの取得### 

NuGet を使用して  `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得することをお勧めします。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、<a href="http://www.windowsazure.com/ja-jp/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは  `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` ディレクトリにインストールされます。

###名前空間宣言

プログラムを使用して Azure ストレージにアクセスするすべての C\# ファイルの冒頭部分に、次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

必ず  `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

###接続文字列の取得

**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Azure プロジェクト テンプレートを使用している場合や、Microsoft.WindowsAzure.CloudConfigurationManager への参照がある場合は、**CloudConfigurationManager** 型を使用して Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したとおり `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 型では、BLOB ストレージ サービス内に格納されているコンテナーと BLOB を表すオブジェクトを取得できます。次のコードは、前に取得したストレージ アカウント オブジェクトを使用して、**CloudBlobClient** オブジェクトを作成します。

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib 依存

.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData]、[Edm]、および [Spatial] です。

## <a name="create-container"> </a>方法:コンテナーを作成する

Azure Storage のどの BLOB もコンテナーに格納する必要があります。この例は、コンテナーがない場合に、コンテナーを作成する方法を示しています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。コンテナー内のファイルをだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

## <a name="upload-blob"> </a>方法:コンテナーに BLOB をアップロードする

Azure BLOB Storage では、ブロック BLOB とページ BLOB がサポートされています。ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。BLOB の参照を取得したら、**UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

##<a name="list-blob"> </a>方法:コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、コンテナーの **ListBlobs** メソッドを使って、その中の BLOB やディレクトリを取得します。返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。次のコードは、`photos` コンテナー内の各項目の URI を取得して出力する方法を示しています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

この例からわかるように、BLOB サービスにはコンテナー内のディレクトリの概念もあります。これは、BLOB をよりフォルダーに近い構造で整理できるようにするためです。たとえば、`photos` というコンテナーに次のブロック BLOB があったとします。

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

'photos' コンテナーの **ListBlobs** を呼び出すと (上の例を参照)、返されるコレクションには最上位レベルにあるディレクトリおよび BLOB を表す **CloudBlobDirectory** および **CloudBlockBlob** オブジェクトが含まれています。結果の出力は次のようになります。

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


必要に応じて、**ListBlobs** メソッドの **UseFlatBlobListing** パラメーターを  
**true** に設定することもできます。そうすると、ディレクトリに関係なく、すべての BLOB が **CloudBlockBlob** として返されるようになります。この場合、**ListBlobs** の呼び出しは次のようになります。

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

詳細については、[CloudBlobContainer.ListBlobs][] に関するページを参照してください。

## <a name="download-blobs"> </a>方法:BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。次の例は、**DownloadToStream** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

##<a name="delete-blobs"> </a>方法:BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次にその **Delete** メソッドを呼び出します。

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


##<a name="list-blobs-async"> </a>方法:BLOB をページで非同期に一覧表示する

多数の BLOB を一覧表示する場合や、1 回の一覧表示操作で返される結果の数を制御する場合には、BLOB の一覧を結果のページで表示できます。この例は、大きな結果のセットを返すために待機している間に実行がブロックされないように、結果をページで非同期に返す方法を示しています。

この例は、BLOB をフラットな一覧で表示しますが、**ListBlobsSegmentedAsync** メソッドの  `useFlatBlobListing` パラメーターを  `false` に設定することによって、階層化された一覧で表示することもできます。

サンプル メソッドは非同期メソッドを呼び出すため、その先頭を  `async` キーワードにする必要があり、また、**Task** オブジェクトを返す必要があります。**ListBlobsSegmentedAsync** メソッドに対して指定された await キーワードは、一覧表示タスクが完了するまで、サンプル メソッドの実行を中断します。

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。
<ul>
<li>利用可能な API の詳細については、BLOB サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Azure Storage を使用して実行できるさらに高度なタスクについては、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx">Azure のデータの格納とアクセス</a>」を参照してください。</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK</a> を使用して Azure Storage で作業するためのコードの記述を簡略化する方法を説明します。</li>
<li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>構造化データの格納には、<a href="/ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/">テーブル ストレージ</a>を使用します。</li>
    <li>非構造化データの格納には、<a href="/ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/">キュー ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/ja-jp/documentation/articles/sql-database-dotnet-how-to-use/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [次のステップ]: #next-steps
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法:プログラムで BLOB ストレージにアクセスする]: #configure-access
  [方法:コンテナーを作成する]: #create-container
  [方法:コンテナーに BLOB をアップロードする]: #upload-blob
  [方法:コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法:BLOB をダウンロードする]: #download-blobs
  [方法:BLOB を削除する]: #delete-blobs
  [方法:BLOB をページで非同期に一覧表示する]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx
  [.NET クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [空間]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=42-->
