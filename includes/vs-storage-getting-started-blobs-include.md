#####コンテナーを作成する
ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。**CloudBlobClient** オブジェクトを使用して既存コンテナーへの参照を取得するか、CreateCloudBlobClient() メソッドを呼び出して新しいコンテナーを作成することができます。

次のコードは、新しい BLOB ストレージ コンテナーを作成する方法を示しています。このコードではまず **BlobClient** オブジェクトが作成され、ストレージ コンテナーの作成など、このオブジェクトが備える機能を使用できるようになります。オブジェクトの作成後、コードは "mycontainer" という名前のストレージ コンテナーを参照しようとします。 この名前のコンテナーが見つからない場合は作成します。

	// Create a blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Get a reference to a container named “mycontainer.”
	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// If “mycontainer” doesn’t exist, create it.
	container.CreateIfNotExists();

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。コンテナー内のファイルをだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。

	container.SetPermissions(
    	new BlobContainerPermissions { PublicAccess = 
	    BlobContainerPublicAccessType.Blob }); 


**注:** このコード ブロックは、以下のセクションで扱うコードの前に使用してください。

#####コンテナーに BLOB をアップロードする
BLOB ファイルをコンテナーにアップロードするには、コンテナーの参照を取得し、それを使用して BLOB の参照を取得します。BLOB の参照を取得したら、**UploadFromStream()** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

	// Get a reference to a blob named "myblob".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
	
	// Create or overwrite the "myblob" blob with the contents of a local file
	// named “myfile”.
	using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
	{
    	blockBlob.UploadFromStream(fileStream);
	}

#####コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、コンテナーの **ListBlobs()** メソッドを呼び出して、その中の BLOB やディレクトリを取得します。返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。BLOB の種類がわからない場合は、種類の確認を使うとどれにキャストすればよいかがわかります。次のコードは、"photos" コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

	// Get a reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop through items in the container and output the length and URI for each 
	// item.
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

BLOB コンテナーの内容を一覧表示する方法は他にもあります。詳細については、「[.NET から BLOB ストレージを使用する方法](../articles/storage/storage-dotnet-how-to-use-blobs.md/#list-blob)」を参照してください。

#####BLOB をダウンロードする
BLOB をダウンロードするには、まず BLOB の参照を取得し、次にその DownloadToStream() メソッドを呼び出します。次の例は、DownloadToStream() メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	blockBlob.DownloadToStream(fileStream);
	}

BLOB をファイルとして保存する方法は他にもあります。詳細については、「[.NET から BLOB ストレージを使用する方法](../articles/storage/storage-dotnet-how-to-use-blobs.md/#download-blobs)」を参照してください。

#####BLOB を削除する
BLOB を削除するには、まず BLOB の参照を取得し、次にその Delete() メソッドを呼び出します。

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	blockBlob.Delete();

[Azure Storage の詳細](http://azure.microsoft.com/documentation/services/storage/)をご覧ください。[サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/azure/ff683677.aspx)に関するページもご覧ください。

<!---HONumber=62-->