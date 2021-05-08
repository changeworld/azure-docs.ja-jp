---
title: 論理的に削除された BLOB を管理および復元する
titleSuffix: Azure Storage
description: Azure portal または Azure Storage クライアント ライブラリを使用して、論理的に削除された BLOB およびスナップショットを管理および復元します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555898"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>論理的に削除された BLOB を管理および復元する

BLOB の論理的な削除を使用すると、削除されたデータがシステムに一定の期間保持されることにより、個々の BLOB とその複数バージョン、スナップショット、およびメタデータが誤った削除や上書きから保護されます。 保持期間中は、BLOB を削除時の状態に復元できます。 保持期間が過ぎると、BLOB オブジェクトは完全に削除されます。 BLOB の論理的な削除の詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure portal を使用して論理的に削除された BLOB を管理する

Azure portal を使用して、論理的に削除された BLOB とスナップショットを表示および復元できます。

### <a name="view-deleted-blobs"></a>削除された BLOB を表示する

論理的に削除された BLOBは、既定では Azure portal に表示されません。 論理的に削除された BLOB を表示するには、コンテナーの **[概要]** ページに移動し、 **[削除された Blob を表示]** 設定を切り替えます。 論理的に削除された BLOB は、 **[削除済み]** の状態で表示されます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="論理的に削除された BLOB を Azure portal で一覧表示する方法を示すスクリーンショット":::

次に、BLOB の一覧から、削除済みの BLOB を選択して、そのプロパティを表示します。 **[概要]** タブで、BLOB の状態が **[削除済み]** に設定されていることを確認します。 ポータルには、BLOB が完全に削除されるまでの日数も表示されます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Azure portal での論理的に削除された BLOB のプロパティを示すスクリーンショット":::

### <a name="view-deleted-snapshots"></a>削除されたスナップショットを表示する

BLOB を削除すると、その BLOB に関連するスナップショットもすべて削除されます。 論理的に削除された BLOB にスナップショットがある場合は、削除されたスナップショットもポータルに表示できます。 論理的に削除された BLOB のプロパティを表示し、 **[スナップショット]** タブに移動して、 **[削除されたスナップショットを表示]** を切り替えます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="スクリーンショットの表示":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>バージョン管理が無効になっている場合に論理的に削除されたオブジェクトを復元する

BLOB のバージョン管理が有効になっていない場合に論理的に削除された BLOB を Azure portal で復元するには、最初に BLOB のプロパティを表示し、 **[概要]** タブの **[削除の取り消し]** ボタンを選択します。BLOB を復元すると、論理的な削除の保持期間中に削除されたすべてのスナップショットも復元されます。

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="論理的に削除された BLOB を Azure portal で復元する方法を示すスクリーンショット":::

論理的に削除されたスナップショットをベース BLOB に昇格させるには、最初に BLOB の論理的に削除されたスナップショットが復元されていることを確認します。 ベース BLOB 自体が論理的に削除されていない場合でも、 **[削除の取り消し]** ボタンを選択すると、BLOB の論理的に削除されたスナップショットが復元されます。 次に、昇格させるスナップショットを選択し、 **[スナップショットの昇格]** ボタンを使用して、ベース BLOB をスナップショットの内容で上書きします。

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="スナップショットをベース BLOB に昇格させる方法を示すスクリーンショット":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に論理的に削除された BLOB を復元する

バージョン管理が有効になっている場合に論理的に削除された BLOB を Azure portal で復元するには、論理的に削除された BLOB を選択してプロパティを表示し、 **[バージョン]** タブを選択します。現在のバージョンとして昇格させるバージョンを選択し、 **[現在のバージョンに変更する]** を選択します。  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Azure portal で BLOB を復元するためにバージョンを昇格させる方法を示すスクリーンショット":::

バージョン管理が有効になっている場合に削除されたバージョンまたはスナップショットを復元するには、BLOB のプロパティを表示し、 **[概要]** タブの **[削除の取り消し]** ボタンを選択します。

> [!NOTE]
> バージョン管理が有効になっている場合に、削除された BLOB に対して **[削除の取り消し]** 操作を選択すると、論理的に削除されたバージョンまたはスナップショットは復元されますが、ベース BLOB は復元されません。 ベース BLOB を復元するには、以前のバージョンを昇格させる必要があります。

## <a name="manage-soft-deleted-blobs-with-code"></a>論理的に削除された BLOB をコードで管理する

Azure Storage クライアント ライブラリを使用して、論理的に削除された BLOB またはスナップショットを復元できます。 次の例は、.NETクライアント ライブラリを使用する方法を示しています。

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>バージョン管理が無効になっている場合に論理的に削除されたオブジェクトを復元する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

バージョン管理が有効になっていない場合に削除された BLOB を復元するには、これらの BLOB に対して [[BLOB の削除の取り消し]](/rest/api/storageservices/undelete-blob) 操作を呼び出します。 **[BLOB の削除の取り消し]** 操作によって、論理的に削除された BLOB とそれらの BLOB に関連する削除済みスナップショットが復元されます。

削除されていない BLOB に対して **[BLOB の削除の取り消し]** を呼び出しても効果はありません。 次の例では、コンテナー内のすべての BLOB に対して **[BLOB の削除の取り消し]** を呼び出し、論理的に削除された BLOB とそのスナップショットを復元します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

特定のバージョンを復元するには、最初にベースの BLOB またはバージョンに対して **[BLOB の削除の取り消し]** 操作を呼び出し、次に目的のバージョンをベース BLOB にコピーします。 次の例では、ブロック BLOB を最後に保存されたバージョンに復元します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

バージョン管理が有効になっていない場合に削除された BLOB を復元するには、これらの BLOB に対して [[BLOB の削除の取り消し]](/rest/api/storageservices/undelete-blob) 操作を呼び出します。 **[BLOB の削除の取り消し]** 操作によって、論理的に削除された BLOB とそれらの BLOB に関連する削除済みスナップショットが復元されます。

削除されていない BLOB に対して **[BLOB の削除の取り消し]** を呼び出しても効果はありません。 次の例では、コンテナー内のすべての BLOB に対して **[BLOB の削除の取り消し]** を呼び出し、論理的に削除された BLOB とそのスナップショットを復元します。

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

特定のスナップショットを復元するには、最初にベース BLOB に対して **[BLOB の削除の取り消し]** 操作を呼び出し、次に目的のスナップショットをベース BLOB にコピーします。 次の例では、ブロック BLOB を最後に生成されたスナップショットに復元します。

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に論理的に削除された BLOB を復元する

バージョン管理が有効になっている場合に論理的に削除された BLOB を復元するには、[[BLOB のコピー]](/rest/api/storageservices/copy-blob) または [[BLOB を URL からコピーする]](/rest/api/storageservices/copy-blob-from-url) 操作を使用して、ベース BLOB に以前のバージョンをコピーします。  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

適用不可。 BLOB のバージョン管理は、Azure Storage クライアント ライブラリのバージョン 12.x 以降でのみサポートされます。

---

## <a name="next-steps"></a>次のステップ

- [BLOB ストレージの論理的な削除](./soft-delete-blob-overview.md)
- [BLOB の論理的な削除の有効化](soft-delete-blob-enable.md)
- [BLOB のバージョン管理](versioning-overview.md)
