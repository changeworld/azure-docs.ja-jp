---
title: "Azure Storage のスケーラビリティおよびパフォーマンスのターゲット | Microsoft Docs"
description: "Standard Storage アカウントと Premium Storage アカウントの両方の容量、要求レート、および送受信の帯域幅を含む、Azure Storage のスケーラビリティとパフォーマンスのターゲットについて説明します。 各 Azure Storage サービス内にあるパーティションのパフォーマンス ターゲットを理解します。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: abaad01bbf7a11ad078c79d7c192ef3f84812178
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure Storage のスケーラビリティおよびパフォーマンスのターゲット
## <a name="overview"></a>概要
このトピックでは、Microsoft Azure Storage のスケーラビリティとパフォーマンスについて説明します。 その他の Azure の制約に関する概要は、「 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../../azure-subscription-service-limits.md)」を参照してください。

> [!NOTE]
> すべてのストレージ アカウントは新しいフラット ネットワーク トポロジで実行され、ストレージ アカウントがいつ作成されたかにかかわらず、以下に示すスケーラビリティおよびパフォーマンスのターゲットがサポートされます。 のフラット ネットワーク アーキテクチャおよびスケーラビリティの詳細については、 [Microsoft Azure Storage の高い整合性を持つ高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)に関するページを参照してください。
> 
> [!IMPORTANT]
> ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。 いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。 必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。 可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。
> 
> ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返しはじめます。 このような状況になった場合、アプリケーションで指数関数的バックオフによる再試行ポリシーを使用する必要があります。 指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。
> 
> 

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合、複数のストレージ アカウントを使用し、それらのストレージ アカウント間でデータが分割されるようにアプリケーションを構築できます。 ボリューム価格については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

## <a name="scalability-targets-for-a-storage-account"></a>ストレージ アカウントのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob Storage のスケール ターゲット
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット
Azure Files と Azure File Sync のスケールおよびパフォーマンス ターゲットの詳細については、「[Azure Files scalability and performance targets (Azure Files のスケーラビリティおよびパフォーマンス ターゲット)](../files/storage-files-scale-targets.md)」を参照してください。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue Storage のスケール ターゲット
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage のスケール ターゲット
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>仮想マシンのディスクのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

詳細については、[Windows VM のサイズ](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)と [Linux VM のサイズ](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

## <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>関連項目
* [Storage の料金詳細](https://azure.microsoft.com/pricing/details/storage/)
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-subscription-service-limits.md)
* [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage.md)
* [Azure Storage のレプリケーション](../storage-redundancy.md)
* [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage-performance-checklist.md)
* [Microsoft Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

