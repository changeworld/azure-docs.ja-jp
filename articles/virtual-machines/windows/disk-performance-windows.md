---
title: 仮想マシンとディスクのパフォーマンス
description: VM とそれに接続されたディスクを連係させてパフォーマンスを向上させる方法について説明します
author: albecker1
ms.author: albecker
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 18c07fceb65623c286b31398314e2b6f124955a6
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540047"
---
# <a name="virtual-machine-and-disk-performance"></a>仮想マシンとディスクのパフォーマンス
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>キャッシュなしの仮想マシンとキャッシュありの仮想マシンの制限
 Premium Storage 対応と Premium Storage キャッシュ対応の仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として Standard_D8s_v3 仮想マシンを見てみましょう。 [Dsv3 シリーズ](../dv3-dsv3-series.md) と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
