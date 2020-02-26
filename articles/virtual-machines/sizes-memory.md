---
title: Azure VM のサイズ - メモリ | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなメモリ最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM の分離,分離された VM,分離,分離された
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492544"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>メモリ最適化済み仮想マシンのサイズ

メモリ最適化済み VM のサイズは、リレーショナル データベース サーバー、中規模から大規模のキャッシュ、インメモリ分析に適した、メモリと CPU の高い比率を提供します。 この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

- オリジナルの D シリーズに続く [Dv2 と DSv2 シリーズ](dv2-dsv2-series-memory.md)には、より強力な CPU が備わっています。 Dv2 シリーズは D シリーズよりも、およそ 35% 高速です。 これは、Intel® Xeon® 8171M 2.1 GHz (Skylake) または Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、Intel Turbo Boost Technology 2.0 を備えています。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

    Dv2 と DSv2 シリーズは、より高速の vCPU やより優れた一時ストレージ パフォーマンスが要求される、または高いメモリ要求を持つアプリケーションに最適です。 多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。

- [Eav4 と Easv4 シリーズ](eav4-easv4-series.md)では、最大 256 MB の L3 キャッシュを備えたマルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを利用しており、ほとんどのメモリ最適化されたワークロードを実行するためのオプションが増えています。 Eav4 シリーズと Easv4 シリーズは、Ev3 および Esv3 シリーズと同じメモリおよびディスク構成を備えています。

- [Ev3 と Esv3 シリーズ](ev3-esv3-series.md) は、ハイパースレッド構成の Intel® Xeon® 8171M 2.1 GHz (Skylake) または Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) プロセッサを備えることで、大半の汎用ワークロード向けに付加価値を高め、他の多くのクラウドの汎用 VM と一線化されています。 メモリが増設 (約 7 GiB/vCPU から 8 GiB/vCPU) されている一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Ev3 は、D/Dv2 ファミリーのハイ メモリ VM サイズのフォローアップです。

- [M シリーズ](m-series.md)は、多数の vCPU (最大 128 個の vCPU) と大量のメモリ (最大 3.8 TiB) を提供します。 このシリーズも非常に大規模なデータベースや他のアプリケーションに最適であり、多数の vCPU と大量のメモリによるメリットを活用できます。

- [Mv2 シリーズ](mv2-series.md)は、クラウドの VM で最大の vCPU 数 (最大 416 個の vCPU) と最大のメモリ (最大 8.19 TiB) を提供します。 非常に大規模なデータベースや他のアプリケーションに最適であり、多数の vCPU と大量のメモリによるメリットを活用することができます。

Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。 これらの仮想マシン サイズは、コンプライアンスや規制上の要件などの要素に関連するワークロードについて、他の顧客からの高いレベルの分離を必要とするワークロードに最適です。 お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。 分離された VM オプションについては、下の仮想マシン ファミリーのページをご覧ください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。