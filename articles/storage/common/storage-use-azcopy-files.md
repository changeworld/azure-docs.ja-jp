---
title: AzCopy v10 を使用して Azure Files をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: AzCopy とファイル ストレージでデータを転送します。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245460"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy とファイル ストレージでデータを転送する 

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事には、Azure Files で使用するサンプル コマンドが含まれています。

始める前に、[AzCopy を使ってみる](storage-use-azcopy-v10.md)の記事を参照して、AzCopy をダウンロードし、ツールについて理解してください。

## <a name="create-file-shares"></a>ファイル共有の作成

AzCopy `make` コマンドを使用し、ファイル共有を作成できます。 このセクションの例では、`myfileshare` という名前のファイル共有を作成します。

|    |     |
|--------|-----------|
| **構文** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>ファイルをアップロードする

AzCopy `copy` コマンドを使用し、ローカル コンピューターからファイルやフォルダーをアップロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをアップロードする
> * フォルダーをアップロードする
> * ワイルドカード文字を使用してファイルをアップロードする

> [!NOTE]
> AzCopy では、ファイルの md5 ハッシュ コードが自動的に計算され、保存されることはありません。 AzCopy にそれを行わせる場合、各コピー コマンドの先頭に `--put-md5` フラグを追加します。 そうすることで、ファイルがダウンロードされたとき、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **例** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>フォルダーをアップロードする

この例では、フォルダー (とそのフォルダー内のすべてのファイル) がファイル共有にコピーされます。 結果的に、同じ名前でファイル共有にフォルダーが生成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

ファイル共有内のフォルダーにコピーするには、コマンド文字列でそのフォルダーの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

ファイル共有に存在しないフォルダーの名前を指定すると、AzCopy では、その名前で新しいフォルダーが作成されます。

### <a name="upload-the-contents-of-a-folder"></a>フォルダーの内容をアップロードする

ワイルドカード記号 (*) を使用することで、フォルダー自体をコピーせずにフォルダーの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **例** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> すべてのサブフォルダーのファイルをアップロードするには、`--recursive` フラグを追加します。

## <a name="download-files"></a>ファイルをダウンロードする

AzCopy `copy` コマンドを使用し、ファイル、フォルダー、ファイル共有をローカル コンピューターにダウンロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをダウンロードする
> * フォルダーをダウンロードする
> * ワイルドカード文字を使用してファイルをダウンロードする

> [!NOTE]
> ファイルの `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作を上書きしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>フォルダーをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

この例では結果的に、ダウンロードしたファイルがすべて含まれる `C:\myFolder\myFileShareFolder` という名前のフォルダーが生成されます。

### <a name="download-the-contents-of-a-folder"></a>フォルダーの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、フォルダー自体をコピーせずにフォルダーの内容をダウンロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> すべてのサブフォルダーのファイルをダウンロードするには、`--recursive` フラグを追加します。

## <a name="next-steps"></a>次の手順

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [AzCopy と BLOB ストレージでデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)