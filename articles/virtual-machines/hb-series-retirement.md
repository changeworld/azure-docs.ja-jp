---
title: HB シリーズの廃止
description: 2021 年 9 月 1 日から始まる HB シリーズの廃止作業
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 2d14b9b3adb9905cc87408db33018746e1479a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680093"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>2024 年 8 月 31 日までに HB シリーズ仮想マシンを移行する
Microsoft Azure がハイ パフォーマンス コンピューティング (HPC) に向けて HBv2 および HBv3 シリーズの仮想マシンを導入したことに伴い、これまでの HB シリーズの仮想マシンから新しい仮想マシンにワークロードを移行することをお勧めします。  

Azure [HBv2](hbv2-series.md) および [HBv3](hbv3-series.md) 仮想マシンは、広いメモリ帯域幅、改善されたリモート ダイレクト メモリ アクセス (RDMA) ネットワーキング機能、大容量かつ高速のソリッド ステート ドライブを持ち、広範囲の HPC ワークロード全体に渡って優れたコスト パフォーマンスを発揮します。 そのため、HB シリーズの Azure 仮想マシン サイズを 2024 年 8 月 31 日に廃止します。

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB シリーズの移行によってどのような影響を受けるのか?  

2024 年 8 月 31 日以降は、残存する HB サイズの仮想マシンのサブスクリプションはすべて割り当てが解除された状態に設定され、稼働を停止し、課金されなくなります。  
> [!NOTE]
> この VM サイズの廃止は、HB シリーズの VM サイズにのみ影響します。 この廃止の発表は、新しい HBv2、HBv3、HC シリーズの仮想マシンには適用しません。 

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?  

H シリーズの仮想マシンのサイズ変更または割り当ての解除が必要になります。 これまでの H シリーズ (H シリーズ プロモーションを含む) の仮想マシンから新しい仮想マシンにワークロードを移行することをお勧めします。

[HBv2](hbv2-series.md) および [HBv3](hbv3-series.md) VM は、HB シリーズと比べ、大きく改善された CPU コアアーキテクチャ、広いメモリ帯域幅、大容量の L3 キャッシュ、強化された InfinibBand ネットワーキングにより、大幅に高いレベルの HPC ワークロード パフォーマンスと費用対効果を発揮します。 その結果、HBv2 および HBv3 シリーズでは、一般に、単位パフォーマンスあたりのコストが大幅に向上する (つまり、一定量のパフォーマンスに対するコストを最小にする) のに加え、単位コストあたりのパフォーマンスが大幅に向上します (つまり、一定量の費用に対するパフォーマンスを最大にします)。

HB シリーズの VM が配置されるすべての地域に HBv2 および HBv3 シリーズの仮想マシンが配置されるため、HB シリーズの VM 上で稼働していた既存のワークロードは、地理的な配置や、それぞれの地域で提供される付加的なサービスへのアクセスについて懸念することなく移行することができます。 

[HB シリーズ](hb-series.md) VM は 2024 年 9 月までは廃止されません。そのため、移行のアセスメント、計画、実行のための長い時間をお客様に持っていただくために、事前にこのガイドを提供しています。 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>HB シリーズ Virtual Machines からのワークロード移行に関する推奨事項 

| 現在の VM サイズ | 目標とする VM サイズ | 仕様の相違点  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM  <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-45rs |Standard_HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> Standard_HB120-32rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM  <br>  メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-30rs |Standard_HB120-32rs_v3 <br> Standard_HB120-16rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |
|Standard_HB60-15rs |Standard_HB120-16rs_v3 |新しい CPU: AMD Rome および MiIan (+20-30% IPC) <br> メモリ: 最大 2 倍の容量の RAM <br> メモリ帯域幅: 最大 30% 増のメモリ帯域幅 <br> InfiniBand: 200 Gb HDR (2 倍の帯域幅) <br> 最大データ ディスク数: 最大 32 (8 倍増 ) |


### <a name="migration-steps"></a>移行の手順 
1. 移行先となるシリーズとサイズを選ぶ。 
2. 移行先の VM シリーズに割り当てられるクォータを取得する。 
3. 現在の HB シリーズの VM サイズを目標とするサイズに変更する。 


### <a name="get-quota-for-the-target-vm-family"></a>移行先の VM ファミリに割り当てられるクォータを取得する。 

[VM ファミリによる vCPU クォータの増加を要求する](../azure-portal/supportability/per-vm-quota-requests.md)ためのガイドに従ってください。


### <a name="resize-the-current-virtual-machine"></a>現在の仮想マシンのサイズを変更する
[仮想マシンのサイズを変更](resize-vm.md)できます。
