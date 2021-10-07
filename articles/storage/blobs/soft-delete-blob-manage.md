---
title: 論理的に削除された BLOB を管理および復元する
titleSuffix: Azure Storage
description: Azure portal または Azure Storage クライアント ライブラリを使用して、論理的に削除された BLOB およびスナップショットを管理および復元します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627940"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>論理的に削除された BLOB を管理および復元する

BLOB の論理的な削除を使用すると、削除されたデータがシステムに一定の期間保持されることにより、個々の BLOB とその複数バージョン、スナップショット、およびメタデータが誤った削除や上書きから保護されます。 保持期間中は、BLOB を削除時の状態に復元できます。 保持期間が過ぎると、BLOB オブジェクトは完全に削除されます。 BLOB の論理的な削除の詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="manage-soft-deleted-blobs"></a>論理的に削除された BLOB を管理する

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure portal を使用して論理的に削除された BLOB を管理する

Azure portal を使用して、論理的に削除された BLOB とスナップショットを表示および復元できます。

#### <a name="view-deleted-blobs"></a>削除された BLOB を表示する

論理的に削除された BLOBは、既定では Azure portal に表示されません。 論理的に削除された BLOB を表示するには、コンテナーの **[概要]** ページに移動し、 **[削除された Blob を表示]** 設定を切り替えます。 論理的に削除された BLOB は、 **[削除済み]** の状態で表示されます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="論理的に削除された BLOB を Azure portal で一覧表示する方法を示すスクリーンショット":::

次に、BLOB の一覧から、削除済みの BLOB を選択して、そのプロパティを表示します。 **[概要]** タブで、BLOB の状態が **[削除済み]** に設定されていることを確認します。 ポータルには、BLOB が完全に削除されるまでの日数も表示されます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Azure portal での論理的に削除された BLOB のプロパティを示すスクリーンショット":::

#### <a name="view-deleted-snapshots"></a>削除されたスナップショットを表示する

BLOB を削除すると、その BLOB に関連するスナップショットもすべて削除されます。 論理的に削除された BLOB にスナップショットがある場合は、削除されたスナップショットもポータルに表示できます。 論理的に削除された BLOB のプロパティを表示し、 **[スナップショット]** タブに移動して、 **[削除されたスナップショットを表示]** を切り替えます。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="スクリーンショットの表示":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>バージョン管理が無効になっている場合に論理的に削除されたオブジェクトを復元する

BLOB のバージョン管理が有効になっていない場合に論理的に削除された BLOB を Azure portal で復元するには、最初に BLOB のプロパティを表示し、 **[概要]** タブの **[削除の取り消し]** ボタンを選択します。BLOB を復元すると、論理的な削除の保持期間中に削除されたすべてのスナップショットも復元されます。

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="論理的に削除された BLOB を Azure portal で復元する方法を示すスクリーンショット":::

論理的に削除されたスナップショットをベース BLOB に昇格させるには、最初に BLOB の論理的に削除されたスナップショットが復元されていることを確認します。 ベース BLOB 自体が論理的に削除されていない場合でも、 **[削除の取り消し]** ボタンを選択すると、BLOB の論理的に削除されたスナップショットが復元されます。 次に、昇格させるスナップショットを選択し、 **[スナップショットの昇格]** ボタンを使用して、ベース BLOB をスナップショットの内容で上書きします。

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="スナップショットをベース BLOB に昇格させる方法を示すスクリーンショット":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に論理的に削除された BLOB を復元する

バージョン管理が有効になっている場合に論理的に削除された BLOB を Azure portal で復元するには、論理的に削除された BLOB を選択してプロパティを表示し、 **[バージョン]** タブを選択します。現在のバージョンとして昇格させるバージョンを選択し、 **[現在のバージョンに変更する]** を選択します。

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Azure portal で BLOB を復元するためにバージョンを昇格させる方法を示すスクリーンショット":::

バージョン管理が有効になっている場合に削除されたバージョンまたはスナップショットを復元するには、BLOB のプロパティを表示し、 **[概要]** タブの **[削除の取り消し]** ボタンを選択します。

> [!NOTE]
> バージョン管理が有効になっている場合に、削除された BLOB に対して **[削除の取り消し]** 操作を選択すると、論理的に削除されたバージョンまたはスナップショットは復元されますが、ベース BLOB は復元されません。 ベース BLOB を復元するには、以前のバージョンを昇格させる必要があります。

### <a name="manage-soft-deleted-blobs-with-code"></a>論理的に削除された BLOB をコードで管理する

Azure Storage クライアント ライブラリを使用して、論理的に削除された BLOB またはスナップショットを復元できます。 次の例は、.NETクライアント ライブラリを使用する方法を示しています。

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>バージョン管理が無効になっている場合に論理的に削除されたオブジェクトを復元する

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

バージョン管理が有効になっていない場合に削除された BLOB を復元するには、これらの BLOB に対して [[BLOB の削除の取り消し]](/rest/api/storageservices/undelete-blob) 操作を呼び出します。 **[BLOB の削除の取り消し]** 操作によって、論理的に削除された BLOB とそれらの BLOB に関連する削除済みスナップショットが復元されます。

削除されていない BLOB に対して **[BLOB の削除の取り消し]** を呼び出しても効果はありません。 次の例では、コンテナー内のすべての BLOB に対して **[BLOB の削除の取り消し]** を呼び出し、論理的に削除された BLOB とそのスナップショットを復元します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

論理的に削除された特定のスナップショットを復元するには、最初にベース BLOB に対して **[BLOB の削除の取り消し]** 操作を呼び出し、次に目的のスナップショットをベース BLOB にコピーします。 次の例では、ブロック BLOB を、最後に生成されたスナップショットに復元します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

バージョン管理が有効になっていない場合に削除された BLOB を復元するには、これらの BLOB に対して [[BLOB の削除の取り消し]](/rest/api/storageservices/undelete-blob) 操作を呼び出します。 **[BLOB の削除の取り消し]** 操作によって、論理的に削除された BLOB とそれらの BLOB に関連する削除済みスナップショットが復元されます。

削除されていない BLOB に対して **[BLOB の削除の取り消し]** を呼び出しても効果はありません。 次の例では、コンテナー内のすべての BLOB に対して **[BLOB の削除の取り消し]** を呼び出し、論理的に削除された BLOB とそのスナップショットを復元します。

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

論理的に削除された特定のスナップショットを復元するには、最初にベース BLOB に対して **[BLOB の削除の取り消し]** 操作を呼び出し、次に目的のスナップショットをベース BLOB にコピーします。 次の例では、ブロック BLOB を最後に生成されたスナップショットに復元します。

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

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に論理的に削除された BLOB を復元する

バージョン管理が有効になっている場合に論理的に削除された BLOB を復元するには、[[BLOB のコピー]](/rest/api/storageservices/copy-blob) または [[BLOB を URL からコピーする]](/rest/api/storageservices/copy-blob-from-url) 操作を使用して、ベース BLOB に以前のバージョンをコピーします。

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

適用不可。 BLOB のバージョン管理は、Azure Storage クライアント ライブラリのバージョン 12.x 以降でのみサポートされます。

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>論理的に削除された BLOB とディレクトリを管理する (階層型名前空間)

階層型名前空間が使用されたアカウントの論理的に削除された BLOB とディレクトリを復元できます。

> [!IMPORTANT]
> 階層型名前空間機能が有効になっているアカウントでの論理的な削除は、現在プレビュー段階であり、すべての Azure リージョンでグローバルに利用できます。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)を参照してください。

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure portal を使用して論理的に削除された BLOB を管理する

Azure portal を使用して、論理的に削除された BLOB とディレクトリを表示および復元できます。

#### <a name="view-deleted-blobs-and-directories"></a>削除された BLOB とディレクトリを表示する

論理的に削除された BLOB またはディレクトリは、既定では Azure portal に表示されません。 論理的に削除された BLOB とディレクトリを表示するには、コンテナーの **[概要]** ページに移動し、 **[削除された Blob を表示]** 設定を切り替えます。 論理的に削除された BLOB とディレクトリは、 **[削除済み]** の状態で表示されます。 次の図は、論理的に削除されたディレクトリを示しています。

> [!div class="mx-imgBorder"]
> ![Azure portal で論理的に削除された BLOB を一覧表示する方法を示すスクリーンショット (階層型名前空間が有効なアカウント)。](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> 論理的に削除された項目 (サブディレクトリと BLOB) を含むディレクトリの名前を変更すると、それらの論理的に削除された項目はディレクトリから切断されるため、 **[削除された Blob を表示]** 設定を切り替えたときに Azure portal に表示されなくなります。 Azure portal でそれらを表示するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。

次に、一覧から削除済みのディレクトリまたは BLOB を選択して、そのプロパティを表示します。 **[概要]** タブで、状態が **[削除済み]** に設定されていることを確認します。 ポータルには、BLOB が完全に削除されるまでの日数も表示されます。

> [!div class="mx-imgBorder"]
> ![Azure portal で論理的に削除された BLOB のプロパティを示すスクリーンショット (階層型名前空間が有効なアカウント)。](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>論理的に削除された BLOB とディレクトリを復元する

Azure portal で論理的に削除された BLOB またはディレクトリを復元するには、最初に BLOB またはディレクトリのプロパティを表示し、次に **[概要]** タブの **[削除の取り消し]** ボタンを選択します。次の図は、論理的に削除されたディレクトリの [削除の取り消し] ボタンを示しています。

> [!div class="mx-imgBorder"]
> ![Azure portal で論理的に削除された BLOB を復元する方法を示すスクリーンショット (階層型名前空間が有効なアカウント)。](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>PowerShell を使用して論理的に削除された BLOB とディレクトリを復元する

> [!IMPORTANT]
> このセクションは、階層型名前空間があるアカウントにのみ適用されます。

1. **Az.Storage** プレビュー モジュールがインストールされていることを確認します。 詳細については、[PowerShell 経由での論理的な削除の有効化](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace)に関するページを参照してください。

2. ストレージ アカウント キー、接続文字列、Azure Active Directory (Azure AD) のいずれかを使用して、ストレージ アカウントの承認を取得します。 詳細については、「[アカウントに接続する](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)」を参照してください。

   次の例では、ストレージ アカウント キーを使用して承認を取得します。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. 論理的に削除された項目を復元するには、`Restore-AzDataLakeGen2DeletedItem` コマンドを使用します。

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   論理的に削除された項目が含まれているディレクトリの名前を変更すると、それらの項目はディレクトリから切断されます。 それらの項目を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された項目を復元しようとしたときにエラーが発生します。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>Azure CLI を使用して論理的に削除された BLOB とディレクトリを復元する

> [!IMPORTANT]
> このセクションは、階層型名前空間があるアカウントにのみ適用されます。

1. `storage-preview` 拡張機能がインストールされていることを確認します。 詳細については、[PowerShell を使用した論理的な削除の有効化](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace)に関するページを参照してください。

2. 削除された項目の一覧を取得します。

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. 項目を復元するには、`az storage fs undelete-path` コマンドを使用します。

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   論理的に削除された項目が含まれているディレクトリの名前を変更すると、それらの項目はディレクトリから切断されます。 それらの項目を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された項目を復元しようとしたときにエラーが発生します。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>.NET を使用して論理的に削除された BLOB とディレクトリを復元する

> [!IMPORTANT]
> このセクションは、階層型名前空間があるアカウントにのみ適用されます。

1. コマンド プロンプトを開き、ディレクトリ (`cd`) をプロジェクト フォルダーに変更します。次に例を示します。

   ```console
   cd myProject
   ```

2. `dotnet add package` コマンドを使用して、[Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet パッケージの `Azure.Storage.Files.DataLake -v 12.7.0` バージョンをインストールします。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 次に、これらの using ステートメントをコード ファイルの先頭に追加します。

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. 次のコードでは、ディレクトリを削除した後、論理的に削除されたディレクトリを復元します。

   この方法では、[DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスが作成されていることを前提としています。 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成する方法については、「[アカウントに接続する](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account)」を参照してください。

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   論理的に削除された項目が含まれているディレクトリの名前を変更すると、それらの項目はディレクトリから切断されます。 それらの項目を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された項目を復元しようとしたときにエラーが発生します。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>Java を使用して論理的に削除された BLOB とディレクトリを復元する

> [!IMPORTANT]
> このセクションは、階層型名前空間があるアカウントにのみ適用されます。

1. 開始するには、テキスト エディターで *pom.xml* ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. 次に、これらの import ステートメントをコード ファイルに追加します。

   ```java
   Put imports here
   ```

3. 次のスニペットでは、`my-file` という名前の論理的に削除されたファイルを復元します。

   この方法では、**DataLakeServiceClient** インスタンスが作成されていることを前提としています。 **DataLakeServiceClient** インスタンスを作成する方法については、「[アカウントに接続する](data-lake-storage-directory-file-acl-java.md#connect-to-the-account)」を参照してください。

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   論理的に削除された項目が含まれているディレクトリの名前を変更すると、それらの項目はディレクトリから切断されます。 それらの項目を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された項目を復元しようとしたときにエラーが発生します。

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>Python を使用して論理的に削除された BLOB とディレクトリを復元する

> [!IMPORTANT]
> このセクションは、階層型名前空間があるアカウントにのみ適用されます。

1. [pip](https://pypi.org/project/pip/) を使用して、Python 向けの Azure Data Lake Storage クライアント ライブラリのバージョン `12.4.0` 以降をインストールします。 このコマンドでは、Python 向けの Azure Data Lake Storage クライアント ライブラリの最新バージョンをインストールします。

   ```
   pip install azure-storage-file-datalake
   ```

2. 次の import ステートメントを、コード ファイルの先頭に追加します。

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. 次のコードでは、ディレクトリを削除した後、論理的に削除されたディレクトリを復元します。

   次のコード例には、**DataLakeServiceClient** 型の `service_client` という名前のオブジェクトが含まれています。 **DataLakeServiceClient** インスタンスを作成する方法の例については、「[アカウントに接続する](data-lake-storage-directory-file-acl-python.md#connect-to-the-account)」を参照してください。

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   論理的に削除された項目が含まれているディレクトリの名前を変更すると、それらの項目はディレクトリから切断されます。 それらの項目を復元するには、ディレクトリの名前を元の名前に戻すか、元のディレクトリ名を使用する別のディレクトリを作成する必要があります。 そうしないと、これらの論理的に削除された項目を復元しようとしたときにエラーが発生します。

## <a name="next-steps"></a>次のステップ

- [BLOB ストレージの論理的な削除](soft-delete-blob-overview.md)
- [BLOB の論理的な削除の有効化](soft-delete-blob-enable.md)
- [BLOB のバージョン管理](versioning-overview.md)
