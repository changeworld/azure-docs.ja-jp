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
ms.date: 11/08/2021
ms.author: b-juche
ms.openlocfilehash: 56091fb0c6ddfa205fc60f6aef06397a0314369e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053162"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files のコスト モデル 

Azure NetApp Files のコスト モデルを理解すると、サービスから経費を管理するのに役立ちます。 

リージョン間レプリケーションに固有のコスト モデルについては、「[リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)」を参照してください。

## <a name="calculation-of-capacity-consumption"></a>容量消費の計算

Azure NetApp Files は、容量プールを作成することによって割り当てられる、プロビジョニング済みのストレージ容量に対して課金されます。 容量プールは、1 時間あたりの割り当て済み GiB あたりの設定コストに基づいて毎月課金されます。 容量プールの割り当ては毎時計測されます。  

容量プールは 4 TiB 以上である必要があり、1 TiB 単位で増減できます。 容量プールには、最小 100 GiB から最大 100 TiB までのサイズのボリュームが含まれます。 ボリュームには、容量プールのプロビジョニング済みサイズから差し引かれたクォータが割り当てられます。 アクティブなボリュームの場合、クォータに対する容量の消費は、アクティブなファイルシステム データまたはスナップショット データである論理 (実効) 容量に基づきます。 詳細については、「[Azure NetApp Files スナップショットのしくみ](snapshots-introduction.md)」を参照してください。 

### <a name="pricing-examples"></a>価格の例

ここでは、Azure NetApp Files のコスト モデルを理解するのに役立つ例を示します。

#### <a name="example-1-one-month-cost-with-static-versus-dynamic-capacity-pool-provisioning"></a>例 1: 静的および動的な容量プールのプロビジョニングの 1 か月のコスト 

(容量やパフォーマンスのニーズの変動により) 容量プールのサイズ要件が変動する場合は、容量およびパフォーマンスのニーズとバランスが取れたコストになるように、[ボリュームと容量プールの動的なサイズ変更](azure-netapp-files-resize-capacity-pools-or-volumes.md)を検討してください。

たとえば、10 TiB で 24 時間 (1 日)、24 TiB で 96 時間 (4 日)、5 TiB で 6 時間 (1 日) (これを 4 回)、6 TiB で 480 時間 (20 日)、月の残りの時間は 0 TiB の Premium 容量を使用するとします。 動的なクラウド消費デプロイ プロファイルは、従来の静的なオンプレミス消費プロファイルとは様相が異なります。 

[ ![容量プールのプロビジョニング (動的と静的の比較) を示した棒グラフ。](../media/azure-netapp-files/cost-model-example-one-capacity.png) ](../media/azure-netapp-files/cost-model-example-one-capacity.png#lightbox)

GiB/時間あたり 0.000403 ドル ([価格はリージョンによって異なります](https://azure.microsoft.com/pricing/details/netapp/)) で料金が請求される場合、毎月のコストの内訳は次のようになります。

*Premium での静的プロビジョニング (ピーク時の容量/パフォーマンス)*

* 24 TiB x 720 時間 x GiB/時間あたり 0.000403 ドル = 7,130.97 ドル/月 (237.70 ドル/日) 

*ボリュームと容量のプールのサイズ変更を使用した動的プロビジョニング* 

* 10 TiB x 24 時間 x GiB/時間あたり 0.000403 ドル = 99.04 ドル
* 24 TiB x 96 時間 x GiB/時間あたり 0.000403 ドル = 950.80 ドル
* 6 TiB x 480 時間 x GiB/時間あたり 0.000403 ドル = 1,188.50 ドル
* 合計 = **2,238.33 ドル**

[ ![サービス レベルのコスト モデル (静的と動的の比較) を示した棒グラフ。](../media/azure-netapp-files/cost-model-example-one-pricing.png) ](../media/azure-netapp-files/cost-model-example-one-pricing.png#lightbox)

このシナリオは、静的なプロビジョニングに比べて、毎月 4,892.64 ドルの節約になります。

#### <a name="example-2-one-month-cost-with-and-without-dynamic-service-level-change"></a>例 2 : 動的なサービス レベル変更をする場合としない場合の 1 か月のコスト

容量プールのサイズ要件は同じままだがパフォーマンス要件が変動する場合は、[ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)ことを検討してください。 1 か月を通じてさまざまなタイプの容量プールをプロビジョニングおよびプロビジョニング解除することで、ジャストインタイムのパフォーマンスを提供し、パフォーマンスが必要でない期間のコストを削減することができます。 

容量要件が 24 TiB で一定であるシナリオを考えます。 ただし、パフォーマンスのニーズは、384 時間 (16 日間) は Standard サービス レベル、120 時間 (5 日間) は Premium サービス レベル、168 時間 (7 日間) は Ultra サービス レベル、そして再び 48 時間 (2 日間) は Standard サービス レベルのパフォーマンス、というように変動します。 このシナリオでは、動的なクラウド消費デプロイ プロファイルは、従来の静的なオンプレミス消費プロファイルと比べて様相が異なります。 

[ ![動的なサービス レベル変更をする場合としない場合のプロビジョニングを示した棒グラフ。](../media/azure-netapp-files/cost-model-example-two-capacity.png) ](../media/azure-netapp-files/cost-model-example-two-capacity.png#lightbox)

この場合、GiB/時間あたり 0.000202 ドル (Standard)、GiB/時間あたり 0.000403 ドル (Premium)、GiB/時間あたり 0.000538 ドル (Ultra) でそれぞれ料金が請求されると仮定すると ([価格はリージョンによって異なります](https://azure.microsoft.com/pricing/details/netapp/))、毎月のコストの内訳は次のようになります。 

*Ultra サービス レベルでの静的プロビジョニング (ピーク時のパフォーマンス)*

* 24 TiB x 720 時間 x GiB/時間あたり 0.000538 ドル = 9,519.76 ドル/月 (317.33 ドル/日) 
 
*動的なサービス レベル変更を使用した動的なプロビジョニング*

* 24 TiB x 384 時間 x GiB/時間あたり 0.000202 ドル = 1,901.31 ドル  
* 24 TiB x 120 時間 x GiB/時間あたり 0.000403 ドル = 1,188.50 ドル  
* 24 TiB x 168 時間 x GiB/時間あたり 0.000538 ドル = 2,221.28 ドル  
* 24 TiB x 48 時間 x GiB/時間あたり 0.000202 ドル = 238.29 ドル 
* 合計 = **5,554.37 ドル** 

[ ![サービス レベルのコスト モデル (静的と動的変更の比較) を示した棒グラフ。](../media/azure-netapp-files/cost-model-example-two-pricing.png) ](../media/azure-netapp-files/cost-model-example-two-pricing.png#lightbox)

このシナリオは、静的なプロビジョニングに比べて、毎月 3,965.39 ドルの節約になります。

## <a name="capacity-consumption-of-snapshots"></a>スナップショットの容量消費 

Azure NetApp Files のスナップショットの容量消費は、親ボリュームのクォータに対して課金されます。  その結果、ボリュームが属する容量プールと同じ課金レートを共有します。  ただし、アクティブ ボリュームとは違って、スナップショットの消費量は消費された増分容量に基づいて測定されます。  Azure NetApp Files のスナップショットは本質的には差分です。 データの変更率にもよりますが、スナップショットの消費容量は多くの場合、アクティブ ボリュームの論理容量を大きく下回ります。 たとえば、10 GiB の差分データのみが含まれる 500 GiB のボリュームのスナップショットがあるとします。 

アクティブなファイルシステムとスナップショットのボリューム クォータに対してカウントされる容量消費は、1000 GiB ではなく、510 GiB になります。 原則として、推奨される容量の 20% で、1 週間分のスナップショット データを保持すると想定できます (スナップショットの頻度とアプリケーションの 1 日あたりのブロック レベルの変更率によって異なります)。 

次の図にこれらの概念を示します。 

* プロビジョニング済み容量が 40 TiB の容量プールがあるとします。 プールには、次の 3 つのボリュームが含まれています。    
    * ボリューム 1 には 20 TiB のクォータが割り当てられており、消費量は 13 TiB (12 TiB アクティブ、1 TiB スナップショット) です。
    * ボリューム 2 には 1 TiB のクォータが割り当てられており、消費量は 450 GiB です。
    * ボリューム 3 には 14 TiB のクォータが割り当てられていますが、消費量は 8.8 TiB (8 TiB アクティブ、800 GiB スナップショット) です。   
* 容量プールは 40 TiB の容量 (プロビジョニングされた分量) に対して測定されます。 22.25 TiB の容量が消費されています (ボリューム 1、2、3 の 13 TiB、450 GiB、8.8 TiB のクォータ)。 容量プールには、17.75 TiB の容量が残っています。   

[ ![3 つのボリュームを持つ容量プールを示す図。](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png) ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png#lightbox)

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [リージョン間レプリケーションのコスト モデル](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [ボリューム クォータについて](volume-quota-introduction.md)
* [ボリュームの容量を監視する](monitor-volume-capacity.md)
* [容量プールまたはボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [タグを使用して課金を管理する](manage-billing-tags.md)
* [容量管理に関する FAQ](faq-capacity-management.md)
* [Azure NetApp Files 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=netapp)
