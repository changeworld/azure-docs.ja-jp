---
title: .NET を使用して BLOB を一覧表示する - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage アカウントのコンテナー内の BLOB を一覧表示する方法について説明します。 サンプル コードは、ディクショナリまたはフォルダーで整理されているかのように BLOB をフラット リスト (階層) で一覧表示する方法を示しています。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 3deb3bd0ff66bb31691c4c380595771e3bf2c681
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75691068"
---
# <a name="list-blobs-with-net"></a>.NET を使用して BLOB を一覧表示する

BLOB をコードから一覧表示する際には、Azure Storage からの結果の取得方法を管理するためのオプションをいくつか指定できます。 各結果セットで返す結果の数を指定し、後続のセットを取得できます。 名前がその文字または文字列から始まる BLOB を返すようにプレフィックスを指定できます。 また、フラット リスト構造 (階層) で BLOB を一覧表示できます。 階層リストでは、フォルダーに整理されたかのように BLOB が返されます。 

この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage/client)を使用して BLOB を一覧表示する方法について説明します。  

## <a name="understand-blob-listing-options"></a>BLOB の一覧表示オプションについて

ストレージ アカウント内の BLOB を一覧表示するには、次のいずれかのメソッドを呼び出します。

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

コンテナー内の BLOB を一覧表示するには、次のいずれかのメソッドを呼び出します。

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

これらのメソッドのオーバーロードを使用すると、一覧表示操作によって BLOB がどのように返されるかを管理するための、追加のオプションを指定できます。 以降のセクションでは、これらのオプションについて説明します。

### <a name="manage-how-many-results-are-returned"></a>返される結果の数を管理する

既定では、一覧表示操作では一度に最大 5000 の結果が返されます。 返される結果セットを減らすには、いずれかの **ListBlobs** メソッドを呼び出すときに、`maxresults` パラメーターに 0 以外の値を指定します。

一覧表示操作が 5000 を超える BLOB を返す場合、または一覧表示操作によってストレージ アカウント内のコンテナーのサブセットが返されるように `maxresults` の値を指定した場合、Azure Storage は BLOB の一覧と共に*継続トークン*を返します。 継続トークンは、Azure Storage から次の結果セットを取得するために使用できる非透過の値です。

コードでは、継続トークンの値をチェックして、それが null かどうかを確認します。 継続トークンが null の場合、結果セットは完了しています。 継続トークンが null でない場合は、一覧表示操作をもう一度呼び出し、継続トークンを渡して次の結果セットを取得し、継続トークンが null になるまでそれを繰り返します。

### <a name="filter-results-with-a-prefix"></a>プレフィックスを使用して結果をフィルター処理する

コンテナーの一覧をフィルター処理するには、 `prefix`パラメーターの文字列を指定します。 プレフィックス文字列には、1 つ以上の文字を含めることができます。 Azure Storage は、名前がそのプレフィックスで始まる BLOB だけを返します。

### <a name="return-metadata"></a>メタデータを返す

結果と共に BLOB のメタデータを返すには、[BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) 列挙型の **Metadata** 値を指定します。 Azure Storage は、返される各 BLOB にメタデータを追加します。そのため、BLOB のメタデータを取得するために、いずれかの **FetchAttributes** メソッドをこのコンテキストで呼び出す必要はありません。

### <a name="flat-listing-versus-hierarchical-listing"></a>フラットな一覧表示と階層的な一覧表示

Azure Storage の BLOB は、(従来のファイル システムのような) 階層的なパラダイムではなく、フラットなパラダイムで組織化されます。 ただし、フォルダー構造を模倣するために、BLOB を*仮想ディレクトリ*に組織化することができます。 仮想ディレクトリは BLOB 名の一部を形成し、区切り文字によって示されます。

BLOB を仮想ディレクトリに組織化するには、BLOB 名に区切り文字を使用します。 既定の区切り文字はスラッシュ (/) ですが、区切り文字として任意の文字を指定できます。

区切り記号を使用して BLOB に名前を付ける場合、BLOB を階層的に一覧表示することを選択できます。 階層的な一覧表示操作の場合、Azure Storage は、親オブジェクトの下にあるすべての仮想ディレクトリと BLOB を返します。 従来のファイル システムをプログラムで走査するのと同じような方法で、一覧表示操作を再帰的に呼び出して階層を走査することができます。

## <a name="use-a-flat-listing"></a>フラットな一覧表示を使用する

既定では、一覧表示操作はフラットな一覧表示で BLOB を返します。 フラットな一覧表示では、BLOB は仮想ディレクトリによって整理されません。

次の例では、フラットな一覧表示を使用し、オプションのセグメント サイズを指定して、指定されたコンテナー内の BLOB を一覧表示し、BLOB 名をコンソール ウィンドウに出力します。

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

出力例は次のようになります。

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>階層的な一覧表示を使用する

一覧表示操作を階層的に呼び出すと、Azure Storage は、階層の最初のレベルに仮想ディレクトリと BLOB を返します。 各仮想ディレクトリの [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) プロパティは、再帰呼び出しでプレフィックスを渡して次のディレクトリを取得できるように設定されます。

BLOB を階層的に一覧表示するには、一覧表示メソッドの `useFlatBlobListing` パラメーターを **false** に設定します。

次の例では、フラットな一覧表示を使用し、オプションのセグメント サイズを指定して、指定されたコンテナー内の BLOB を一覧表示し、BLOB 名をコンソール ウィンドウに出力します。

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

出力例は次のようになります。

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> BLOB のスナップショットは、階層的な一覧表示操作では一覧表示できません。

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>次のステップ

- [BLOB を一覧表示する](/rest/api/storageservices/list-blobs)
- [BLOB リソースの列挙](/rest/api/storageservices/enumerating-blob-resources)
