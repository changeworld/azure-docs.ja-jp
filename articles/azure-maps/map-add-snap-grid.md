---
title: マップにスナップ グリッドを追加する | Microsoft Azure Maps
description: Azure Maps Web SDK を使用してマップにスナップ グリッドを追加する方法
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 3202461de662e0f789b6c3a6187dcfbe2fa58764
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439616"
---
# <a name="add-a-snap-grid-to-the-map"></a>マップにスナップ グリッドを追加する

スナップ グリッドを使用すると、共有のエッジとノード、および直線を持つ図形を簡単に描画できます。 グリッドへの図形のスナップは、マップ上に作成アウトラインやネットワーク パスを描画するときに便利です。

スナップ グリッドの解像度はピクセル単位です。 グリッドは正方形で、最も近い整数のズーム レベルを基準としています。 グリッドは、各ズーム レベルで、実際の物理的な領域に対して 2 倍の比率で拡大されます。

## <a name="use-a-snap-grid"></a>スナップ グリッドを使用する

`atlas.drawing.SnapGridManager` クラスを使用してスナップ グリッドを作成し、マネージャーを接続するマップへの参照を渡します。 グリッドを表示する場合は、`showGrid` オプションを `true` に設定します。 図形をグリッドにスナップするには、その図形をスナップ グリッド マネージャーの `snapShape` 関数に渡します。 位置の配列をスナップする場合は、それを `snapPositions` 関数に渡します。

次の例では、HTML マーカーをドラッグしたときにそれをグリッドにスナップします。 描画ツールは、`drawingcomplete` イベントが発生したときに描画される図形をグリッドにスナップするために使用されます。

<br/>

<iframe height="500" scrolling="no" title="スナップ グリッドを使用する" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href="https://codepen.io">CodePen</a> 上の Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) による「<a href="https://codepen.io/azuremaps/pen/rNmzvXO">スナップ グリッドを使用する</a>」 Pen を表示します。
</iframe>


## <a name="snap-grid-options"></a>スナップ グリッドのオプション

次の例は、スナップ グリッド マネージャーで使用できるさまざまなカスタマイズ オプションを示しています。 グリッド線のスタイルは、スナップ グリッド マネージャーの `getGridLayer` 関数を使用して基になる線レイヤーを取得することによってカスタマイズできます。

<br/>

<iframe height="700" scrolling="no" title="スナップ グリッドのオプション" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href="https://codepen.io">CodePen</a> 上の Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) による「<a href="https://codepen.io/azuremaps/pen/RwVZJry">スナップ グリッドのオプション</a>」 Pen を表示します。
</iframe>


## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [図形データを取得する](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [描画イベントに応答する](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [対話式操作の種類とキーボード ショートカット](drawing-tools-interactions-keyboard-shortcuts.md)