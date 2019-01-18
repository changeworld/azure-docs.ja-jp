---
title: Azure Maps にヒート マップ レイヤーを追加する | Microsoft Docs
description: JavaScript マップにヒート マップ レイヤーを追加する方法
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cbf134640f981056c0996ffc6768ebc1381ce2ac
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995152"
---
# <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ヒート マップは、点密度マップとも呼ばれ、一定の範囲の色を使用して、データの密度を表すために使用されるデータ視覚化の一種です。 多くの場合、マップ上のデータの "ホットスポット" を示すために使用され、大規模なポイント データ セットをレンダリングする場合に適しています。  たとえば、シンボルがマップ領域の大部分を覆うようにマップ ビュー内に何万もの点がレンダリングされ、重なり合うシンボルが増えると、データを詳しく分析することが困難になります。 ただし、これと同じデータ セットをヒート マップとして視覚化すると、ポイント データが最も密集している場所と他の領域に対する相対密度を簡単に把握できます。 ヒート マップは多くのシナリオで使用されます。 次に例をいくつか示します。

* 気温データが一般的にヒート マップとしてレンダリングされるのは、2 つのデータ ポイント間の気温の概算を示す場合です。
* ノイズ センサーのデータをヒート マップとしてレンダリングすることで、センサーがある場所のノイズの異常がわかるだけでなく、離れた場所の散逸を分析することもできます。 個々の場所のノイズ レベルは高くなくても、複数のセンサーのノイズ感知領域が重なる場合は、その重なる領域でノイズ レベルがより高くなる可能性があり、結果としてヒート マップに可視化されます。
* 各データ ポイントの強度が速度に基づいているウェイト ハイト マップとして速度を含む GPS トレースを視覚化する方法は、車両がスピードを出した場所をすばやく確認するために適しています。

> [!TIP]
> バブル レイヤーの既定では、データ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', '$type', 'Point']` に設定します。

## <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ポイントのデータ ソースを単純なヒート マップとしてレンダリングするには、データ ソースを HeatMapLayer クラスのインスタンスに渡し、それを次のようにマップに追加します。

<br/>

<iframe height='500' scrolling='no' title='単純なヒート マップ レイヤー' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a>」Pen を表示します。
</iframe>

このサンプルでは、各ヒート ポイントの半径はすべてのズーム レベルで 10 ピクセルです。 ヒート マップ レイヤーをマップに追加すると、このサンプルではラベル レイヤーの下に挿入されます。 その結果、ラベルはヒート マップの上で明瞭に見えるので、ユーザー エクスペリエンスが向上します。 このサンプルのデータは、[USGS 地震ハザード プログラム](https://earthquake.usgs.gov/)がソースであり、過去 30 日間に発生した重大な地震を表すポイントで構成されています。

## <a name="customizing-the-heat-map-layer"></a>ヒート マップ レイヤーのカスタマイズ

前の例では、半径と不透明度のオプションを設定してヒート マップをカスタマイズしました。 ヒート マップ レイヤーには、カスタマイズのためのオプションがいくつか用意されています。

* `radius`:各データ ポイントをレンダリングするときのピクセルの半径を定義します。 この半径は、固定数または式として設定できます。 ズーム レベルに基づいて半径を設定する式を使用して半径を設定すると、マップ上の一貫した空間領域を表す半径があるようにヒート マップが表示されます。
* `color`:ヒート マップの配色方法を指定します。 グラデーション カラー パレットはヒート マップによく使用されますが、ヒート マップを等高線データのように見せたい場合には段階的なカラー パレットも便利です。 このようなカラー パレットでは、最小濃度値から最大濃度値の色を定義します。 ヒート マップの色の値は、`heatmap-density` 値に対する式として指定されます。 グラデーション式のインデックス 0 の色または段階色の既定の色で、データがない領域の色、つまり背景色が定義されます。 多くの場合、この値は透明または半透明の黒に設定することが好まれます。 色の式の例を次に示します。

| グラデーション カラーの式 | 段階的な色の式 | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`:ヒート マップ レイヤーの不透明度または透明度を指定します。
* `intensity`:ヒート マップの全体的な強度を高めるために、各データ ポイントの重みに乗数を適用します。 これにより、データ ポイントの重みのわずかな違いを視覚化しやすくなります。
* `weight`:既定では、すべてのデータ ポイントの重みは 1 なので、すべてのデータ ポイントの重みは等しくなります。 weight オプションは乗数として機能し、数値または式として設定できます。 重みを 2 などの数値に設定すると、各データ ポイントがマップに 2 回配置された場合に相当するので、密度が 2 倍になります。 weight オプションを数値に設定すると、intensity オプションを使用する場合と同様にヒート マップがレンダリングされます。 ただし、式を使用する場合は、各データ ポイントの重みはさまざまな要素やそのポイントのプロパティのいくつかのメトリックに基づいて決まります。 地震データを例に取ると、各データ ポイントは 1 つの地震を表しており、各地震の重要なメトリックはマグニチュードです。 地震は常に発生していますが、大部分はマグニチュードが低く、感じることすらありません。 式にマグニチュード値を使用して weight オプションを割り当てると、より大きな地震をヒート マップ内でより適切に表すことができます。
* 最小/最大ズーム、可視、フィルターという基本のレイヤー オプションに加え、データ ソースを更新する場合の `source` オプションや、データ ソースがベクター タイル ソースの場合の `source-layer` オプションもあります。

ヒート マップ レイヤーのさまざまなオプションをテストするためのツールを次に紹介します。

<br/>

<iframe height='700' scrolling='no' title='ヒート マップ レイヤーのオプション' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a>」Pen を表示します。
</iframe>

> [!TIP]
> データ ソースに対するクラスタリングを有効にすると、相互に近いポイントはクラスター化されたポイントとしてグループ化されます。 各クラスターのポイント数は、ヒート マップの重み式として使用できます。また、レンダリングする必要があるポイント数を大幅に減らすことができます。 クラスターのポイント数は、次のようにポイント フィーチャーの point_count プロパティに格納されています。 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> クラスタリングの半径がわずか数ピクセルの場合、レンダリングによる視覚的な違いはほとんどありません。 半径が大きいほど各クラスター内でグループ化されるポイントが増えるので、ヒート マップのパフォーマンスは向上しますが、違いがより顕著になります。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](./map-add-pin.md)

