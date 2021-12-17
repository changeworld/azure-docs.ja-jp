---
title: Azure Data Lake Storage Gen2 に関する既知の問題
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6a612621ef316e7d4e7c248968b3cc87c04b85ae
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046263"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、階層型名前空間機能が有効になっているアカウントの制限事項と既知の問題について説明します。

> [!NOTE]
> この記事で説明する機能の一部は、ネットワーク ファイル システム (NFS) 3.0 のサポートが有効になっているアカウントではサポートされていない場合があります。 さまざまな機能が有効になっている場合の機能サポートの影響を示す表を表示するには、「[Azure ストレージ アカウントにおける Blob Storage 機能のサポート](storage-feature-support-in-storage-accounts.md)」を参照してください。

## <a name="supported-blob-storage-features"></a>サポートされる BLOB ストレージの機能

階層型名前空間を持つアカウントで、より多くの BLOB ストレージ機能が動作するようになりました。 完全な一覧については、「[Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](./storage-feature-support-in-storage-accounts.md)」を参照してください。

## <a name="supported-azure-service-integrations"></a>サポートされる Azure サービスの統合

Azure Data Lake Storage Gen2 は、データの取り込み、分析の実行、およびビジュアル表現の作成に使用できるいくつかの Azure サービスをサポートしています。 サポートされる Azure サービスの一覧については、「[Azure Data Lake Storage Gen2 をサポートする Azure サービス](data-lake-storage-supported-azure-services.md)」を参照してください。

詳細については、「[Azure Data Lake Storage Gen2 をサポートする Azure サービス](data-lake-storage-supported-azure-services.md)」を参照してください。

## <a name="supported-open-source-platforms"></a>サポートされるオープン ソース プラットフォーム

一部のオープン ソース プラットフォームは Data Lake Storage Gen2 をサポートしています。 完全な一覧については、「[Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)」を参照してください。

詳細については、「[Azure Data Lake Storage Gen2 がサポートされているオープンソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)」を参照してください。

## <a name="blob-storage-apis"></a>BLOB ストレージ API

Data Lake Storage Gen2 API、NFS 3.0、BLOB API では、同じデータを処理できます。

このセクションでは、BLOB API、NFS 3.0、Data Lake Storage Gen2 API を使用して同じデータを操作する場合の問題と制限事項について説明します。

- BLOB API、NFS 3.0、Data Lake Storage API を使用して、ファイルの同じインスタンスに書き込むことはできません。 Data Lake Storage Gen2 または API または NFS 3.0 を使用してファイルに書き込むと、そのファイルのブロックは、[Get Block List](/rest/api/storageservices/get-block-list) BLOB API への呼び出しで認識されなくなります。 唯一の例外は、上書きする場合です。 0 切り捨てオプションを使用して、API または NFS 3.0 のいずれかを使用してファイル/BLOB を上書きできます。

- 区切り記号を指定せずに [List Blobs](/rest/api/storageservices/list-blobs) 操作を使用した場合、結果にはディレクトリと BLOB の両方が含まれます。 区切り記号を使用する場合は、スラッシュ (`/`) のみを使用してください。 サポートされている区切り記号はこれだけです。

- [Delete Blob](/rest/api/storageservices/delete-blob) API を使用してディレクトリを削除した場合、そのディレクトリは空の場合にのみ削除されます。 これは、Blob API を使用してディレクトリを再帰的に削除することはできないことを意味します。

次の BLOB REST API はサポートされていません。

- [Put Blob (Page)](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)

アンマネージド VM ディスクは、階層型名前空間があるアカウントではサポートされていません。 ストレージ アカウントで階層型名前空間を有効にする場合は、階層型名前空間機能が有効ではないストレージ アカウントにアンマネージド VM ディスクを配置してください。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>アクセス制御リスト (ACL) を再帰的に設定するためのサポート

親ディレクトリから子項目に ACL 変更を再帰的に適用する機能が公開されています。 この機能の現在のリリースでは、Azure Storage Explorer、PowerShell、Azure CLI、.NET、Java、Python SDK を使用して ACL の変更を適用できます。 Azure portal のサポートはまだ提供されていません。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>アクセス制御リスト (ACL) と匿名読み取りアクセス

コンテナーへの[匿名読み取りアクセス](./anonymous-read-access-configure.md)が許可されている場合、そのコンテナーやコンテナーに含まれているファイルには ACL は作用しません。  これは読み取り要求にのみ影響を与えます。  書き込み要求では ACL が引き続き優先されます。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

AzCopy の最新バージョン ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) のみを使用してください。 AzCopy の以前のバージョン (AzCopy v8.1 など) はサポートされていません。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

バージョン `1.6.0` 以降のみを使用します。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure portal での Storage Explorer

ACL はまだサポートされていません。

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>サード パーティ製アプリケーション

REST API を使用して動作するサード パーティ製アプリケーションは、Data Lake Storage Gen2 と使用しても引き続き機能します。 Blob API を呼び出すアプリケーションは動作する可能性があります。

## <a name="storage-analytics-logs-classic"></a>Storage Analytics ログ (クラシック)

リテンション期間の設定はまだサポートされていませんが、Azure Storage Explorer、REST、SDK などのサポートされているツールを使用して、ログを手動で削除することができます。

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure Storage Blob (WASB) ドライバー

現時点では、BLOB API のみで動作するように設計された WASB ドライバーでは、いくつかの一般的なシナリオで問題が発生します。 特に、階層型名前空間が有効なストレージ アカウントのクライアントである場合です。 これらの問題は、Data Lake Storage のマルチプロトコル アクセスによっても軽減されません。

階層型名前空間が有効なストレージ アカウントへのクライアントとして WASB ドライバーを使用することはできません。 代わりに、Hadoop 環境で [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) ドライバーを使用することをお勧めします。 Hadoop branch 3 より前のバージョンを使用するオンプレミスの Hadoop 環境から移行しようとしている場合は、お客様と組織にとって適切な方法についてご連絡できるように、Azure サポート チケットを開いてください。

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>BLOB の論理削除機能 (現在プレビュー段階)

論理削除されたファイルまたはディレクトリの親ディレクトリの名前が変更された場合、論理削除された項目が Azure portal で正しく表示されないことがあります。 そのような場合、[PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) または [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli) を使用し、論理削除された項目を一覧表示し、復元できます。

## <a name="events"></a>イベント

アカウントにイベント サブスクリプションがある場合は、セカンダリ エンドポイントに対する読み取り操作によってエラーが発生します。 この問題を解決するには、イベント サブスクリプションを削除します。

<<<<<<< HEAD
しばらく (おそらく当面) の間は、階層型名前空間が有効なストレージ アカウントのクライアントとして、WASB ドライバーを使用しているお客様はサポートされません。 代わりに、Hadoop 環境で [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) ドライバーを使用することをお勧めします。 Hadoop branch 3 より前のバージョンを使用するオンプレミスの Hadoop 環境から移行しようとしている場合は、お客様と組織にとって適切な方法についてご連絡できるように、Azure サポート チケットを開いてください。
=======
> [!TIP]
> セカンダリ エンドポイントへの読み取りアクセスは、読み取りアクセス geo 冗長ストレージ (RA-GRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にした場合にのみ使用できます。
>>>>>>> repo_sync_working_branch
