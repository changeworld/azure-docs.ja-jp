---
title: 仮想マシンとディスクのパフォーマンス - Linux
description: Linux で、仮想マシンとそれに接続されたディスクを連携させてパフォーマンスを向上させる方法について説明します
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591856"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>仮想マシンとディスクのパフォーマンス (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>非キャッシュ時とキャッシュ時の仮想マシンの制限
Premium Storage と Premium Storage キャッシュの両方に対応した仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として、Standard_D8s_v3 仮想マシンを見てみましょう。 [Dsv3 シリーズ](../dv3-dsv3-series.md)と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

この仮想マシンとディスクの組み合わせで、IO アクティビティを作成するベンチマーク テストを実行してみましょう。 Azure でストレージ IO のベンチマークを行う方法については、「[Azure Disk Storage 上のアプリケーションのベンチマーク](disks-benchmarks.md)」を参照してください。 ベンチマーク ツールから、この VM とディスクの組み合わせで 22,800 IOPS を達成できることがわかります。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
