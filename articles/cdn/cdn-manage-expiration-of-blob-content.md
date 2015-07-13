<properties 
 pageTitle="Azure Content Delivery Network (CDN) で BLOB コンテンツの有効期限を管理する方法" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>


#Azure Content Delivery Network (CDN) で BLOB コンテンツの有効期限を管理する方法  

有効期限 (TTL) の期間中に頻繁にアクセスされる BLOB では、Azure CDN のキャッシュを最大限に活用できます。BLOB は、TTL の期間中キャッシュ内に保持され、その期間が経過した後、BLOB サービスによって更新されます。このプロセスが繰り返されます。

TTL を制御するためのオプションが 2 つあります。

1.	キャッシュの値を設定しないで、既定の TTL である 7 日を使用します。 
2.	*x-ms-blob-cache-control* プロパティを **Put Blob**、**Put Block List**、または **Set Blob Properties** 要求で明示的に設定します。または Azure マネージ ライブラリを使用して [BlobProperties.CacheControl](http://msdn.microsoft.com/library/microsoft.windowsazure.storageclient.blobproperties.cachecontrol.aspx) プロパティを設定します。このプロパティを設定すると、BLOB の *Cache-Control* ヘッダーの値が設定されます。ヘッダーまたはプロパティの値には、秒単位で適切な値を指定する必要があります。たとえば、キャッシュの最大期間を 1 年に設定する場合は、要求ヘッダーとして `x-ms-blob-cache-control: public, max-age=31556926` を指定できます。キャッシュ ヘッダーの設定の詳細については、[HTTP/1.1 仕様](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)を参照してください。  

CDN を使用してキャッシュするすべてのコンテンツは、公開されているアクセス可能な BLOB として、Azure ストレージ アカウントに格納する必要があります。Azure BLOB サービスの詳細については、**BLOB サービスの概念**に関するページを参照してください。

BLOB サービスでコンテンツを操作する方法はいくつかあります。

-	**Azure マネージ ライブラリ リファレンス**で提供されるマネージ API を使用する。
-	サード パーティのストレージの管理ツールを使用する。
-	Azure Storage サービスの REST API を使用する。  

次のコード例はコンソール アプリケーションの例です。このアプリケーションでは、Azure マネージ ライブラリを使用して、コンテナーを作成し、そのコンテナーにパブリック アクセスの権限を設定して、コンテナー内に BLOB を作成します。また、BLOB の Cache-Control ヘッダーを設定して明示的に目的の更新間隔を指定します。

上で示したように CDN が有効になっていることが前提となっており、作成される BLOB は CDN によってキャッシュされます。必ず、独自のストレージ アカウントとアクセス キーを使用して、アカウントの資格情報を指定してください。

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;
	
	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");
	            
	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);
	            
	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	
	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();
	
	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);
	
	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");
	
	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }
	
	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

BLOB が CDN 固有の URL を介して利用できることをテストします。前述の BLOB の URL は次のようになります。

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

必要に応じて、**wget** や Fiddler などのツールを使用して要求と応答の詳細を調べることができます。

##関連項目

[Azure Content Delivery Network (CDN) でクラウド サービスのコンテンツの有効期限を管理する方法](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=62-->