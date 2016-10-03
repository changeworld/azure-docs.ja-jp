<properties
 pageTitle="Azure CDN で Azure Storage BLOB コンテンツの有効期限を管理する | Microsoft Azure"
 description="Azure CDN キャッシュで BLOB の有効期限を制御するオプションについて説明します。"
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# Azure CDN で Azure Storage BLOB コンテンツの有効期限を管理する

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services、ASP.NET、または IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure Storage BLOB サービス](cdn-manage-expiration-of-blob-content.md)

[Azure Storage](../storage/storage-introduction.md) の [BLOB サービス](../storage/storage-introduction.md#blob-storage)は、Azure ベースに元々あって Azure CDN と統合されたサービスの 1 つです。パブリックにアクセス可能な BLOB コンテンツは、その有効期間 (TTL) が経過するまで、Azure CDN でキャッシュできます。TTL は、Azure Storage からの HTTP 応答の [*Cache-control* ヘッダー](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)によって決まります。

>[AZURE.TIP] BLOB に TTL を設定しないこともできます。その場合は、Azure CDN が既定の 7 日間の TTL を自動的に適用します。
>
>その他のファイルと BLOB へのアクセスを高速化する Azure CDN のしくみの詳細については、「[Azure CDN の概要](./cdn-overview.md)」を参照してください。
>
>Azure Storage BLOB サービスの詳細については、[BLOB サービスの概念](https://msdn.microsoft.com/library/dd179376.aspx)に関するページを参照してください。

このチュートリアルでは、Azure Storage で TTL を設定する方法のいくつかを示します。

## Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) は、Azure の各種サービスを管理する最も簡単で最も強力な方法の 1 つです。`Get-AzureStorageBlob` コマンドレットを使用して BLOB への参照を取得し、`.ICloudBlob.Properties.CacheControl` プロパティを設定します。

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] PowerShell は、[CDN プロファイルとエンドポイントの管理](./cdn-manage-powershell.md)に使用することもできます。

## .NET 用 Azure Storage クライアント ライブラリ

.NET を使用して BLOB の TTL を設定するには、[.NET 用 Azure Storage クライアント ライブラリ](../storage/storage-dotnet-how-to-use-blobs.md)を使用して、[CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) プロパティを設定します。

```csharp
class Program
{
	const string connectionString = "<storage connection string>";
	static void Main()
	{
		// Retrieve storage account information from connection string
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
		
		// Create a blob client for interacting with the blob service.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		
		// Create a reference to the container
		CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

		// Create a reference to the blob
		CloudBlob blob = container.GetBlobReference("<blob name>");

		// Set the CacheControl property to expire in 1 hour (3600 seconds)
		blob.Properties.CacheControl = "public, max-age=3600";

		// Update the blob's properties in the cloud
		blob.SetProperties();
	}
}
```

>[AZURE.TIP] 「[Azure Blob Storage Samples for .NET (.NET 用Azure Blob Storage のサンプル)](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)」には、他にも多くの使用可能な .NET コード サンプルがあります。

## その他の方法

- [Azure コマンド ライン インターフェイス](../xplat-cli-install.md)

	BLOB をアップロードするときに、`-p` スイッチを使用して *cacheControl* プロパティを設定します。この例では、TTL を 1 時間 (3,600 秒) に設定します。

	```text
	azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
	```

- [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)

	[Put Blob](https://msdn.microsoft.com/ja-JP/library/azure/dd179451.aspx)、[Put Block List](https://msdn.microsoft.com/ja-JP/library/azure/dd179467.aspx)、または [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx) 要求で*x-ms-blob-cache-control* プロパティを明示的に設定します。

- サード パーティのストレージの管理ツール

	一部のサードパーティの Azure Storage 管理ツールでは、BLOB の *CacheControl* プロパティを設定できます。

## *Cache-Control* ヘッダーのテスト

BLOB の TTL は簡単に確認できます。お使いのブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使用して、BLOB に *Cache-control* 応答ヘッダーが含まれているかテストします。**wget**、[Postman](https://www.getpostman.com/)、[Fiddler](http://www.telerik.com/fiddler) などのツールを使用しても応答ヘッダーを確認できます。

## 次のステップ

- [*Cache-Control* ヘッダーに関するページを読みます](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)。
- [Azure CDN でクラウド サービスのコンテンツの有効期限を管理する方法を確認します。](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0921_2016-->