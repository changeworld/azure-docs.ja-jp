---
title: お使いの環境をスケーリングする方法 ‐ Azure Time Series Insights | Microsoft Docs
description: Azure portal でお使いの Azure Time Series Insights 環境をスケーリングする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310987"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 環境をスケーリングする方法

この記事では、[Azure portal](https://portal.azure.com) を使用して Time Series Insights 環境の容量を変更する方法について説明します。 容量は、受信レート、ストレージの容量、および選択された SKU に関連するコストに適用される乗数です。

Azure ポータルを使用して、容量を特定の価格 SKU 内で増減できます。

ただし、価格レベル SKU の変更は許可されません。 たとえば、S1 価格の SKU の環境を S2 に変換したり、その逆方向に変換したりすることはできません。

## <a name="ga-limits"></a>GA の制限

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>環境の容量を変更する

1. Azure Portal で Time Series Insights 環境を見つけて選択します。

1. Time Series Insighs 環境のメニューから、 **[ストレージの構成]** を選択します。

   [![Time Series Insights の容量を構成する](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. **容量**のスライダーを調整して、受信レートとストレージ容量の要件を満たす容量を選択します。 **受信レート**、**ストレージの容量**、および**コストの見積もり**が動的に更新されて変化の影響が示されることに注目してください。

   [![容量のスライダーを使用して環境を構成する](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   または、スライダーの右側にあるテキスト ボックスに、容量の乗数の数を入力することもできます。

1. **[保存]** を選択して環境をスケーリングします。 変更がコミットされるまで、一時的に進行状況のインジケーターが表示されます。

1. 新しい容量が、[スロットルを防止するのに十分](time-series-insights-diagnose-and-solve-problems.md)であることを確認します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Time Series Insights のリテンション期間](time-series-insights-concepts-retention.md)に関するページを参照してください。

- [Azure Time Series Insights のデータ リテンションの構成](time-series-insights-how-to-configure-retention.md)について確認します。

- [環境の計画](time-series-insights-environment-planning.md)について確認します。