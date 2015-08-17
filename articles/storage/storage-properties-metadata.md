
<properties 
  pageTitle="ストレージ リソースのプロパティとメタデータの設定と取得 | Microsoft Azure" 
  description="Azure Storage リソースのプロパティとメタデータを設定し、取得する方法について説明します。" 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="08/04/2015" 
  ms.author="tamram"/>


# プロパティおよびメタデータの設定と取得 #

## 概要

Azure Storage のオブジェクトは、含まれるデータのほかにもシステムのプロパティとユーザー定義のメタデータをサポートします。

*   **システムのプロパティ。** システムのプロパティは、各ストレージ リソースに存在します。このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。これらは Azure ストレージ クライアント ライブラリで管理されます。  

*   **ユーザー定義のメタデータ。** ユーザー定義のメタデータは、名前と値のペアの形式で、特定のリソースで指定したメタデータです。メタデータを使用すると、ストレージ リソースで追加の値を格納できます。つまり、これらの値は、自身の目的のためだけに存在するため、リソースの動作には影響しません。

## プロパティの設定と取得

ストレージ リソースのプロパティとメタデータの値を取得するには、2 つの手順が必要です。値の読み取りができるようになる前に、**FetchAttributes** か **FetchAttributesAsync** メソッドのいずれかを呼び出して値を明確に取得する必要があります。

> [AZURE.IMPORTANT]ストレージ リソースのプロパティとメタデータの値は、いずれかの **FetchAttributes** メソッドを呼び出さない限り、設定されません。

BLOB でプロパティを設定するには、プロパティ値を指定してから、**SetProperties** または **SetPropertiesAsync** メソッドを呼び出します。

次のコード例では、コンテナーを作成し、プロパティ値をコンソール ウィンドウに書き込みます。

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## メタデータの設定と取得

メタデータは、BLOB またはコンテナーのリソースで 1 つ以上の名前と値のペアとして指定できます。メタデータを設定するには、リソースの **Metadata** コレクションに名前と値のペアを追加した後、**SetMetadata** メソッドを呼び出して値をサービスに保存します。

> [AZURE.NOTE]\: メタデータの名前は、C# 識別子の名前付け規則に従う必要があります。
 
メタデータを取得するには、BLOB またはコンテナーで **FetchAttributes** メソッドを呼び出して **Metadata** コレクションを設定した後、値を読み取ります。

次のコード例では、新しいコンテナーを作成してそのメタデータを設定した後、メタデータの値を読み取ります。

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## 関連項目  

- [Azure Storage クライアント ライブラリ リファレンス](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [.NET 用の BLOB ストレージの概要](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=August15_HO6-->