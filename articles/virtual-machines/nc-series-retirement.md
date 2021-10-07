---
title: NC シリーズの提供終了
description: 2022 年 8 月 31 日までの NC シリーズの提供終了
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: e95c32903f601a883b62a06eb1b1901c15042dbb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699846"
---
# <a name="migrate-your-nc-and-nc_promo-series-virtual-machines-by-august-31-2022"></a>2022 年 8 月 31 日までに NC および NC_Promo シリーズの仮想マシンを移行してください
データセンター テクノロジの最新のイノベーションを利用して、最新の最適化された仮想マシン インスタンスを Azure に移行する活動を続けるに際し、古いハードウェアの廃止方法を熟慮の上計画しています。 この点を念頭に置いて、2022 年 8 月 31 日に NVIDIA Tesla K80 GPU を搭載した NC (v1) GPU VM サイズは廃止されます。 

## <a name="how-does-the-nc-series-migration-affect-me"></a>NC シリーズの移行によってお客様が受ける影響  

2022 年 8 月 31 日以降、お客様のサブスクリプション内の残りの NC サイズの仮想マシンは、割り当て解除済みの状態に設定されます。 これらの仮想マシンは停止し、ホストから削除されます。 これらの仮想マシンは、割り当て解除された状態では課金されなくなります。 

この VM サイズの廃止は、[NC シリーズ](nc-series.md)の VM サイズにのみ影響します。 新しい [NCv3](ncv3-series.md)、[NC T4 v3](nct4-v3-series.md)、[ND v2](ndv2-series.md) の各シリーズの仮想マシンには影響はありません。 


## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?  
NC 仮想マシンのサイズ変更や割り当て解除が必要になります。 GPU ワークロードを別の GPU 仮想マシン サイズに移行することをお勧めします。 ワークロードを他の [GPU 高速仮想マシン サイズ](sizes-gpu.md)に移行する方法についての詳細を参照してください。

## <a name="next-steps"></a>次のステップ

ワークロードを他の GPU Azure 仮想マシンのサイズに移行する方法についての[詳細](n-series-migration.md)をご覧ください。 

ご質問がある場合は、カスタマー サポートを通じてご連絡ください。
