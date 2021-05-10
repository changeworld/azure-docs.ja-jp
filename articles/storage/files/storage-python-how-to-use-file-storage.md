---
title: Python での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する Python アプリケーションとサービスを開発する方法を説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: d45ce3a782d7ee145f769283b82e34647c78f26e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799872"
---
# <a name="develop-for-azure-files-with-python"></a>Python での Azure Files 用の開発

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

ファイル データの格納に Azure Files を使用するアプリまたはサービスを開発するための Python の基本的な使い方について説明します。 シンプルなコンソール アプリを作成し、Python と Azure Files での基本操作の実行方法を示します。

- Azure ファイル共有を作成する
- ディレクトリを作成する
- Azure ファイル共有のファイルとディレクトリを列挙する
- ファイルのアップロード、ダウンロード、および削除
- スナップショットを使用してファイル共有バックアップを作成する

> [!NOTE]
> Azure Files は SMB 経由でアクセスできるため、ファイル I/O の標準 Python I/O クラスと関数を使って Azure ファイル共有にアクセスする簡単なアプリケーションを作成できます。 この記事では、Azure Storage Python SDK を使用するアプリを記述する方法を説明します。ここでは、Azure Files との通信に [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) が使用されます。

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Microsoft Azure Storage SDK for Python をダウンロードしてインストールする

> [!NOTE]
> Azure Storage SDK for Python 0.36 以前のバージョンからアップグレードする場合は、`pip uninstall azure-storage` を使用して以前の SDK をアンインストールしてから、最新のパッケージをインストールします。

# <a name="python-v12"></a>[Python v12](#tab/python)

[Azure File Storage クライアント ライブラリ v12.x for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) には、Python 2.7 または 3.6 以降が必要です。

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) には、Python 2.7 または 3.6 以降が必要です。

---

## <a name="install-via-pypi"></a>PyPI でインストールする

Python Package Index (PyPI) でインストールするには、次のように入力します。

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>サンプル アプリケーションを表示する

Python を Azure Files と一緒に使用する方法を示すサンプル アプリケーションを表示して実行するには、[Azure Storage:Python での Azure Files の使用開始](https://github.com/Azure-Samples/storage-file-python-getting-started)に関するページを参照してください。

サンプル アプリケーションを実行するには、`azure-storage-file` パッケージと `azure-storage-common` パッケージの両方がインストールされていることを確認します。

---

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files を使用するようにアプリケーションを設定する

この記事のコード スニペットを使用するには、Python ソース ファイルの先頭近くに次のコードを追加します。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Azure Files への接続を設定する

# <a name="python-v12"></a>[Python v12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) を使用して、共有、ディレクトリ、ファイルを操作できます。 次のコードによって、ストレージ アカウント接続文字列を使用する `ShareServiceClient` オブジェクトが作成されます。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) オブジェクトを使用して、共有、ディレクトリ、ファイルを操作できます。 次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、`FileService` オブジェクトを作成します。 `<myaccount>` と `<mykey>` をアカウント名とキーに置き換えます。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

# <a name="python-v12"></a>[Python v12](#tab/python)

次のコード サンプルでは、共有が存在しない場合は [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) オブジェクトを使用してそれが作成されます。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

次のコード サンプルでは、共有が存在しない場合は [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) オブジェクトを使用してそれが作成されます。

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>ディレクトリを作成する

ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することができます。

# <a name="python-v12"></a>[Python v12](#tab/python)

次のメソッドでは、[ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) オブジェクトを使用して、指定されたファイル共有のルートにディレクトリが作成されます。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下のコードはルート ディレクトリの下に *sampledir* という名前のサブディレクトリを作成します。

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>ファイルをアップロードする

このセクションでは、ローカル ストレージから Azure File Storage にファイルをアップロードする方法について説明します。

# <a name="python-v12"></a>[Python v12](#tab/python)

次のメソッドでは、指定されたファイルの内容が、指定された Azure ファイル共有内の指定されたディレクトリにアップロードされます。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Azure ファイル共有には、少なくともファイルを配置できるルート ディレクトリが含まれます。 ファイルを作成してデータをアップロードするには、[create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-)、[create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)、[create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)、または [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) の各メソッドを使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベルのメソッドです。

`create_file_from_path` は、指定したパスからファイルの内容をアップロードし、`create_file_from_stream` は既に開いているファイルまたはストリームから内容をアップロードします。 `create_file_from_bytes` はバイト配列をアップロードし、`create_file_from_text` は指定したエンコーディング (既定値から UTF-8) を使って、指定したテキスト値をアップロードします。

次の例では、*sunset.png* ファイルの内容を **myfile** ファイルにアップロードします。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure ファイル共有のファイルとディレクトリを列挙する

# <a name="python-v12"></a>[Python v12](#tab/python)

サブディレクトリ内のファイルとディレクトリを一覧表示するには、[list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) メソッドを使用します。 このメソッドでは、反復可能な自動ページングが返されます。 次のコードでは、指定されたディレクトリ内の各ファイルとディレクトリの **名前** がコンソールに出力されます。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

共有内のファイルとディレクトリを一覧表示するには、[list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) メソッドを使用します。 このメソッドは、ジェネレーターを返します。 次のコードでは、共有内の各ファイルとディレクトリの **名前 (name)** をコンソールに出力しています。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>ファイルをダウンロードする

# <a name="python-v12"></a>[Python v12](#tab/python)

ファイルからデータをダウンロードするには、[download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-) を使用します。

次の例では、指定したファイルの内容を `download_file` を使用して取得し、ファイル名の先頭に **DOWNLOADED-** を追加してローカルに保存する操作が示されています。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

ファイルからデータをダウンロードするには、[get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、[get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、[get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、または [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) を使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベルのメソッドです。

次の例は、`get_file_to_path` を使用して **myfile** ファイルの内容をダウンロードし、*out-sunset.png* ファイルに格納する方法を示しています。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>共有スナップショットを作成する

ファイル共有全体の特定の時点のコピーを作成できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**メタデータでの共有スナップショットの作成**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>共有とスナップショットの一覧表示

特定の共有のすべてのスナップショットを一覧表示できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>共有スナップショットの参照

各共有スナップショットを参照して、その時点のファイルとディレクトリを取得できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>共有スナップショットからのファイルの取得

共有スナップショットからファイルをダウンロードできます。 これにより、以前のバージョンのファイルを復元できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>単一の共有スナップショットの削除
単一の共有スナップショットを削除できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>ファイルを削除する

# <a name="python-v12"></a>[Python v12](#tab/python)

ファイルを削除するには、[delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-) を呼び出します。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

ファイルを削除するには、[delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-) を呼び出します。

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>共有スナップショットが存在する場合の共有の削除

# <a name="python-v12"></a>[Python v12](#tab/python)

スナップショットを含む共有を削除するには、[delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) を `delete_snapshots=True` を指定して呼び出します。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

スナップショットが含まれる共有は、すべてのスナップショットを最初に削除しない限り削除できません。

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>次のステップ

Python での Azure Files を操作する方法を習得したので、詳細について次のリンクを参照してください。

- [Python デベロッパー センター](/azure/developer/python/)
- [Azure Storage Services REST API (Azure Storage サービスの REST API)](/rest/api/azure/)
- [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
