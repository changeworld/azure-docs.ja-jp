---
title: Azure Storage のスケーラビリティおよびパフォーマンスのターゲット
description: Standard Azure ストレージ アカウントの容量、要求レート、受信と送信の帯域幅など、スケーラビリティとパフォーマンスのターゲットについて説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 93e09f3ab6780eb9ce7fa29b4554b53d796b6837
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564955"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Standard ストレージ アカウントでの Azure Storage のスケーラビリティとパフォーマンスのターゲット

この記事では、Standard Azure ストレージ アカウントでのスケーラビリティとパフォーマンスのターゲットについて詳しく説明します。 ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。 いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。 

必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。 可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。

ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返し始めます。 503 エラーが発生する場合は、再試行に対してエクスポネンシャル バックオフ ポリシーを使用するようアプリケーションを変更することを検討します。 指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。

## <a name="standard-storage-account-scale-limits"></a>Standard ストレージ アカウントのスケールの上限
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>ストレージ リソース プロバイダーのスケールの上限 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob Storage のスケール ターゲット
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット
Azure Files と Azure File Sync のスケールおよびパフォーマンス ターゲットの詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../files/storage-files-scale-targets.md)」を参照してください。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット
Azure File Sync は使用量無制限を目標に設計されていますが、使用量無制限が常に可能なわけではありません。 次の表では、Microsoft のテストの境界と、どのターゲットがハード制限かを示します。

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue Storage のスケール ターゲット
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage のスケール ターゲット
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>関連項目
* [Storage の料金詳細](https://azure.microsoft.com/pricing/details/storage/)
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-subscription-service-limits.md)
* [Azure Storage のレプリケーション](../storage-redundancy.md)
* [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage-performance-checklist.md)

