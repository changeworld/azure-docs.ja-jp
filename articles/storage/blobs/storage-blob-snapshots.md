---
title: .NET での BLOB スナップショットの作成と管理 - Azure Storage
description: BLOB の読み取り専用スナップショットを作成して、特定の時点での BLOB データをバックアップする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 17cd57fbcf9b1c14fb275a070bdefdd1282c4d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370527"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>.NET での BLOB スナップショットの作成と管理

スナップショットは、ある時点で作成された読み取り専用の BLOB です。 スナップショットは BLOB をバックアップするのに便利です。 この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage?view=azure-dotnet)を使用して BLOB スナップショットを作成および管理する方法について説明します。

## <a name="about-blob-snapshots"></a>BLOB スナップショットについて

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

スナップショットが作成された日時を示す **DateTime** 値が BLOB の URI に追加される点を除き、BLOB のスナップショットはベース BLOB とまったく同じです。 たとえば、ページ BLOB の URI が `http://storagesample.core.blob.windows.net/mydrives/myvhd` の場合、スナップショットの URI は `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` のようになります。

> [!NOTE]
> すべてのスナップショットがベース BLOB の URI を共有します。 ベース BLOB とスナップショットの唯一の違いは、追加される **DateTime** 値です。
>

BLOB に対するスナップショットの数に制限はありません。 スナップショットは、明示的に削除されるまで保持されます。つまり、スナップショットはベース BLOB よりも長く存続できません。 ベース BLOB に関連付けられたスナップショットを列挙して、現在のスナップショットを追跡できます。

BLOB のスナップショットを作成すると、BLOB のシステム プロパティが同じ値でスナップショットにコピーされます。 また、スナップショットの作成時に別のメタデータを指定しない限り、ベース BLOB のメタデータもスナップショットにコピーされます。 作成したスナップショットの読み取り、コピー、削除はできますが、変更はできません。

ベース BLOB に関連付けられているリースはスナップショットにコピーされません。 スナップショットはリースを取得することはできません。

VHD ファイルは、VM ディスクの現時点の情報と状態の格納に使用します。 ディスクを VM 内から切断するか、VM をシャットダウンしてから、その VHD ファイルのスナップショットを撮ることができます。 このスナップショットを後に使用して、その時点での VHD ファイルを取得して VM を再作成することができます。

## <a name="create-a-snapshot"></a>スナップショットの作成

ブロック BLOB のスナップショットを作成するには、次のいずれかの方法を使用します。

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

次のコード例は、スナップショットを作成する方法を示しています。 この例では、スナップショットの作成時に別のメタデータを指定しています。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>スナップショットの削除

BLOB を削除するには、まずその BLOB のスナップショットをすべて削除する必要があります。 スナップショットは個別に削除することも、コピー元 BLOB が削除されたときにすべてのスナップショットを削除するように指定することもできます。 スナップショットがまだ存在する BLOB を削除しようとすると、エラーが発生します。

BLOB スナップショットを削除するには、次のいずれかの BLOB 削除方法を使用して、[DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) 列挙型を含めます。

- [削除](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

次のコード例は、.NET で BLOB とそのスナップショットを削除する方法を示しています。`blockBlob` は [CloudBlockBlob][dotnet_CloudBlockBlob] 型のオブジェクトです。

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>スナップショットに絶対 URI を返す

次のコード例では、スナップショットを作成し、プライマリの場所の絶対 URI を書き込みます。

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>スナップショットの課金方法について

スナップショット (BLOB の読み取り専用コピー) を作成すると、別途データ ストレージ料金がアカウントに課金される場合があります。 不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

### <a name="important-billing-considerations"></a>課金に関する重要な考慮事項

スナップショットの作成時に考慮する必要のある重要なポイントを以下に示します。

- ストレージ アカウントには、一意のブロックまたはページに対する料金が発生します。そのブロックまたはページが BLOB とスナップショットのどちらに含まれているかは関係ありません。 BLOB に関連付けられているスナップショットについてアカウントに追加料金が発生するのは、そのベースになっている BLOB を更新したときです。 ベース BLOB を更新すると、BLOB はスナップショットから分化します。 この場合、各 BLOB またはスナップショットの一意のブロックまたはページに対して課金されます。
- ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。 そのブロックに割り当てられているブロック ID とデータが、スナップショット側と同じであっても変わりません。 ブロックが再度コミットされると、そのブロックはスナップショット内の対応するブロックから分化し、そのデータに対する料金が発生します。 これは、まったく同じデータでページ BLOB 内のページを更新した場合にも当てはまります。
- [UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、[UploadFromByteArray][dotnet_UploadFromByteArray] のいずれかのメソッドを呼び出してブロック BLOB を置き換えると、その BLOB 内のすべてのブロックが置き換えられます。 その BLOB に関連付けられているスナップショットがある場合、ベース BLOB とスナップショットのすべてのブロックが分化し、両方の BLOB のすべてのブロックが課金対象となります。 これは、ベース BLOB 内のデータとスナップショット内のデータとがまったく同一であっても変わりません。
- 2 つのブロックに格納されているデータが同一であるかどうかを判断する方法は、Azure Blob service には用意されていません。 アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。 料金は一意のブロックに対して発生するため、スナップショットがある BLOB を更新すると、一意のブロックが増え、追加料金が発生することを考慮することが重要です。

### <a name="minimize-cost-with-snapshot-management"></a>スナップショット管理によりコストを最小限に抑える

不要な料金を回避するために、スナップショットを慎重に管理することをお勧めします。 次のベスト プラクティスに従えば、スナップショットの記憶域から発生するコストを最小限に抑えられます。

- まったく同じデータで更新する場合も含め、BLOB を更新するときは必ずその BLOB に関連付けられているスナップショットを削除してから作成し直すようにします (アプリケーションの設計上、スナップショットを維持しなければならない場合を除く)。 BLOB のスナップショットを削除してから作成し直すことにより、BLOB とスナップショットの分化を確実に防ぐことができます。
- BLOB のスナップショットを維持する場合は、[UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、または [UploadFromByteArray][dotnet_UploadFromByteArray] の呼び出しによる BLOB の更新を行わないようにします。 これらのメソッドを呼び出すと、BLOB 内のすべてのブロックが置き換えられ、ベース BLOB とスナップショットが大幅に分化します。 代わりに、[PutBlock][dotnet_PutBlock] メソッドと [PutBlockList][dotnet_PutBlockList] メソッドを使用して、最小数のブロックのみを更新するようにしてください。

### <a name="snapshot-billing-scenarios"></a>スナップショットの課金シナリオ

ブロック BLOB とそのスナップショットについて料金が発生するしくみを次のシナリオで説明します。

#### <a name="scenario-1"></a>シナリオ 1

シナリオ 1 では、スナップショットが作成されてからベース BLOB は更新されていません。したがって、料金の発生対象は、一意のブロックである 1、2、3 のみです。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>シナリオ 2

シナリオ 2 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 更新されたのはブロック 3 です。格納されているデータと ID が同じでも、スナップショット内のブロック 3 とは異なります。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>シナリオ 3

シナリオ 3 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、スナップショット側はブロック 3 のままです。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>シナリオ 4

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。 このため、8 つある一意のブロックすべてについての料金がアカウントに課金されます。 このシナリオは、[UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、[UploadFromByteArray][dotnet_UploadFromByteArray] などの更新メソッド を使用した場合に発生する可能性があります。これは、これらのメソッドによって BLOB の内容がすべて置き換えられるためです。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>次のステップ

- 仮想マシン (VM) ディスク スナップショットの操作の詳細については、「[増分スナップショットを使用した Azure 非管理 VM ディスクのバックアップ](../../virtual-machines/windows/incremental-snapshots.md)」を参照してください。

- BLOB ストレージのその他の使用例については、「[Azure のコード サンプル](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)」を参照してください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
