---
title: CloudSimple による VMware ソリューションのノード概要 -　Azure
description: CloudSimple ノードと概念について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64576861"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple ノードの概要

ノードは、以下のようになっています。

* 専用のベア メタル コンピューティングホスト。 VMware ESXi ハイパーバイザーがインストール済み。  
* プライベート クラウドを作成するにあたり購入または予約することができる計算単位  
* CloudSimple サービスが使用可能な領域で購入または保予約が可能

ノードは、プライベート クラウドの構成要素です。  プライベート クラウドを作成するには、少なくとも同じ SKU のノードが 3 つ必要です。  プライベート クラウドを展開するには、他のノードを追加します。  既存のクラスターにノードを追加することができます。 または、Azure portal 内のノードを購入して CloudSimple サービスに関連付けることで、新しいクラスターを作成することができます。  購入したすべてのノードは、CloudSimple サービスの下で表示されます。  CloudSimple ポータルの購入済みのノードからプライベート クラウドを作成します。

## <a name="purchased-nodes"></a>購入済みのノード

購入済みのノードの容量は、従量課金制です。 ノードを購入すると、オンデマンドで、VMware クラスターをすばやく拡張できます。 必要に応じて、ノードを追加したり、購入済みのノードを削除して VMware クラスターをスケール ダウンすることができます。 購入済みのノードは、月単位で課金され、購入したサブスクリプションに課金されます。

* Azure サブスクリプションをクレジット カードで支払う場合、ただちにカードに課金されます。
* 請求書による課金の場合、次回の請求書に料金が反映されます。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>CloudSimple ノード SKU による VMware ソリューション

次の種類のノードは購入または予約に利用できます。

| SKU | CS28 - ノード | CS36 - ノード |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz、28 のコア (56 HT) | 2x2.3 GHz、36 のコア (72 HT) |
| RAM | 256 GB | 512 GB |
| キャッシュ ディスク |  1.6 TB NVMe | 3.2-TB NVMe |
| 容量ディスク | 5.625 TB Raw | 11.25 TB Raw |
| ストレージの種類 | All Flash | All Flash |

## <a name="limits"></a>制限

プライベート クラウドに次のノード制限が適用されます。

| Resource | 制限 |
|----------|-------|
| プライベート クラウドを作成するための最小数のノード | 3 |
| プライベート クラウド上のクラスタ内の 最大ノード数 | 16 |
| プライベート クラウド内の最大ノード数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次の手順

* [ノードの購入](create-nodes.md)方法を説明します。
* [プライベート クラウド](cloudsimple-private-cloud.md)について説明します。