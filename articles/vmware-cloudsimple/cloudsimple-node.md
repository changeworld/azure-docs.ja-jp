---
title: Azure VMware Solutions (AVS) - ノードの概要
description: AVS ノードと概念について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024927"
---
# <a name="avs-nodes-overview"></a>AVS ノードの概要

ノードは、AVS プライベート クラウドの構成要素です。 ノードは、以下のようになっています。

* VMware ESXi ハイパーバイザーがインストールされている専用ベア メタル コンピューティング ホスト  
* AVS プライベート クラウドを作成するために購入または予約できるコンピューティング単位
* AVS サービスが使用可能なリージョンで購入または保予約が可能

購入済みのノードから AVS プライベート クラウドを作成します。 AVS プライベート クラウドを作成するには、少なくとも同じ SKU のノードが 3 つ必要です。 AVS プライベート クラウドを拡張するには、ノードを追加します。 既存のクラスターにノードを追加するか、Azure portal でノードを購入して AVS サービスに関連付けることで、新しいクラスターを作成できます。 購入済みのすべてのノードが、AVS サービスの下に表示されます。 

## <a name="provisioned-nodes"></a>プロビジョニングされたノード

プロビジョニングされたノードでは、従量課金制の容量が提供されます。 ノードのプロビジョニングは、VMware クラスターを必要に応じて迅速にスケーリングするのに役立ちます。 必要に応じてノードを追加したり、プロビジョニングされたノードを削除して VMware クラスターをスケールダウンしたりできます。 プロビジョニングされたノードは月単位で課金され、それらがプロビジョニングされているサブスクリプションに対して請求されます。

* Azure サブスクリプションをクレジット カードで支払う場合、ただちにカードに課金されます。
* 請求書による課金の場合、次回の請求書に料金が反映されます。

## <a name="vmware-solution-by-avs-nodes-sku"></a>VMware Solution by AVS ノード SKU

次の種類のノードをプロビジョニングまたは予約できます。

| SKU           | CS28 - ノード                 | CS36 - ノード                 | CS36m - ノード                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| リージョン        | 米国東部、米国西部            | 米国東部、米国西部            | 西ヨーロッパ                 |
| CPU           | 2x2.2 GHz、28 のコア (56 HT) | 2x2.3 GHz、36 のコア (72 HT) | 2x2.3 GHz、36 のコア (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| キャッシュ ディスク    | 1.6 TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量ディスク | 5.625 TB Raw                | 11.25 TB Raw                | 15.36 TB Raw                |
| ストレージ型  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>制限

AVS プライベート クラウドには、次のノード制限が適用されます。

| リソース | 制限 |
|----------|-------|
| AVS プライベート クラウドを作成するためのノードの最小数 | 3 |
| AVS プライベート クラウド上のクラスター内のノードの最大数 | 16 |
| AVS プライベート クラウド内のノードの最大数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次のステップ

* [ノードの購入](create-nodes.md)方法を確認します。
* [AVS プライベート クラウド](cloudsimple-private-cloud.md)について確認します。
* [ノードをプロビジョニングする](create-nodes.md)方法を学習する
* [プライベート クラウド](cloudsimple-private-cloud.md)について確認します。
