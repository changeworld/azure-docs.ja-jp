---
title: HB シリーズの提供終了
description: HB シリーズの提供終了が 2021 年 9 月 1 日に始まりました。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712157"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>2024 年 8 月 31 日までに HB シリーズ仮想マシンを移行する

Microsoft Azure では、ハイパフォーマンス コンピューティング (HPC) のために HBv2 と HBv3 シリーズの仮想マシン (VM) が導入されました。 このため、元の HB シリーズの VM から新しいオファリングにワークロードを移行することをお勧めします。

Azure [HBv2](hbv2-series.md) および [HBv3](hbv3-series.md) VM は、広いメモリ帯域幅、改善されたリモート ダイレクト メモリ アクセス (RDMA) ネットワーキング機能、大容量かつ高速のソリッド ステート ドライブを持ち、さまざまな HPC ワークロード全体に渡って優れたコスト パフォーマンスを発揮します。 その結果、HB シリーズの Azure VM のサイズは 2024 年 8 月 31 日に提供終了になります。

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB シリーズの移行によってどのような影響を受けるのか?

2024 年 8 月 31 日以降、残りの HB サイズの VM サブスクリプションは、割り当て解除状態に設定されます。 これらは動作を停止し、課金料金が発生しなくなります。

> [!NOTE]
> この VM サイズの提供終了は、HB シリーズの VM サイズにのみ影響します。 この提供終了のお知らせは、新しい HBv2、HBv3、HC シリーズの VM には適用されません。

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?

H シリーズ VM のサイズを変更するか、割り当てを解除することが必要になります。 元の H シリーズ VM と H シリーズ Promo VM から新しいオファリングにワークロードを移行することをお勧めします。

[HBv2](hbv2-series.md) と [HBv3](hbv3-series.md) の VM は、次の理由により、HPC ワークロードのパフォーマンスとコスト効率が大幅に向上しています。

- CPU コア アーキテクチャの大幅な改良。
- より高いメモリ帯域幅。
- より大きい L3 キャッシュ。
- HB シリーズよりも強化された InfiniBand ネットワーク。

その結果、HBv2 および HBv3 シリーズでは、一般に、単位パフォーマンスあたりのコストが大幅に向上し (一定量のパフォーマンスに対するコストを最小にする)、単位コストあたりのパフォーマンスが大幅に向上します (一定量の費用に対するパフォーマンスを最大にします)。

HB シリーズの VM が含まれているすべてのリージョンには、HBv2 と HBv3 シリーズの VM が含まれています。 HB シリーズ VM 上で実行される既存のワークロードは、地理的な配置またはそれらの領域での追加のサービスへのアクセスを心配せずに移行できます。

[HB シリーズ](hb-series.md) VM は、2024 年 9 月までは廃止されません。 移行を評価、計画、実行するための時間的余裕が十分あるように、前もってこのガイドを提供しています。

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>HB シリーズ VM からのワークロード移行に関する推奨事項

| 現在の VM サイズ | ターゲット VM サイズ | 仕様の違い  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM  <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM  <br>  メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |

### <a name="migration-steps"></a>移行の手順

1. 移行用のシリーズとサイズを選択します。
1. ターゲット VM シリーズのクォータを取得します。
1. 現在の HB シリーズの VM サイズを目標とするサイズに変更する。

### <a name="get-a-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する

ガイドに従って、[VM ファミリによる vCPU クォータの増加をリクエストします](../azure-portal/supportability/per-vm-quota-requests.md)。

### <a name="resize-the-current-vm"></a>現在の VM のサイズを変更する

[仮想マシンのサイズを変更](resize-vm.md)できます。
