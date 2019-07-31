---
title: Azure 仮想マシン スケール セットを併置する | Microsoft Docs
description: Azure 仮想マシン スケール セット リソースの併置によってパフォーマンスが向上するしくみについて説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 555c4da18169ae9fd1e917fd0b8e3c4e98e87178
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850732"
---
# <a name="co-location"></a>コロケーション

VM 間の待機時間に最も大きな影響を及ぼすものの 1 つは、単に距離です。

## <a name="preview-proximity-placement-groups"></a>更新:近接通信配置グループ 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>次の手順

スケール セット用にの[近接配置グループ](proximity-placement-groups.md)を作成します。

