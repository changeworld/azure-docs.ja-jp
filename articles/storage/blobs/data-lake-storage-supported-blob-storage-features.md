---
title: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能 | Microsoft Docs
description: Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能について説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196010"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、 [BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの機能へのアクセスを失うことなく、Blob Storage アカウントで階層型名前空間を有効にできます。

Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能を次の表に示します。 この表に示されている項目は、サポートが引き続き拡張されるため、時間の経過と共に変化します。

## <a name="supported-blob-storage-features"></a>サポートされている BLOB ストレージ機能

> [!NOTE]
> サポート レベルは、機能が Data Lake Storage Gen2 でどのようにサポートされているかのみを示します。

|BLOB ストレージ機能 |サポート レベル |関連記事 |
|---------------|-------------------|---|
|ホット アクセス層|一般公開|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|クール アクセス層|一般公開|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|events|一般公開|[Blob Storage のイベント処理](storage-blob-event-overview.md)|
|メトリック (クラシック)|一般公開|[Azure Storage Analytics のメトリック (クラシック)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor のメトリック|一般公開|[Azure Monitor の Azure Storage メトリック](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB ストレージの PowerShell コマンド|一般公開|[クイック スタート: PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する](storage-quickstart-blobs-powershell.md)|
|BLOB ストレージの Azure CLI コマンド|一般公開|[クイック スタート: Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](storage-quickstart-blobs-cli.md)|
|BLOB ストレージ API|一般公開|[クイック スタート: .NET 用 Azure Blob Storage クライアント ライブラリ v12](storage-quickstart-blobs-dotnet.md)<br>[クイック スタート: Java v12 SDK で BLOB を管理する](storage-quickstart-blobs-java.md)<br>[クイック スタート: Python v12 SDK で BLOB を管理する](storage-quickstart-blobs-python.md)<br>[クイック スタート: Node.js の JavaScript v12 SDK を使用して BLOB を管理する](storage-quickstart-blobs-nodejs.md)|
|アーカイブ アクセス層|プレビュー|[Azure BLOB Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)|
|ライフサイクル管理ポリシー|プレビュー|[Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)|
|診断ログ|プレビュー|[Azure Storage Analytics のログ](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Change Feed|まだサポートされていません|[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)|
|アカウントのフェールオーバー|まだサポートされていません|[ディザスター リカバリーとアカウントのフェールオーバー](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB コンテナーの ACL|まだサポートされていません|[コンテナー ACL の設定](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|カスタム ドメイン|まだサポートされていません|[カスタム ドメインを Azure Blob ストレージ エンドポイントにマップする](storage-custom-domain-name.md)|
|不変ストレージ|まだサポートされていません|[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](storage-blob-immutable-storage.md)|
|スナップショット|まだサポートされていません|[.NET での BLOB スナップショットの作成と管理](storage-blob-snapshots.md)|
|論理的な削除|まだサポートされていません|[Azure Storage Blob の論理的な削除](storage-blob-soft-delete.md)|
|静的な Web サイト|まだサポートされていません|[Azure Storage での静的な Web サイトのホスティング](storage-blob-static-website.md)|
|Azure Monitor へのログイン|まだサポートされていません|まだ使用できません|
|Premium ブロック BLOB|まだサポートされていません|[BlockBlobStorage アカウントを作成する](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)