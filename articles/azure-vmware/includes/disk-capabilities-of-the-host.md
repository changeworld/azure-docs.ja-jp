---
title: ホストのディスク機能
description: クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。
ms.topic: include
ms.date: 04/23/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 29a16c506d4e381e25c9c12a424b3e1fba7ad08d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638761"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Azure VMware Solution クラスターは、ハイパーコンバージド ベアメタル インフラストラクチャに基づいています。 ホストの RAM、CPU、ディスク容量を次の表に示します。

| ホストの種類 | CPU   | RAM (GB)  | vSAN NVMe キャッシュ階層 (TB、生)  | vSAN SSD 容量階層 (TB、生)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  デュアル Intel 18 コア 2.3 GHz  |     576      |                3.2               |                15.20               |

クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。 これらのホストはハードウェア テストに合格しており、すべてのデータが安全に削除されています。 