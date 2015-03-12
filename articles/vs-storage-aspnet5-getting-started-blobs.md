<properties 
	pageTitle="Azure Storage の使用" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [変更内容](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Azure Storage の使用 (ASP.NET 5 プロジェクト)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [キュー](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [テーブル](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。1 つの BLOB は任意のサイズにできます。BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。

使用を開始するには、Azure Storage アカウントを作成し、このストレージ内に 1 つ以上のコンテナーを作成する必要があります。たとえば、"Scrapbook" という名前のストレージを作成し、このストレージに、写真を格納するための "images" という名前のコンテナーと、音声ファイルを格納するための "audio" という名前のコンテナーを作成します。コンテナーを作成すると、個々の BLOB ファイルをコンテナーにアップロードできるようになります。プログラムを使用して BLOB を操作する方法の詳細については、「[.NET から BLOB ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET")」を参照してください。

ASP.NET 5 プロジェクトでプログラムを使用して BLOB にアクセスするには、次の項目を追加する必要があります (存在していない場合)。

1. プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. 次のコードを使用して構成設定を取得します。

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### ストレージ接続文字列を取得する
BLOB を使用した操作を行うには、BLOB を使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。ASP.NET 5 プロジェクトを使用する場合、次のコードに示すように、構成オブジェクトの get メソッドを呼び出して Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得することができます。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### コンテナーを作成する
ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。**CloudBlobClient** オブジェクトを使用して既存コンテナーへの参照を取得するか、CreateCloudBlobClient() メソッドを呼び出して新しいコンテナーを作成することができます。

次のコードは、新しい BLOB ストレージ コンテナーを作成する方法を示しています。このコードではまず **BlobClient** オブジェクトが作成され、ストレージ コンテナーの作成など、このオブジェクトが備える機能を使用できるようになります。オブジェクトの作成後、コードは "mycontainer" という名前のストレージ コンテナーを参照しようとします。この名前のコンテナーが見つからない場合は作成します。

**注:** ASP.NET 5 で Azure ストレージへの呼び出しを実行する API は、非同期です。詳細については、「[Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx)」を参照してください。次のコードは、非同期のプログラミング方法が使用されていると仮定しています。

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。コンテナー内のファイルをだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**注:** このコードはすべて、以下のセクションのコードの前に使用してください。

##### コンテナーに BLOB をアップロードする
BLOB ファイルをコンテナーにアップロードするには、コンテナーの参照を取得し、それを使用して BLOB の参照を取得します。BLOB の参照を取得したら、**UploadFromStreamAsync()** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##### コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、コンテナーの **ListBlobsSegmentedAsync()** メソッドを呼び出して、その中の BLOB やディレクトリを取得します。返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。BLOB の種類がわからない場合は、種類の確認を使うとどれにキャストすればよいかがわかります。次のコードは、コンテナー内の各項目の URI を取得して出力する方法を示しています。

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

BLOB コンテナーの内容を一覧表示する方法は他にもあります。詳細については、「[.NET から BLOB ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob)」を参照してください。

##### BLOB をダウンロードする
BLOB をダウンロードするには、まず BLOB の参照を取得し、次にその **DownloadToStreamAsync()** メソッドを呼び出します。次の例は、**DownloadToStreamAsync()** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

BLOB をファイルとして保存する方法は他にもあります。詳細については、「[.NET から BLOB ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs)」を参照してください。

##### BLOB を削除する
BLOB を削除するには、まず BLOB の参照を取得し、次にその **DeleteAsync()** メソッドを呼び出します。

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Azure Storage の詳細を確認する](http://azure.microsoft.com/documentation/services/storage/)
「[サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/azure/ff683677.aspx)」と「[ASP.NET 5](http://www.asp.net/vnext)」も参照してください。<!--HONumber=42-->
