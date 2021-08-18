---
title: ホストのディスク機能
description: クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 847f69950a47208b27976972da8dc3427727e774
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747627"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Azure VMware Solution クラスターは、ハイパーコンバージド ベアメタル インフラストラクチャに基づいています。 ホストの RAM、CPU、ディスク容量を次の表に示します。

| ホストの種類 | CPU   | RAM (GB)  | vSAN NVMe キャッシュ階層 (TB、生)  | vSAN SSD 容量階層 (TB、生)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  デュアル Intel 18 コア 2.3 GHz  |     576      |                3.2               |                15.20               |

クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。 これらのホストはハードウェア テストに合格しており、すべてのデータが安全に削除されています。 