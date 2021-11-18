---
title: Power BI 用 Azure Maps ビジュアルにヒート マップ レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルでヒート マップ レイヤーを使用する方法について説明します。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.custom: ''
ms.openlocfilehash: 211721d66b1a69020860f5ba24aaf2bae525afdf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717242"
---
# <a name="add-a-heat-map-layer-to-the-azure-maps-visual-for-power-bi"></a>Power BI 用 Azure Maps ビジュアルにヒート マップ レイヤーを追加する

この記事では、Power BI で Azure Maps ビジュアルにヒート マップ レイヤーを追加する方法について説明します。

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Azure マップ Visual for Power BI 用ヒート マップ レイヤー。":::

ヒート マップは密度マップとも呼ばれ、データの密度を色で表現した地図上のオーバーレイの一種です。 ヒート マップは、マップ上のデータの "ホットスポット" を示す目的で使用されることもよくあります。 ヒート マップは、多数の点によってデータセットをレンダリングする優れた方法です。 マップに多数のデータ ポイントを表示すると、パフォーマンスが低下したり、シンボルが重なってマップが使用できなくなったりします。 データをヒート マップとしてレンダリングすると、パフォーマンスが向上するだけでなく、各データ ポイントの相対的な密度を簡単に確認できるので、データの意味を理解するのに役立ちます。

ヒート マップは、ユーザーが膨大な比較データを視覚化する場合に便利です。

- 地域または国どうしで顧客満足度または店舗のパフォーマンスを比較する。
- 顧客が異なる場所にあるショッピング センターを利用する頻度を測定する。
- 膨大な統計データ セットと地理的データ セットを視覚化する。

## <a name="prerequisites"></a>前提条件

- [Power BI 用 Azure Maps ビジュアルの概要](./power-bi-visual-getting-started.md)。
- [Azure Maps Power BI ビジュアルのレイヤーの概要](./power-bi-visual-understanding-layers.md)。

## <a name="add-the-heat-map-layer"></a>ヒート マップ レイヤーを追加する

1. Power BI Desktop で、作成した Azure マップを選択します。
1. **[形式]** ウィンドウで、**[ヒート マップ]** のスイッチを **[オン]** に切り替えます。

これで、レポートに合わせてすべてのヒート マップ レイヤー設定を調整できます。

## <a name="heat-map-layer-settings"></a>ヒート マップ レイヤーの設定

**[形式]** ウィンドウ の **[ヒート マップ]** セクションでは、特定の要件を満たしたヒート マップの視覚化を柔軟にカスタマイズおよび設計できます。 **[ヒート マップ]** セクション では、次の操作を実行できます。

- 測定単位としてピクセルまたはメートルのいずれかを使用して、各データ ポイントの半径を設定します。
- ヒート マップ レイヤーの不透明度と強度をカスタマイズします。  
- size フィールドの値を各データ ポイントの重みとして使用する必要があるかどうかを指定します。
- カラー ピッカーから異なる色を選択します。
- ヒート マップ レイヤーを表示するズーム レベルの最小値と最大値を設定します。
- 3D 棒グラフ レイヤーやバブル レイヤーなど、さまざまなレイヤー間でのヒート マップ レイヤーの位置を決定します。

次の表は、**[形式]** ウィンドウの **[ヒート マップ]** セクションで使用できる主な設定を示しています。

| 設定              | 説明      |
|----------------------|------------------|
| Radius | ヒート マップ内の各データ ポイントの半径。<br /><br />単位 = ‘ピクセル’: 1 - 200 の場合に有効な値。 既定値: **20**<br />単位 = ‘メートル’: 1 - 4,000,000 の場合に有効な値|
| ユニット  | 半径の距離単位。 次のいずれかの値になります。<br /><br />**ピクセル**。 ピクセルに設定すると、ズーム レベルに関係なく、各データ ポイントのサイズは常に同じになります。<br />**メートル**。 メートルに設定すると、ズーム レベルに基づいてデータ ポイントのサイズが拡大縮小され、半径が空間的に正確になります。<br /><br /> 既定値: **ピクセル**  |
| Opacity | ヒート マップ レイヤーの不透明度を設定します。 既定値: **1**<br/>値は 0 ～ 1 の 10 進数で指定してください。 |
| 強度 | 各ヒート ポイントの強度。 強度は 0 ～ 1 の 10 進値であり、1 つのデータ ポイントを "ホット" に設定する方法を指定するために使用されます。 既定値: **0.5** |
| サイズを重みとして使用する | 各データ ポイントの重みとして size フィールド値を使用する必要があるかどうかを決定するブール値。 オンにすると、レイヤーは重み付けヒート マップとしてレンダリングされます。 既定値: **オフ** |
| グラデーション |ユーザーが低 (0%)、中 (50%)、高 (100%) のグラデーション色の 3 色を選択するカラー ピッカー。 |
| Min zoom\(最小ズーム\) |レイヤーが表示される最小ズーム レベル。 有効値は 1 ～ 22 です。 既定値: **0** |
|Max zoom\(最大ズーム\) |レイヤーが表示される最大ズーム レベル。  有効値は 1 ～ 22 です。 既定値: **22**|
|Layer position\(レイヤーの位置\) |他のマップ レイヤーに対するレイヤーの位置を指定します。 有効な値には、**[ラベルの上]**、**[ラベルの下]**、**[道路の下]** があります |

## <a name="next-steps"></a>次のステップ

マップにデータを表示する方法を変更する:

> [!div class="nextstepaction"]
> [横棒グラフ レイヤーを追加する](power-bi-visual-add-bar-chart-layer.md)

マップにコンテキストをさらに追加する:

> [!div class="nextstepaction"]
> [参照レイヤーを追加する](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)

ビジュアルをカスタマイズする:

> [!div class="nextstepaction"]
> [Power BI における色の書式設定に関するヒントとコツ](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [視覚エフェクトのタイトル、背景、および凡例をカスタマイズする](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)