---
title: NV シリーズの提供終了
description: NV シリーズの提供終了 (2021 年 9 月 1 日以降)
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254275"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>2022 年 8 月 31 日までに NV および NV_Promo シリーズの仮想マシンを移行してください
データセンター テクノロジの最新のイノベーションを利用して、最新の最適化された仮想マシン インスタンスを Azure に移行する活動を続けるに際し、古いハードウェアは廃止する方法を熟慮の上計画しています。
これを念頭に置いて、NV シリーズの Azure 仮想マシンのサイズは 2022 年 9 月 1 日に廃止されます。

## <a name="how-does-the-nv-series-migration-affect-me"></a>NV シリーズの移行によってお客様が受ける影響  

2022 年 9 月 1 日以降、お客様のサブスクリプション内の残りの NV および NV_Promo サイズの仮想マシンは、割り当て解除済みの状態に設定されます。 これらの仮想マシンは停止し、ホストから削除されます。 これらの仮想マシンは、割り当て解除された状態では課金されなくなります。 

現在の VM サイズの廃止は、[NV シリーズ](nv-series.md)の VM サイズにのみ影響します。 [NVv3](nvv3-series.md) シリーズおよび [NVv4](nvv4-series.md) シリーズの仮想マシンには影響はありません。 

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?  

NV 仮想マシンのサイズ変更や割り当て解除が必要になります。 GPU の視覚化またはグラフィックス ワークロードを別の [GPU 高速仮想マシン サイズ](sizes-gpu.md)に移行することをお勧めします。

ワークロードを他の GPU Azure 仮想マシンのサイズに移行する方法についての[詳細](nv-series-migration-guide.md)をご覧ください。 

ご質問がある場合は、カスタマー サポートを通じてご連絡ください。
