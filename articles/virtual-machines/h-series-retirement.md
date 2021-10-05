---
title: H シリーズの廃止
description: H シリーズの廃止 (2021 年 9 月 1 日以降)
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 98465066f79f93777255a3072c472d342b557bdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600230"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>2022 年 8 月 31 日までに H および H_Promo シリーズの仮想マシンを移行してください
Microsoft Azure には、新世代のハイ パフォーマンス コンピューティング (HPC)、汎用、およびメモリ最適化の仮想マシンが導入されているため、元の H シリーズ (H シリーズ promo を含む) の仮想マシンから新しいオファリングにワークロードを移行することをお勧めします。

Azure [HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md)、[Dv4](dv4-dsv4-series.md)、[Dav4 ](dav4-dasv4-series.md)、[Ev4 ](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md) 仮想マシンは、さまざまな HPC ワークロードにわたって、より広いメモリ帯域幅、向上したネットワーク機能、より優れたコストとパフォーマンスを備えています。 その結果、H シリーズの Azure 仮想マシンのサイズ (H8、H8m、H16、H16r、H16m、H16mr、H8 Promo、H8m Promo、H16 Promo、H16r Promo、H16m Promo、H16mr Promo) を 2022 年 8 月 31 日に廃止します。

## <a name="how-does-the-h-series-migration-affect-me"></a>H シリーズの移行によってどのような影響がありますか?  

2022 年 8 月 31 日を過ぎると、前述の H シリーズ仮想マシンの残っているサブスクリプションはすべて割り当てが解除された状態に設定され、稼働が停止し、課金されなくなります。 

現在の VM サイズの廃止は、H シリーズ promo を含む [H シリーズ](h-series.md)の VM サイズにのみ影響があります。 

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?  

H シリーズの仮想マシンのサイズ変更または割り当ての解除が必要になります。 これまでの H シリーズ (H シリーズ promo を含む) の仮想マシンから新しい仮想マシンにワークロードを移行することをお勧めします。

HPC ワークロードの場合、[HC](hc-series.md)、[HBv2](hbv2-series.md)、[HBv3](hbv3-series.md) VM は、H シリーズと比べ、大きく改善された CPU コアアーキテクチャ、広いメモリ帯域幅、大容量の L3 キャッシュ、強化された InfiniBand ネットワークのハードウェアとソフトウェアのサポートにより、大幅に高いレベルの HPC ワークロード パフォーマンスと費用対効果を発揮します。 その結果、HC、HBv2、HBv3 シリーズでは、一般に、単位パフォーマンスあたりのコストが大幅に向上し (一定量のパフォーマンスに対するコストを最小にする)、単位コストあたりのパフォーマンスが大幅に向上します (一定量の費用に対するパフォーマンスを最大にします)。 

汎用ワークロードの場合、[Dv4](dv4-dsv4-series.md)、[Dav4](dav4-dasv4-series.md)、Dv5 VM は、同一またはそれ以上のコア数で同等またはそれ以上の CPU パフォーマンスを発揮し、物理 CPU コアあたりのメモリ量は同等であり、Azure ネットワーク機能が向上し、全体的なコストも抑えられます。 

メモリ最適化ワークロードの場合、[Ev4](ev4-esv4-series.md)、[Eav4](eav4-easv4-series.md)、Ev5 VM は、同一またはそれ以上のコア数で同等またはそれ以上の CPU パフォーマンスを発揮し、物理 CPU コアあたりのメモリ量は同等であり、Azure ネットワーク機能が向上し、全体的なコストも抑えられます。 

[H シリーズ](h-series.md) VM (H シリーズ Promo を含む) は 2022 年 9 月までは廃止されません。そのため、移行のアセスメント、計画、実行のための長い時間をお客様に持っていただくために、事前にこのガイドを提供しています。 


### <a name="migration-steps"></a>移行の手順 
1. 移行先となるシリーズとサイズを選ぶ。 
2. ターゲット VM シリーズのクォータを取得します 
3. 現在の H シリーズの VM サイズをターゲット サイズに変更します 


### <a name="breaking-changes"></a>重大な変更 
InfiniBand ネットワーク インターフェイスを公開する H シリーズ VM サイズ (VM サイズ名に 'r' が付いているものなど) を使用していて、新しい VM サイズでも InfiniBand ネットワークをサポートしたい場合、InfiniBand ドライバーが組み込まれたレガシ OS イメージ (CentOS 7.4 以前、Windows Server 2012) は使用できなくなります。 代わりに、最新のオペレーティング システム (CentOS 7.5 以降、Windows Server 2016 以降) と OFED ドライバーをサポートする最新の OS イメージ (Azure Marketplace に掲載されているものなど) を使用してください。 それぞれの VM サイズでサポートされる OS を含む[サポートされるソフトウェア スタック](hbv3-series.md#get-started)を参照してください。 


### <a name="get-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する 

[VM ファミリによる vCPU クォータの増加を要求する](../azure-portal/supportability/per-vm-quota-requests.md)ためのガイドに従ってください。


### <a name="resize-the-current-virtual-machine"></a>現在の仮想マシンのサイズを変更する
[仮想マシンのサイズを変更](resize-vm.md)できます。
