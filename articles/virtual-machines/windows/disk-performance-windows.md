---
title: 仮想マシンとディスクのパフォーマンス - Windows
description: Windows で、仮想マシンとそれに接続されたディスクを連携させてパフォーマンスを向上させる方法について説明します
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584121"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>仮想マシンとディスクのパフォーマンス (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>キャッシュなしの仮想マシンとキャッシュありの仮想マシンの制限
 Premium Storage 対応と Premium Storage キャッシュ対応の仮想マシンでは、2 つの異なるストレージ帯域幅の制限があります。 例として Standard_D8s_v3 仮想マシンを見てみましょう。 [Dsv3 シリーズ](../dv3-dsv3-series.md) と Standard_D8s_v3 に関するドキュメントを次に示します。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

この VM とディスクの組み合わせに対して、IO アクティビティを作成するベンチマーク テストを実行してみましょう。Azure 上でストレージ IO のベンチマークを実行する方法の詳細については、[こちら](disks-benchmarks.md)を参照してください。 ベンチマーク ツールから、この VM とディスクの組み合わせで 22,800 IOPS を達成できることがわかります。

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]