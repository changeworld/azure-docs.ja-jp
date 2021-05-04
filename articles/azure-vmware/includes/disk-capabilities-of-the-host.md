---
title: ホストのディスク機能
description: クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 7ee15fbfd668d5db24282a6e4de8e4dfc6639c3e
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945761"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-cloud-clusters.md -->


Azure VMware Solution クラスターは、ハイパーコンバージド ベアメタル インフラストラクチャに基づいています。 ホストの RAM、CPU、ディスク容量を次の表に示します。

| ホストの種類 | CPU   | RAM (GB)  | vSAN NVMe キャッシュ階層 (TB、生)  | vSAN SSD 容量階層 (TB、生)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  デュアル Intel 18 コア 2.3 GHz  |     576      |                3.2               |                15.20               |

クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。 これらのホストはハードウェア テストに合格しており、すべてのデータが安全に削除されています。 