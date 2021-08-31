---
title: Azure BLOB バックアップのサポート マトリックス
description: Azure BLOB をバックアップするときのサポート設定と制限の概要について説明します。
ms.topic: conceptual
ms.date: 07/07/2021
ms.custom: references_regions
ms.openlocfilehash: ba2798ff464720379326ee56098f840a06e2c042
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722648"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Azure BLOB バックアップのサポート マトリックス

この記事では、利用できるリージョン、サポートされるシナリオ、BLOB の運用バックアップの制限事項について説明します。

## <a name="supported-regions"></a>サポートされているリージョン

BLOB の運用バックアップは、現在、次のリージョンでご利用いただけます。オーストラリア中部、オーストラリア中部 2、オーストラリア東部、オーストラリア南東部、ブラジル南部、ブラジル南東部、カナダ中部、カナダ東部、インド中部、米国中部、東アジア、米国東部、米国東部 2、フランス中部、ドイツ北部、ドイツ中西部、東日本、西日本、韓国中部、韓国南部、米国中北部、北ヨーロッパ、ノルウェー東部、ノルウェー西部、南アフリカ北部、米国中南部、東南アジア、インド南部、スイス北部、スイス西部、アラブ首長国連邦中部、アラブ首長国連邦北部、英国南部、英国西部、米国中西部、西ヨーロッパ、インド西部、米国西部、米国西部 2。

## <a name="limitations"></a>制限事項

BLOB の運用バックアップでは、ローカル バックアップソリューションを提供するために、BLOB のポイントインタイム リストア、BLOB のバージョン管理、BLOB の論理的な削除、BLOB の変更フィード、および削除ロックが使用されます。 そのため、これらの機能に適用される制限が、運用バックアップにも適用されます。

**サポートされているシナリオ:** 運用バックアップでサポートされているのは、Standard 汎用 v2 ストレージ アカウントのブロック BLOB のみです。 そのため、ADLS Gen2 アカウントはサポートされません。 また、ストレージ アカウントのページ BLOB、追加 BLOB、Premium BLOB は復元されず、ブロック BLOB のみが復元されます。

**その他の制限事項:**

- 保持期間中にコンテナーを削除した場合、そのコンテナーは、ポイントインタイム リストア操作では復元されません。 復元しようとしている BLOB 範囲に、削除されたコンテナー内の BLOB が含まれている場合、ポイントインタイム リストア操作は失敗します。 削除からのコンテナーの保護の詳細については、「[コンテナーの論理的な削除 (プレビュー)](../storage/blobs/soft-delete-container-overview.md)」を参照してください。
- 現在の時点から復元ポイントまでの間にホット層とクール層の間で BLOB が移動した場合、BLOB は以前の層に復元されます。 アーカイブ層でのブロック BLOB の復元はサポートされていません。 たとえば、ホット層の BLOB が 2 日前にアーカイブ層に移動され、復元操作によって 3 日前の時点に復元された場合、BLOB はホット層に復元されません。 アーカイブされた BLOB を復元するには、最初にアーカイブ層の外に移動します。 詳細については、「[アーカイブ層から BLOB データをリハイドレートする](../storage/blobs/storage-blob-rehydration.md)」を参照してください。
- [Put Block](/rest/api/storageservices/put-block) または [Put Block from URL](/rest/api/storageservices/put-block-from-url) を使用してアップロードされたが、[Put Block List](/rest/api/storageservices/put-block-list) を使用してコミットされていないブロックは、BLOB の一部ではないため、復元操作の一部としては復元されません。
- アクティブなリースを持つ BLOB は復元できません。 リースがアクティブになっている BLOB が、復元する BLOB の範囲に含まれる場合、復元操作は自動的に失敗します。 復元操作を始める前に、アクティブなリースをすべて中断してください。
- スナップショットは、復元操作の一環として作成または削除されません。 ベース BLOB のみが以前の状態に復元されます。
- 復元される BLOB の中に[変更できない BLOB](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs) がある場合、このような変更できない BLOB は、選択した復旧ポイントに基づいた状態に復元されません。 ただし、不変性が有効になっていないその他の BLOB は、選択した復旧ポイントに想定どおり復元されます。

## <a name="next-steps"></a>次のステップ

[Azure Blob の運用バックアップの概要](blob-backup-overview.md)