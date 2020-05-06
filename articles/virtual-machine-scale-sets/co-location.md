---
title: Azure 仮想マシン スケール セットの併置
description: Azure 仮想マシン スケール セット リソースの併置によってパフォーマンスが向上するしくみについて説明します。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "79226963"
---
# <a name="co-location"></a>コロケーション

VM 間の待機時間に最も大きな影響を及ぼすものの 1 つは、単に距離です。

## <a name="preview-proximity-placement-groups"></a>プレビュー:近接通信配置グループ 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次のステップ

スケール セット用にの[近接配置グループ](proximity-placement-groups.md)を作成します。

