---
title: Azure NetApp Files のコスト モデル | Microsoft Docs
description: サービスから経費を管理するための Azure NetApp Files のコスト モデルについて説明します。
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 2df332450c996c1a7b1b9b5e35b06d4fb226ed93
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108794468"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files のコスト モデル 

Azure NetApp Files のコスト モデルを理解すると、サービスから経費を管理するのに役立ちます。 

リージョン間レプリケーションに固有のコスト モデルについては、「[リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)」を参照してください。

## <a name="calculation-of-capacity-consumption"></a>容量消費の計算

Azure NetApp Files は、プロビジョニングされたストレージ容量に対して課金されます。  プロビジョニングされる容量は、容量プールを作成することによって割り当てられます。  容量プールは、プロビジョニングされた GiB/月あたりの料金に基づいて、1 時間単位で課金されます。 1 つの容量プールの最小サイズは 4 TiB であり、容量プールを後から 1 TiB 単位で拡張することができます。 ボリュームは容量プール内に作成されます。  各ボリュームには、プールでプロビジョニングされた容量からデクリメントするクォータが割り当てられます。 ボリュームに割り当てることができるクォータは、最小 100 GiB から最大 100 TiB の範囲です。  

アクティブなボリュームの場合、クォータに対する容量の消費は、アクティブなファイルシステムまたはスナップショット データのいずれかの論理 (有効な) 容量に基づきます。 ボリュームには、セット サイズ (クォータ) と同じ量のデータしか格納できません。

プロビジョニングされた分量に対する容量プールの合計使用容量は、プール内の全ボリュームの実際の消費量の合計です。 

   ![合計使用容量の計算を示す式。](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

## <a name="capacity-consumption-of-snapshots"></a>スナップショットの容量消費 

Azure NetApp Files のスナップショットの容量消費は、親ボリュームのクォータに対して課金されます。  その結果、ボリュームが属する容量プールと同じ課金レートを共有します。  ただし、アクティブ ボリュームとは違って、スナップショットの消費量は消費された増分容量に基づいて測定されます。  Azure NetApp Files のスナップショットは本質的には差分です。 データの変更率にもよりますが、スナップショットの消費容量は多くの場合、アクティブ ボリュームの論理容量を大きく下回ります。 たとえば、10 GiB の差分データのみが含まれる 500 GiB のボリュームのスナップショットがあるとします。 アクティブなファイルシステムとスナップショットのボリューム クォータに対してカウントされる容量消費は、1000 GiB ではなく、510 GiB になります。 原則として、推奨される容量の 20% で、1 週間分のスナップショット データを保持すると想定できます (スナップショットの頻度とアプリケーションの 1 日あたりのブロック レベルの変更率によって異なります)。 

次の図にこれらの概念を示します。 

* プロビジョニング済み容量が 40 TiB の容量プールがあるとします。 プールには、次の 3 つのボリュームが含まれています。    
    * ボリューム 1 には 20 TiB のクォータが割り当てられており、消費量は 13 TiB (12 TiB アクティブ、1 TiB スナップショット) です。
    * ボリューム 2 には 1 TiB のクォータが割り当てられており、消費量は 450 GiB です。
    * ボリューム 3 には 14 TiB のクォータが割り当てられていますが、消費量は 8.8 TiB (8 TiB アクティブ、800 GiB スナップショット) です。   
* 容量プールは 40 TiB の容量 (プロビジョニングされた分量) に対して測定されます。 22.25 TiB の容量が消費されています (ボリューム 1、2、3 の 13 TiB、450 GiB、8.8 TiB のクォータ)。 容量プールには、17.75 TiB の容量が残っています。   

![3 つのボリュームを持つ容量プールを示す図](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [ボリューム クォータについて](volume-quota-introduction.md)
* [ボリュームの容量を監視する](monitor-volume-capacity.md)
* [容量プールまたはボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [タグを使用して課金を管理する](manage-billing-tags.md)
* [容量管理に関する FAQ](azure-netapp-files-faqs.md#capacity-management-faqs)
