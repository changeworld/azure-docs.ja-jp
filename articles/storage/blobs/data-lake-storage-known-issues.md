---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033939"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされている機能とツールについて説明します。

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>BLOB API の使用に関する問題と制限事項

BLOB API と Data Lake Storage Gen2 API では、同じデータを処理できます。

このセクションでは、BLOB API と Data Lake Storage Gen2 API を使用して同じデータを操作する場合の問題と制限事項について説明します。

* BLOB API と Data Lake Storage API の両方を使用して、ファイルの同じインスタンスに書き込むことはできません。 Data Lake Storage Gen2 API を使用してファイルに書き込むと、そのファイルのブロックは、[Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) BLOB API への呼び出しで認識されなくなります。 Data Lake Storage Gen2 API または BLOB API のいずれかを使用して、ファイルを上書きできます。 これはファイルのプロパティには影響しません。

* 区切り記号を指定せずに [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。 区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

* [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。 これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。

## <a name="support-for-other-blob-storage-features"></a>その他の BLOB ストレージ機能のサポート

階層型名前空間を持つストレージ アカウント (Azure Data Lake Storage Gen2) でまだサポートされていないか部分的にサポートされているその他すべての機能とツールを次の表に示します。

| 機能 / ツール    | 詳細情報    |
|--------|-----------|
| **Data Lake Storage Gen2 API** | 部分的にサポート <br><br>現在のリリースでは、Data Lake Storage Gen2 **REST** API を使用して、ディレクトリとの対話やアクセス制御リスト (Acl) の設定を行うことができますが、これらのタスクを実行する他の SDK (.NET、Java、Python など) はありません。 ファイルのアップロードやダウンロードなどの他のタスクを実行するには、BLOB SDK を使用できます。  |
| **AzCopy** | バージョン固有のサポート <br><br>AzCopy の最新バージョン ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用します。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。|
| **Azure Blob Storage ライフサイクル管理ポリシー** | すべてのアクセス層がサポートされています。 アーカイブ アクセス層は、現在プレビュー段階です。 BLOB スナップショットの削除は、まだサポートされていません。 |
| **Azure Content Delivery Network (CDN)** | まだサポートされていません|
| **Azure Search** |サポート (プレビュー)|
| **Azure Storage Explorer** | バージョン固有のサポート <br><br>バージョン `1.6.0` から `1.10.0` のみを使用してください。 <br> バージョン `1.10.0` は[無料のダウンロード](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)として提供されています。 バージョン `1.11.0` はまだサポートされていません。|
| **BLOB コンテナーの ACL** |まだサポートされていません|
| **Blobfuse** |まだサポートされていません|
| **カスタム ドメイン** |まだサポートされていません|
| **Azure portal での Storage Explorer** | 制限付きサポート。 ACL はまだサポートされていません。 |
| **診断ログ** |診断ログがサポートされています (プレビュー)。<br><br>Azure portal でのログの有効化は現在サポートされていません。 PowerShell を使用してログを有効にする方法の例を次に示します。 <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>` <br><br>この例に示すように、`-ServiceType` パラメーターの値として `Blob` を指定してください。 <br><br>現時点では、診断ログの表示に Azure Storage Explorer を使用できません。 ログを表示するには、AzCopy または SDK を使用してください。
| **不変ストレージ** |まだサポートされていません <br><br>不変ストレージでは、[WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) 状態でデータを格納できます。|
| **オブジェクト レベルの階層** |クール層とアーカイブ層がサポートされています。 アーカイブ層はプレビュー段階です。 その他のすべてのアクセス層は、まだサポートされていません。|
| **Powershell と CLI のサポート** | 機能の制限あり <br><br>BLOB の操作がサポートされています。 ディレクトリの操作とアクセス制御リスト (ACL) の設定は、まだサポートされていません。 |
| **静的な Web サイト** |まだサポートされていません <br><br>具体的には、[静的な Web サイト](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)にファイルを提供する機能です。|
| **サード パーティ製アプリケーション** | 制限付きサポート <br><br>REST API を使用して動作するサード パーティ製アプリケーションは、Data Lake Storage Gen2 と使用しても引き続き機能します。 <br>Blob API を呼び出すアプリケーションは動作する可能性があります。|
|**論理的な削除** |まだサポートされていません|
| **バージョン管理機能** |まだサポートされていません <br><br>これには、[論理削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)や、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)などのその他のバージョン管理機能が含まれます。|


