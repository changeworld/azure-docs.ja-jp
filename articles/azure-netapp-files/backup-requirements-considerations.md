---
title: Azure NetApp Files バックアップの要件と考慮事項 | Microsoft Docs
description: Azure NetApp Files バックアップを使用する前に知っておく必要がある要件と考慮事項について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: b-juche
ms.openlocfilehash: 5e622f86b7b0b9d757dc32089db86c7bfca7fe63
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239754"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Azure NetApp Files バックアップの要件と考慮事項 

この記事では、Azure NetApp Files バックアップを使用する前に知っておく必要がある要件と考慮事項について説明します。

## <a name="requirements-and-considerations"></a>要件と考慮事項

Azure NetApp Files バックアップを使用する前に、要件および考慮事項をいくつか知っておく必要があります。 

* Azure NetApp Files バックアップは、ご自身の Azure NetApp Files サブスクリプションに関連付けられているリージョン内で使用できます。 リージョン内の Azure NetApp Files バックアップが保護できるのは、同じリージョン内にある Azure NetApp Files ボリュームだけです。 たとえば、米国西部 2 内に配置されているボリュームに対して、米国西部 2 内のサービスによって作成されたバックアップは、同じく米国西部 2 内に配置されている Azure ストレージにも送信されます。 Azure NetApp Files は、異なるリージョンへのバックアップまたはバックアップ レプリケーションをサポートしいません。  

* バックアップが実際に完了してから、表示されるまでに、最長で 5 分の遅延が発生する可能性があります。

* 現在、Azure NetApp Files のバックアップ機能では、関連付けられているスナップショット ポリシーによって作成された日単位、週単位、月単位のローカル スナップショットを、Azure ストレージにバックアップすることができます。 時間単位のバックアップは現時点では、サポートされていません。

* Azure NetApp Files バックアップによって使用されるのは、リージョン内の 3 つの Azure 可用性ゾーンにわたってデータを同期的にレプリケートする[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (ZRS) アカウントです。ただし、以下のリージョンについては、[ローカル冗長ストレージ](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (LRS) ストレージのみがサポートされています。   

    * 米国西部   

    LRS の場合、サーバーラックやドライブの故障からの復旧が可能です。 ただし、データ センター内で火災や洪水などの災害が発生した場合、LRS を使用しているストレージ アカウントのすべてのレプリカが失われたり、回復不能になったりする可能性があります。 

* ポリシー ベースの (スケジュールされた) Azure NetApp Files バックアップを使用するには、スナップショット ポリシーが構成され、有効になっている必要があります。 「[Azure NetApp Files を使用してスナップショットを管理する](azure-netapp-files-manage-snapshots.md)」を参照してください。   
    バックアップする必要のあるボリュームには、スナップショットを作成するための構成済みスナップショット ポリシーが必要です。 設定された数のバックアップが Azure ストレージに保存されます。 

* (ボリュームに十分な空き容量が残っていない、といった) 問題が発生し、スナップショット ポリシーによって新しいスナップショットの作成が停止されると、バックアップ機能によって、バックアップする新しいスナップショットが作成されなくなります。 

* リージョン間レプリケーションの設定では、Azure NetApp Files のバックアップはソース ボリュームにのみ構成できます。 リージョン間レプリケーションの "*宛先*" ボリュームではサポートされていません。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
* [Azure NetApp Files のスナップショットのしくみ](snapshots-introduction.md)
