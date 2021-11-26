---
title: Azure ストレージ アカウントにおける Blob Storage 機能のサポート
description: ストレージ アカウントの種類ごとに、各ストレージ アカウント機能のサポート レベルとそのアカウントで有効な設定を明らかにします。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: normesta
ms.openlocfilehash: 7bd3709f012eb42ad11d0524abf47f776b9f5096
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719150"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Azure ストレージ アカウントにおける Blob Storage 機能のサポート

この記事では、機能が完全にサポートされているか、プレビュー レベルでサポートされているか、またはまだサポートされていないかを明らかにします。 サポート レベルは、ストレージ アカウントの種類は何か、また特定の機能またはプロトコルがそのアカウントで有効になっているかどうかに左右されます。

サポートは継続的に拡張されるため、この表に示されている項目は時間の経過と共に変化します。

## <a name="standard-general-purpose-v2-accounts"></a>Standard 汎用 v2 アカウント

| ストレージ機能 | BLOB Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  | SFTP <sup>1</sup> | 
|---------------|-------------------|---|---|--|
| [アクセス層 - アーカイブ](access-tiers-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [アクセス層 - コールド](access-tiers-overview.md)    | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)| ![はい](../media/icons/yes-icon.png) |
| [アクセス層 - ホット](access-tiers-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [匿名パブリック アクセス](anonymous-read-access-configure.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)| ![はい](../media/icons/yes-icon.png) |
| [Azure Active Directory セキュリティ](authorize-access-azure-active-directory.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB インベントリ](blob-inventory.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [BLOB インデックス タグ](storage-manage-find-blobs.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB のスナップショット](snapshots-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blob Storage API](reference.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>3</sup> | ![はい](../media/icons/yes-icon.png) <sup>3</sup>| ![はい](../media/icons/yes-icon.png) <sup>3</sup> |
| [Blob Storage の Azure CLI コマンド](storage-quickstart-blobs-cli.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [Blob Storage のイベント](storage-blob-event-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>3</sup>  | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blob Storage の PowerShell コマンド](storage-quickstart-blobs-powershell.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [BLOB バージョン管理](versioning-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [変更フィード](storage-blob-change-feed.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [カスタム ドメイン](storage-custom-domain-name.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [カスタマー マネージド アカウントのフェールオーバー](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [カスタマー マネージド キー (暗号化)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [カスタマー指定のキー (暗号化)](encryption-customer-provided-keys.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [データ冗長オプション](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [暗号化スコープ](encryption-scope-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [不変ストレージ](immutable-storage-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [ライフサイクル管理のための最終アクセス時刻の追跡](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [ライフサイクル管理ポリシー (BLOB の削除)](./lifecycle-management-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [ライフサイクル管理ポリシー (階層化)](./lifecycle-management-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [Azure Monitor へのログイン](./monitor-blob-storage.md) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Azure Monitor のメトリック](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [ブロック BLOB のオブジェクト レプリケーション](object-replication-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![はい](../media/icons/yes-icon.png) |![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [ブロック BLOB のポイントインタイム リストア](point-in-time-restore-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB の論理的な削除](./soft-delete-blob-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [コンテナーの論理的な削除](soft-delete-container-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [静的な Web サイト](storage-blob-static-website.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [Storage Analytics ログ (クラシック)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>3</sup> | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Storage Analytics のメトリック (クラシック)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送 (SFTP) プロトコルのサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

<sup>2</sup>    機能はプレビュー レベルでサポートされています。

<sup>3</sup> 「[Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)」を参照してください。 これらの問題は、階層型名前空間機能が有効になっているすべてのアカウントに適用されます。

## <a name="premium-block-blob-accounts"></a>Premium ブロック BLOB アカウント

| ストレージ機能 | BLOB Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|---------------|-------------------|---|---|--|
| [アクセス層 - アーカイブ](access-tiers-overview.md)  | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [アクセス層 - コールド](access-tiers-overview.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [アクセス層 - ホット](access-tiers-overview.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [匿名パブリック アクセス](anonymous-read-access-configure.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Azure Active Directory セキュリティ](authorize-access-azure-active-directory.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB インベントリ](blob-inventory.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [BLOB インデックス タグ](storage-manage-find-blobs.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB のスナップショット](snapshots-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blob Storage API](reference.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>3</sup> | ![はい](../media/icons/yes-icon.png) <sup>3</sup>| ![はい](../media/icons/yes-icon.png) <sup>3</sup> |
| [Blob Storage の Azure CLI コマンド](storage-quickstart-blobs-cli.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [Blob Storage のイベント](storage-blob-event-overview.md) | ![はい](../media/icons/yes-icon.png)  <sup>3</sup>  | ![はい](../media/icons/yes-icon.png) <sup>3</sup>| ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blob Storage の PowerShell コマンド](storage-quickstart-blobs-powershell.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [BLOB バージョン管理](versioning-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [変更フィード](storage-blob-change-feed.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [カスタム ドメイン](storage-custom-domain-name.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [カスタマー マネージド アカウントのフェールオーバー](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [カスタマー マネージド キー (暗号化)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [カスタマー指定のキー (暗号化)](encryption-customer-provided-keys.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [データ冗長オプション](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [暗号化スコープ](encryption-scope-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [不変ストレージ](immutable-storage-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [ライフサイクル管理のための最終アクセス時刻の追跡](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [ライフサイクル管理ポリシー (BLOB の削除)](./lifecycle-management-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [ライフサイクル管理ポリシー (階層化)](./lifecycle-management-overview.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [Azure Monitor へのログイン](./monitor-blob-storage.md) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Azure Monitor のメトリック](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| [ブロック BLOB のオブジェクト レプリケーション](object-replication-overview.md) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [ブロック BLOB のポイントインタイム リストア](point-in-time-restore-overview.md) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [BLOB の論理的な削除](./soft-delete-blob-overview.md)   | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>   <sup>3</sup> | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [コンテナーの論理的な削除](soft-delete-container-overview.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| [静的な Web サイト](storage-blob-static-website.md) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| [Storage Analytics ログ (クラシック)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>  <sup>3</sup> | ![いいえ](../media/icons/no-icon.png)| ![いいえ](../media/icons/no-icon.png) |
| [Storage Analytics のメトリック (クラシック)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送 (SFTP) プロトコルのサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

<sup>2</sup>    機能はプレビュー レベルでサポートされています。

<sup>3</sup> 「[Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)」を参照してください。 これらの問題は、階層型名前空間機能が有効になっているすべてのアカウントに適用されます。

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)

- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートにおける既知の問題](network-file-system-protocol-known-issues.md)

- [Azure Blob Storage でのセキュリティで保護されたファイル転送プロトコル (SFTP) のサポートに関する既知の問題 (プレビュー)](secure-file-transfer-protocol-known-issues.md)
