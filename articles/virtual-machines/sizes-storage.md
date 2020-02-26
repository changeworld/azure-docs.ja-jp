---
title: Azure VM のサイズ - ストレージ | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなストレージ最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 17cceb04d3d6e559cd6f2b49674f6d408a0650a4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492380"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>ストレージ最適化済み仮想マシンのサイズ

ストレージ最適化済み VM サイズは高いディスク スループットと IO を実現し、ビッグ データ、SQL、NoSQL データベース、データ ウェアハウス、および大規模なトランザクション データベースに最適です。  例としては、Cassandra、MongoDB、Cloudera、および Redis があります。 この記事では、最適化された各サイズのローカル ストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

[Lsv2 シリーズ](lsv2-series.md)は、2.55 GHz の全コア ブーストと 3.0 GHz の最大ブーストが可能な [AMD EPYC<sup>TM</sup> 7551 プロセッサ](https://www.amd.com/en/products/epyc-7000-series)上で実行され、高スループット、低待ち時間、直接マッピングされたローカル NVMe ストレージを特長としています。 Lsv2 シリーズの VM には、同時マルチスレッド構成で 8 から 80 vCPU のサイズが用意されています。  vCPU あたり 8 GiB のメモリ、8 vCPU あたり 1 つの 1.92 TB NVMe SSD M.2 デバイスが用意され、L80s v2 では最大 19.2 TB (10 x 1.92 TB) を使用できます。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

[Windows](windows/storage-performance.md) または [Linux](linux/storage-performance.md) 用の Lsv2 シリーズの仮想マシンでパフォーマンスを最適化する方法について説明します。