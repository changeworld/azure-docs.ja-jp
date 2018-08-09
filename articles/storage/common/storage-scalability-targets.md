---
title: Azure Storage のスケーラビリティおよびパフォーマンスのターゲット | Microsoft Docs
description: Standard Storage アカウントと Premium Storage アカウントの両方の容量、要求レート、および送受信の帯域幅を含む、Azure Storage のスケーラビリティとパフォーマンスのターゲットについて説明します。 各 Azure Storage サービス内にあるパーティションのパフォーマンス ターゲットを理解します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 1db7219beead4d7ee228baff4c802c105419ba1c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524605"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure Storage のスケーラビリティおよびパフォーマンスのターゲット
## <a name="overview"></a>概要
この記事では、Azure Storage のスケーラビリティとパフォーマンスについて説明します。 その他の Azure の制約に関する概要は、「 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../../azure-subscription-service-limits.md)」を参照してください。

> [!NOTE]
> すべてのストレージ アカウントは新しいフラット ネットワーク トポロジで実行され、ストレージ アカウントがいつ作成されたかにかかわらず、この記事で概要を説明するスケーラビリティとパフォーマンスのターゲットがサポートされます。 のフラット ネットワーク アーキテクチャおよびスケーラビリティの詳細については、 [Microsoft Azure Storage の高い整合性を持つ高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)に関するページを参照してください。
> 

> [!IMPORTANT]
> ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。 いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。 必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。 可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。
> 
> ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返し始めます。 このようなエラーが発生した場合は、アプリケーションで指数関数的バックオフによる再試行ポリシーを使用する必要があります。 指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。
> 
> 

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築できます。 その後、それらのストレージ アカウント間でデータをパーティション分割できます。 ボリューム価格については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

## <a name="scalability-targets-for-a-storage-account"></a>ストレージ アカウントのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob Storage のスケール ターゲット
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット
Azure Files と Azure File Sync のスケールおよびパフォーマンス ターゲットの詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../files/storage-files-scale-targets.md)」を参照してください。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット
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
* [Microsoft Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

