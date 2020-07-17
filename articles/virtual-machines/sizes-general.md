---
title: Azure の VM のサイズ - 汎用 | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまな汎用サイズを一覧表示します。 このシリーズの各サイズにおけるストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269671"
---
# <a name="general-purpose-virtual-machine-sizes"></a>汎用仮想マシンのサイズ

汎用 VM サイズは、CPU とメモリのバランスの取れた比率を提供します。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 この記事では、汎用コンピューティングのためのサービスに関する情報を提供します。

- [Av2 シリーズ](av2-series.md) VM は多様なハードウェアの種類とプロセッサにデプロイできます。 A シリーズの VM は、開発とテストのような、エントリ レベルのワークロードに最適な CPU のパフォーマンスとメモリ構成を備えています。 デプロイされるハードウェアに関係なく、実行中のインスタンスに対して一貫したプロセッサ パフォーマンスを提供するため、ハードウェアに基づいてサイズが調整されます。 このサイズがデプロイされる物理ハードウェアを判断するには、仮想マシン内から仮想ハードウェアをクエリします。 ユース ケースの例には、開発とテスト用のサーバー、低トラフィックの Web サーバー、小規模から中規模のデータベース、概念実証、コード リポジトリがあります。

  > [!NOTE]
  > A8 - A11 VM は、2021 年 3 月で廃止される予定です。 詳細については、「[HPC マイグレーション ガイド](https://azure.microsoft.com/resources/hpc-migration-guide/)」を参照してください。

- Web サーバー、小規模なデータベース、開発とテスト環境など、CPU が常時最大限のパフォーマンスを発揮している必要のないワークロードでは、[B シリーズのバースト可能な](sizes-b-series-burstable.md) VM が最適です。 このようなワークロードでは通常、負荷の急増に対応できることがパフォーマンスの要件となります。 B シリーズでは、価格を重視するベースライン パフォーマンスが設定されている VM サイズを購入でき、VM インスタンスの使用量がベース パフォーマンスを下回る場合にはクレジットが蓄積されていきます。 VM にクレジットが蓄積されていると、アプリケーションでより高い CPU パフォーマンスが必要な場合に、VM のベースラインを上回り、CPU を 100% まで使用できます。

- [Dav4 シリーズと Dasv4 シリーズ](dav4-dasv4-series.md)は、マルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを使用する新しいサイズです。最大で 256 MB の L3 キャッシュを備え、8 コアのそれぞれにその L3 キャッシュの 8 MB が専用に割り当てられ、汎用ワークロードを実行するためのカスタマー オプションが増えます。 Dav4 シリーズと Dasv4 シリーズは、D および Dsv3 シリーズと同じメモリおよびディスク構成を備えています。

- [DCv2 シリーズ](dcv2-series.md)は、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 これらのマシンは、最新世代の Intel XEON E-2288G プロセッサと SGX テクノロジによって支援されています。 Intel Turbo Boost Technology により、これらのマシンは最大 5.0 GHz まで高速化できます。 DCv2 シリーズのインスタンスを使用すると、安全なエンクレーブ ベースのアプリケーションを構築して、コードやデータを使用中に保護することができます。

- オリジナルの D シリーズの後継である [Dv2 および Dsv2 シリーズ](dv2-dsv2-series.md) VM は、より強力な CPU と最適な CPU 対メモリ構成を備えているため、ほとんどの運用環境のワークロードに適しています。 Dv2 シリーズは D シリーズよりも、およそ 35% 高速です。 Dv2 シリーズは、Intel Turbo Boost Technology 2.0 を備えた、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのプロセッサ上で実行されています。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

- [Dv3 および Dsv3 シリーズ](dv3-dsv3-series.md) VM は、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのハイパースレッド構成のプロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値を提供します。 メモリが増設 (約 3.5 GiB/vCPU から 4 GiB/vCPU) される一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Dv3 シリーズには、D および Dv2 シリーズのハイ メモリの VM サイズがなくなりました。これらは、メモリ最適化された [Ev3 および Esv3 シリーズ](ev3-esv3-series.md)に移動されました。

D シリーズのユース ケースの例には、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

## <a name="other-sizes"></a>その他のサイズ

- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
