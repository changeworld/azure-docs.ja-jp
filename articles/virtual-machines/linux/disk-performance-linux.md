---
title: 仮想マシンとディスクのパフォーマンス
description: VM とそれに接続されたディスクを連携させてパフォーマンスを向上させる方法について説明します
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017252"
---
# <a name="virtual-machine-and-disk-performance"></a>仮想マシンとディスクのパフォーマンス
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>非キャッシュ時とキャッシュ時の仮想マシンの制限
Premium Storage と Premium Storage キャッシュの両方に対応した仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として、Standard_D8s_v3 仮想マシンを見ていきましょう。 [Dsv3 シリーズ](../dv3-dsv3-series.md)と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

この VM とディスクの組み合わせに対して、IO アクティビティを作成するベンチマーク テストを実行してみましょう。Azure でストレージ IO のベンチマークを実行する方法の詳細については、[こちら](disks-benchmarks.md)を参照してください。 ベンチマーク ツールから、この VM とディスクの組み合わせで 22,800 IOPS を達成できることがわかります。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]