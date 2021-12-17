---
title: NV シリーズの提供終了
description: NV シリーズの提供終了 (2021 年 9 月 1 日以降)
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 666b681d33c41482bcf33947c38be33aeee29783
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436448"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>2022 年 8 月 31 日までに NV および NV_Promo シリーズの仮想マシンを移行してください

データセンター テクノロジの最新のイノベーションを利用することにより、Azure では最適化された最新の仮想マシン (VM) インスタンスが継続的に利用されています。 イノベーションを行うにあたり、最新ではなくなったハードウェアの提供を終了する方法についても慎重に計画しています。 こうした事情で、NV シリーズの Azure VM サイズの提供を 2022 年 9 月 1 日に終了することになりました。

## <a name="how-does-the-nv-series-migration-affect-me"></a>NV シリーズの移行によってお客様が受ける影響

2022 年 9 月 1 日以降、お客様のサブスクリプションに残っている NV および NV_Promo サイズの VM は、割り当てが解除された状態に設定されます。 これらの VM は停止し、ホストから削除されます。 これらの VM は、割り当てが解除された状態で課金されなくなります。 

現在の VM サイズの提供終了は、[NV シリーズ](nv-series.md)の VM サイズにのみ影響します。 この提供終了は、[NVv3](nvv3-series.md) シリーズや [NVv4](nvv4-series.md) シリーズの VM には影響しません。 

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?

NV VM のサイズを変更するか、割り当てを解除することが必要になります。 GPU 視覚化またはグラフィックス ワークロードを、別の [GPU 高速化 VM サイズ](sizes-gpu.md)に移行することをお勧めします。

ワークロードを他の GPU Azure VM サイズに移行する方法の詳細については、[こちら](nv-series-migration-guide.md)を参照してください。 

ご質問がある場合は、カスタマー サポートを通じてご連絡ください。
