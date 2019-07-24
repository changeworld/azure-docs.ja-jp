---
title: AzCopy v10 を使用して Azure Blob Storage をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: この記事には、コンテナーの作成、ファイルのコピー、ローカル ファイル システムとコンテナー間の同期に役立つ、一連の AzCopy サンプル コマンドが含まれています。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299403"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy と Blob Storage でデータを転送する

AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事には、Blob Storage で使用するサンプル コマンドが含まれています。

## <a name="get-started"></a>作業開始

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE]
> この記事の例では、`AzCopy login` コマンドを使用して ID を認証したと想定しています。 AzCopy では次に、Blob Storage のデータへのアクセスを承認するために、Azure AD アカウントが使用されます。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。
>
> (例: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`)。

## <a name="create-a-container"></a>コンテナーを作成する

AzCopy `make` コマンドを使用し、コンテナーを作成できます。 このセクションの例では、`mycontainer` という名前のコンテナーが作成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **例** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **例** (階層型名前空間) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>ファイルをアップロードする

AzCopy `copy` コマンドを使用し、ローカル コンピューターからファイルやフォルダーをアップロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをアップロードする
> * フォルダーをアップロードする
> * ワイルドカード文字を使用してファイルをアップロードする

> [!NOTE]
> AzCopy では、ファイルの md5 ハッシュ コードが自動的に計算されたり、保存されたりすることはありません。 AzCopy にそれを行わせる場合、各コピー コマンドの先頭に `--put-md5` フラグを追加します。 そうすることで、BLOB がダウンロードされたとき、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **例** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **例** (階層型名前空間) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 既定では、AzCopy はデータをブロック BLOB にアップロードします。 追加 BLOB またはページ BLOB としてファイルをアップロードするには、`--blob-type=[BlockBlob|PageBlob|AppendBlob]` フラグを使用します。

### <a name="upload-a-folder"></a>フォルダーをアップロードする

この例では、フォルダー (とそのフォルダー内のすべてのファイル) が BLOB コンテナーにコピーされます。 結果的に、同じ名前でコンテナーにフォルダーが生成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **例** (階層型名前空間) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

コンテナー内のフォルダーにコピーするには、コマンド文字列でそのフォルダーの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **例** (階層型名前空間) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

コンテナーに存在しないフォルダーの名前を指定すると、AzCopy では、その名前で新しいフォルダーが作成されます。

### <a name="upload-the-contents-of-a-folder"></a>フォルダーのコンテンツをアップロードする

ワイルドカード記号 (*) を使用することで、フォルダー自体をコピーせずにフォルダーの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **例** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **例** (階層型名前空間) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> すべてのサブフォルダーのファイルをアップロードするには、`--recursive` フラグを追加します。

## <a name="download-files"></a>ファイルをダウンロードする

AzCopy `copy` コマンドを使用し、BLOB、フォルダー、コンテナーをローカル コンピューターにダウンロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをダウンロードする
> * フォルダーをダウンロードする
> * ワイルドカード文字を使用してファイルをダウンロードする

> [!NOTE]
> BLOB の `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作をオーバーライドしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **例** (階層型名前空間) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>フォルダーをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **例** (階層型名前空間) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

この例では結果的に、ダウンロードしたファイルがすべて含まれる `C:\myFolder\myBlobFolder` という名前のフォルダーが生成されます。

### <a name="download-the-contents-of-a-folder"></a>フォルダーのコンテンツをダウンロードする

ワイルドカード記号 (*) を使用することで、フォルダー自体をコピーせずにフォルダーの内容をダウンロードできます。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> すべてのサブフォルダーのファイルをダウンロードするには、`--recursive` フラグを追加します。

## <a name="copy-blobs-between-storage-accounts"></a>ストレージ アカウント間で BLOB をコピーする

AzCopy を使用し、BLOB を他のストレージ アカウントにコピーできます。 コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

AzCopy では、[URL からブロックの配置](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API が使用されます。そのため、データはストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * 別のストレージ アカウントに BLOB をコピーする
> * 別のストレージ アカウントにフォルダーをコピーする
> * 別のストレージ アカウントにコンテナーをコピーする
> * すべてのコンテナー、フォルダー、ファイルを別のストレージ アカウントにコピーする

### <a name="copy-a-blob-to-another-storage-account"></a>別のストレージ アカウントに BLOB をコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>別のストレージ アカウントにフォルダーをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>別のストレージ アカウントにコンテナーをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>すべてのコンテナー、フォルダー、ファイルを別のストレージ アカウントにコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>ファイルを同期する

ローカル ファイル システムのコンテンツを BLOB コンテナーに同期できます。 コンピューター上のローカル ファイル システムに BLOB コンテナーを同期することもできます。 同期は一方向です。 言い換えると、2 つのエンドポイントのいずれかを同期元として、いずれかを同期先として選択します。

> [!NOTE]
> 現行版の AzCopy では、他のコピー元とコピー先の間では同期されません (例:ファイル ストレージまたはアマゾン ウェブ サービス (AWS) S3 バケット)。

`sync` コマンドでは、ファイル名と最後に変更されたタイムスタンプが比較されます。 任意の `--delete-destination` フラグの値を `true` または `prompt` に設定すると、コピー元のフォルダーからファイルがなくなった場合、コピー先のフォルダーのファイルが削除されます。

`--delete-destination` フラグを `true` に設定すると、AzCopy では、プロンプトを与えずにファイルが削除されます。 AzCopy でファイルが削除される前にプロンプトを表示する場合、`--delete-destination` フラグを `prompt` に設定します。

> [!NOTE]
> 誤削除を防ぐために、`--delete-destination=prompt|true` フラグを使用する前に[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)機能を有効にしてください。

### <a name="synchronize-a-container-to-a-local-file-system"></a>ローカル ファイル システムにコンテナーを同期する

この場合、ローカル ファイル システムが同期元になり、コンテナーが同期先になります。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **例** (階層型名前空間) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>コンテナーにローカル ファイル システムを同期する

この場合、コンテナーが同期元になり、ローカル ファイル システムが同期先になります。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **例** (階層型名前空間) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>次の手順

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)