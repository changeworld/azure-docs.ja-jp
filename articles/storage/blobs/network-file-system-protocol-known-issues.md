---
title: Azure Blob Storage の NFS 3.0 に関する既知の問題 | Microsoft Docs
description: Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートに関する制限事項と既知の問題について学習します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 9d54072c49fd0ce1571ebbcfced76d74ce18ea7a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471104"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートにおける既知の問題

この記事では、Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートに関する制限事項と既知の問題について説明します。

## <a name="nfs-30-support"></a>NFS 3.0 のサポート

- 既存のストレージ アカウントで NFS 3.0 のサポートを有効にすることはできません。

- ストレージ アカウントで NFS 3.0 のサポートを一度有効にすると、無効に戻すことはできません。

## <a name="nfs-30-features"></a>NFS 3.0 の機能

次の NFS 3.0 の機能はまだサポートされていません。

- UDP 経由の NFS 3.0。 TCP 経由の NFS 3.0 のみがサポートされています。

- ネットワーク ロック マネージャー (NLM) を使用したファイルのロック。 mount コマンドに `-o nolock` パラメーターを含める必要があります。

- サブディレクトリのマウント。 ルート ディレクトリ (コンテナー) のみをマウントできます。

- マウントの一覧表示 (例: `showmount -a` コマンドを使用)

- エクスポートの一覧表示 (例: `showmount -e` コマンドを使用)

- ハード リンク

- コンテナーを読み取り専用としてエクスポートする

## <a name="nfs-30-clients"></a>NFS 3.0 クライアント

NFS 用の Windows クライアントはまだサポートされていません

## <a name="blob-storage-features"></a>Blob Storage の機能

NFS 3.0 プロトコルのサポートを有効にすると、一部の Blob Storage 機能は完全にサポートされますが、機能によっては、プレビュー レベルでのみサポートされる機能や、現時点ではまだまったくサポートされていないものもあります。  

NFS 3.0 のサポートが有効になっているアカウントでの各 Blob Storage 機能のサポート状況を確認するには、「[Azure ストレージ アカウントにおける Blob Storage 機能のサポート](storage-feature-support-in-storage-accounts.md)」を参照してください。

## <a name="blob-storage-apis"></a>BLOB ストレージ API

NFS 3.0、BLOB API と Data Lake Storage Gen2 API では、同じデータを処理できます。 

このセクションでは、NFS 3.0、BLOB API と Data Lake Storage Gen2 API を使用して同じデータを操作する場合の問題と制限事項について説明します。 

- NFS 3.0 と BLOB API または Data Lake Storage API を使用して、ファイルの同じインスタンスに書き込むことはできません。 NFS を使用してファイルに書き込むと、そのファイルのブロックは、 [Get Block List](/rest/api/storageservices/get-block-list)  BLOB API への呼び出しで認識されなくなります。 唯一の例外は、上書きする場合です。 いずれかの API を使用して、ファイルまたは BLOB を上書きできます。 ゼロ切り捨てオプションを使用して、NFS 3.0 で上書きすることもできます。

- 区切り記号を指定せずに [List Blobs](/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。 区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

- [Delete Blob](/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。 これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

* [Put Blob (Page)](/rest/api/storageservices/put-blob)
* [Put Page](/rest/api/storageservices/put-page)
* [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](/rest/api/storageservices/put-page-from-url)
* [Put Block List](/rest/api/storageservices/put-block-list)

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。 

## <a name="see-also"></a>関連項目

- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)
- [ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)