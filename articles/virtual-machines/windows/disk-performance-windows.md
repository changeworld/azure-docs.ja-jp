---
title: 仮想マシンとディスクのパフォーマンス
description: VM とそれに接続されたディスクを連係させてパフォーマンスを向上させる方法について説明します
author: albecker1
ms.author: albecker
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: b3ef2c2c3b130478a8b2d3b3c3ce44a7c65b87fe
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663367"
---
# <a name="virtual-machine-and-disk-performance"></a>仮想マシンとディスクのパフォーマンス
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>キャッシュなしの仮想マシンとキャッシュありの仮想マシンの制限
 Premium Storage 対応と Premium Storage キャッシュ対応の仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として Standard_D8s_v3 仮想マシンを見てみましょう。 [Dsv3 シリーズ](../dv3-dsv3-series.md) と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
