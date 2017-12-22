---
title: "Azure Content Delivery Network で Azure Blob Storage の有効期限を管理する | Microsoft Docs"
description: "Azure CDN キャッシュで BLOB の有効期限を制御するオプションについて説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 6f82ae396a17f903a522c716f73a5f7d2de660e7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Azure Content Delivery Network で Azure Blob Storage の有効期限を管理する
> [!div class="op_single_selector"]
> * [Azure Web コンテンツ](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure BLOB Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Azure Storage の [Blob Storage サービス](../storage/common/storage-introduction.md#blob-storage)は、Azure ベースに元々あって Azure Content Delivery Network (CDN) と統合されたサービスの 1 つです。 パブリックにアクセス可能な BLOB コンテンツは、その有効期間 (TTL) が経過するまで、Azure CDN でキャッシュできます。 TTL は、配信元サーバーからの HTTP 応答の `Cache-Control` ヘッダーによって決まります。 この記事では、Azure Storage の BLOB で `Cache-Control` ヘッダーを設定する方法のいくつかを示します。

[CDN キャッシュ規則](cdn-caching-rules.md)を設定して、Azure Portal からキャッシュの設定を制御することもです。 1 つ以上のキャッシュ規則を設定し、キャッシュの動作を **[上書き]** または **[キャッシュのバイパス]** に設定すると、この記事で説明する最初に提供されるキャッシュ設定は無視されます。 全般的なキャッシュの概念については、「[キャッシュのしくみ](cdn-how-caching-works.md)」をご覧ください。

> [!TIP]
> BLOB に TTL を設定しないこともできます。 この場合は、Azure Portal でキャッシュ規則を設定していない限り、Azure CDN によって既定の 7 日間の TTL が自動的に適用されます。 この既定の TTL は、一般的な Web 配信の最適化に対してのみ適用されます。 大きなファイルの最適化に対する既定の TTL は 1 日、メディア ストリーミングの最適化に対する既定の TTL は 1 年です。
> 
> その他のファイルと BLOB へのアクセスを高速化する Azure CDN のしくみについて詳しくは、「[Azure Content Delivery Network (CDN) の概要](cdn-overview.md)」をご覧ください。
> 
> Azure Blob Storage について詳しくは、「[Blob Storage の概要](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)」をご覧ください。
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Azure PowerShell を使った Cache-Control ヘッダーの設定
[Azure PowerShell](/powershell/azure/overview) は、Azure の各種サービスを管理する最も簡単で最も強力な方法の 1 つです。 `Get-AzureStorageBlob` コマンドレットを使用して BLOB への参照を取得し、`.ICloudBlob.Properties.CacheControl` プロパティを設定します。 

For example:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> PowerShell は、[CDN プロファイルとエンドポイントの管理](cdn-manage-powershell.md)に使用することもできます。
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>.NET を使った Cache-Control ヘッダーの設定
.NET コードを使って BLOB の `Cache-Control` ヘッダーを設定するには、[.NET 用 Azure Storage クライアント ライブラリ](../storage/blobs/storage-dotnet-how-to-use-blobs.md)を使って、[CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) プロパティを設定します。

For example:

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
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> 「[Azure Blob Storage Samples for .NET ](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)」(.NET 用 Azure Blob Storage のサンプル) には、他にも多くの使用可能な .NET コード サンプルがあります。
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>他の方法を使った Cache-Control ヘッダーの設定

### <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー
[Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) を使うと、*CacheControl* のようなプロパティなどの Blob Storage リソースを表示して編集できます。 

Azure Storage Explorer で BLOB の *CacheControl* プロパティを更新するには、以下の操作を行います。
   1. BLOB を選択し、コンテキスト メニューから **[プロパティ]** を選択します。 
   2. 下にスクロールして *CacheControl* プロパティを表示させます。
   3. 値を入力し、**[保存]** をクリックします。


![Azure Storage Explorer のプロパティ](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure コマンド ライン インターフェイス
[Azure コマンド ライン インターフェイス](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) (CLI) では、コマンド ラインから Azure BLOB リソースを管理することができます。 Azure CLI で BLOB をアップロードするときにキャッシュ制御ヘッダーを設定するには、`-p` スイッチを使って *cacheControl* プロパティを設定します。 次の例では、TTL を 1 時間 (3,600 秒) に設定する方法を示します。
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\test.txt myContainer test.txt
```

### <a name="azure-storage-services-rest-api"></a>Azure ストレージ サービス REST API
[Azure ストレージ サービス REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) を使うと、要求で次の操作を使って *x-ms-blob-cache-control* プロパティを明示的に設定できます。
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Cache-Control ヘッダーのテスト
BLOB の TTL 設定を簡単に確認できます。 ブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使って、BLOB に `Cache-Control` 応答ヘッダーが含まれているかどうかをテストします。 [Wget](https://www.gnu.org/software/wget/)、[Postman](https://www.getpostman.com/)、[Fiddler](http://www.telerik.com/fiddler) などのツールを使って応答ヘッダーを確認することもできます。

## <a name="next-steps"></a>次のステップ
* [Azure CDN でクラウド サービスのコンテンツの有効期限を管理する方法を確認します](cdn-manage-expiration-of-cloud-service-content.md)
* [キャッシュの概念を学習する](cdn-how-caching-works.md)

