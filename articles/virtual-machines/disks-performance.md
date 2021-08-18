---
title: 仮想マシンとディスクのパフォーマンス
description: 仮想マシンとそれに接続されたディスクを連携させてパフォーマンスを向上させる方法について説明します。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 64b535c717d446957e06464a7b3393565a4e250e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113362307"
---
# <a name="virtual-machine-and-disk-performance"></a>仮想マシンとディスクのパフォーマンス
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>非キャッシュ時とキャッシュ時の仮想マシンの制限
Premium Storage と Premium Storage キャッシュの両方に対応した仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として、Standard_D8s_v3 仮想マシンを見てみましょう。 [Dsv3 シリーズ](dv3-dsv3-series.md)と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

