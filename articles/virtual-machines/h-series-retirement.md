---
title: H シリーズの提供終了
description: H シリーズの提供終了が 2021 年 9 月 1 日に始まりました。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705251"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>2022 年 8 月 31 日までに H シリーズと H シリーズ Promo の仮想マシンを移行してください

Microsoft Azure には、ハイ パフォーマンス コンピューティング (HPC)、汎用、メモリ最適化の仮想マシン (VM) の新しい世代が導入されました。 このため、元の H シリーズと H シリーズ Promo の VM から新しい製品にワークロードを移行することをお勧めします。

Azure [HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md)、[Dv4](dv4-dsv4-series.md)、[Dav4 ](dav4-dasv4-series.md)、[Ev4 ](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md) VM は、さまざまな HPC ワークロードにわたって、より広いメモリ帯域幅、向上したネットワーク機能、より優れたコストとパフォーマンスを備えています。 2022 年 8 月 31 日に、次の H シリーズの Azure VM サイズは廃止されます。

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 Promo
- H8m Promo
- H16 Promo
- H16r Promo
- H16m Promo
- H16mr Promo

## <a name="how-does-the-h-series-migration-affect-me"></a>H シリーズの移行によってどのような影響がありますか?

2022 年 8 月 31 日を過ぎると、前の一覧で残っている H シリーズ VM サブスクリプションは、割り当て解除済みの状態に設定されます。 これらは動かなくなり、課金料金が発生しなくなります。

現在の VM サイズの廃止は、H シリーズ Promo を含む [H シリーズ](h-series.md)の VM サイズにのみ影響があります。

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?

お使いの H シリーズ VM のサイズを変更するか、割り当てを解除することが必要になります。 元の H シリーズ VM と H シリーズ Promo VM から新しいオファリングにワークロードを移行することをお勧めします。

**HPC ワークロード:** [HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md) の各 VM は、次の理由により、HPC ワークロードのパフォーマンスとコスト効率が大幅に向上しています。

- CPU コア アーキテクチャの大幅な改良。
- より高いメモリ帯域幅。
- より大きい L3 キャッシュ。
- H シリーズと比較して強化された InfiniBand ネットワークのハードウェアとソフトウェアのサポート。

その結果、HC、HBv2、HBv3 シリーズでは、一般に、単位パフォーマンスあたりのコストが大幅に向上し (一定量のパフォーマンスに対するコストを最小にする)、単位コストあたりのパフォーマンスが大幅に向上します (一定量の費用に対するパフォーマンスを最大にします)。

**汎用ワークロード:** [Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md)、Dv5 VM は、同一またはそれ以上のコア数で同等またはそれ以上の CPU パフォーマンスを発揮し、物理 CPU コアあたりのメモリ量は同等であり、Azure ネットワーク機能が向上し、全体的なコストも抑えられます。

**メモリ最適化ワークロード:** [Ev4](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md)、Ev5 VM は、同一またはそれ以上のコア数で同等またはそれ以上の CPU パフォーマンスを発揮し、物理 CPU コアあたりのメモリ量は同等であり、Azure ネットワーク機能が向上し、全体的なコストも抑えられます。

[H シリーズ](h-series.md)と H シリーズ Promo の VM は、2022 年 9 月までは廃止されません。 移行を評価、計画、実行するための時間的余裕が十分確保されるように、前もってこのガイドを提供しています。

### <a name="migration-steps"></a>移行の手順

1. 移行用のシリーズとサイズを選択します。
1. ターゲット VM シリーズのクォータを取得します。
1. 現在の H シリーズの VM サイズをターゲット サイズに変更します。

### <a name="breaking-changes"></a>重大な変更

InfiniBand ネットワーク インターフェイスを公開する H シリーズ VM サイズ (VM サイズ名に "r" が付いているサイズなど) を使用していて、新しい VM サイズでも InfiniBand ネットワークをサポートしたい場合、InfiniBand ドライバーが組み込まれたレガシ OS イメージ (CentOS 7.4 以前、Windows Server 2012) は使用できなくなります。

代わりに、最新のオペレーティング システム (CentOS 7.5 以降、Windows Server 2016 以降) と OFED ドライバーをサポートする最新の OS イメージ (Azure Marketplace で利用できるものなど) を使用してください。 それぞれの VM サイズでサポートされる OS を含む[サポートされるソフトウェア スタック](hbv3-series.md#get-started)を参照してください。

### <a name="get-a-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する

ガイドに従って、[VM ファミリによる vCPU クォータの増加をリクエストします](../azure-portal/supportability/per-vm-quota-requests.md)。

### <a name="resize-the-current-vm"></a>現在の VM のサイズを変更する

[仮想マシンのサイズを変更](resize-vm.md)できます。
