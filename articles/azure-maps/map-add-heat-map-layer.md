---
title: マップにヒート マップ レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、マップにヒート マップ レイヤーを追加する方法を示します。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ead30a80a6568e72f922f355916d31121b49a93b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911221"
---
# <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ヒート マップは、点密度マップとも呼ばれ、一定の範囲の色を使用して、データの密度を表すために使用されるデータ視覚化の一種です。 多くの場合、マップ上のデータの "ホットスポット" を示すために使用され、大規模なポイント データセットをレンダリングする場合に適しています。 

たとえば、マップ ビュー内の何万もの点をシンボルとしてレンダリングすると、マップ領域の大部分が覆われます。 これにより、シンボルの多くが重なり合う可能性が高くなり、データから分析情報を得ることが難しくなります。 ただし、これと同じデータセットをヒート マップとして視覚化すると、ポイント データが最も密集している場所と他の領域に対する相対密度を簡単に把握できます。

ヒート マップは、次のようなさまざまなシナリオで使用できます。

- **気温データ**: 2 つのデータ ポイント間の気温の近似値が提供されます。
- **ノイズ センサーのデータ**: センサーがある場所のノイズの強度が示されるだけでなく、離れた場所の散逸を分析情報も提供されます。 個々の場所のノイズ レベルは高くないかもしれません。 しかし、複数のセンサーのノイズ感知領域が重なる場合は、その重なる領域でノイズ レベルがより高くなる可能性があり、結果としてヒート マップに可視化されます。
- **GPS トレース**: 速度を、重み付けされたハイト マップとして含めます。ここで、各データ ポイントの強度は速度に基づいています。 たとえば、これにより、車両のスピードが速められた場所を確認することができます。

> [!TIP]
> ヒート マップ レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。 MultiPoint フィーチャーも含める場合は、レイヤーの `filter` プロパティを `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ポイントのデータ ソースをヒート マップとしてレンダリングするには、ご自分のデータ ソースを `HeatMapLayer` クラスのインスタンスに渡し、それをマップに追加します。

次のコードでは、各ヒート ポイントの半径はすべてのズーム レベルで 10 ピクセルです。 ヒート マップ レイヤーをマップに追加すると、このサンプルではそれがラベル レイヤーの下に挿入されて、ユーザー エクスペリエンスが向上します。 ラベルはヒート マップの上に明確に表示されます。 このサンプルのデータは、[USGS 地震ハザード プログラム](https://earthquake.usgs.gov/)がソースであり、過去 30 日間に発生した重大な地震を表しています。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

上記のコードの完全な実行コード サンプルを次に示します。

<br/>

<iframe height='500' scrolling='no' title='単純なヒート マップ レイヤー' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a>」Pen を表示します。
</iframe>

## <a name="customize-the-heat-map-layer"></a>ヒート マップ レイヤーをカスタマイズする

前の例では、半径と不透明度のオプションを設定してヒート マップをカスタマイズしました。 ヒート マップ レイヤーには、次のようなカスタマイズのためのオプションがいくつか用意されています。

* `radius`:各データ ポイントをレンダリングするときのピクセルの半径を定義します。 この半径は、固定数または式として設定できます。 式を使用することにより、ズーム レベルに基づいて半径をスケーリングし、マップ上の一貫した空間領域 (たとえば、5 マイルの半径) を表すことができます。
* `color`:ヒート マップの配色方法を指定します。 色のグラデーションはヒート マップの一般的な機能であり、`interpolate` 式を使用してその効果を実現できます。 また、`step` 式を使用してヒート マップを色分けし、密度を、等高線またはレーダー スタイル マップのような範囲に視覚的に分割することもできます。 このようなカラー パレットでは、最小濃度値から最大濃度値の色を定義します。 

  ヒート マップの色の値は、`heatmap-density` 値に対する式として指定します。 補間式内のインデックス 0 の色、または段階式の既定の色では、データのない領域の色が定義されます。 これを使用すれば、背景色を定義することができます。 多くの場合、この値は透明、または半透明の黒に設定されます。 
   
  色の式の例を次に示します。

  | 補間色の式 | 段階的な色の式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] |   

- `opacity`:ヒート マップ レイヤーの不透明度または透明度を指定します。
- `intensity`:ヒート マップの全体的な強度を高めるために、各データ ポイントの重みに乗数を適用します。 これにより、データ ポイントの重みのわずかな違いを視覚化しやすくなります。
- `weight`:既定では、データ ポイントはすべて、重みが 1 となり、均等に重み付けされています。 weight オプションは乗数として機能し、数値または式として設定できます。 2 などの数値を重みとして設定すると、それは各データ ポイントをマップに 2 回配置することに相当するので、密度が 2 倍になります。 weight オプションを数値に設定すると、intensity オプションを使用する場合と同様にヒート マップがレンダリングされます。 

  ただし、式を使用する場合は、各データ ポイントの重みを、各データ ポイントのプロパティに基づいて決めることができます。 たとえば、各データ ポイントが地震を表しているとします。 各地震データ ポイントの重要なメトリックは、マグニチュード値です。 地震は常に発生していますが、大部分はマグニチュードが低く、感じることすらありません。 式にマグニチュード値を使用して各データ ポイントに重みを割り当てると、地震の深刻さをヒート マップ内でよりわかりやすく表すことができます。
- `source` および `source-layer`:データ ソースを更新することができます。

ヒート マップ レイヤーのさまざまなオプションをテストするためのツールを次に紹介します。

<br/>

<iframe height='700' scrolling='no' title='ヒート マップ レイヤーのオプション' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a>」Pen を表示します。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一貫性のあるズーム可能なヒート マップ

既定では、ヒート マップ レイヤーにレンダリングされるデータ ポイントの半径には、すべてのズーム レベルの固定ピクセル半径が含まれます。 マップをズームすると、データがまとめて集計され、ヒート マップ レイヤーの外観が変化します。 

`zoom` 式を使用すると、各データ ポイントによってマップの同じ物理領域がカバーされるように、各ズーム レベルの半径をスケーリングできます。 これにより、ヒート マップ レイヤーの外観は、より静的で一貫性の高いものになります。 マップの各ズーム レベルは、垂直方向および水平方向のピクセル数がすぐ下のズーム レベルの 2 倍になっています。 

ズーム レベルごとに半径が 2 倍になるようにスケーリングすると、すべてのズーム レベルで外観に一貫性のあるヒート マップが作成されます。 これを行うには、次のサンプルに示すように、基数 2 の `exponential interpolation` 式で `zoom` を使用します。 マップをズームして、ヒート マップがズーム レベルに応じてどのように変化するかを確認してください。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一貫性のあるズーム可能なヒート マップ" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によるペンの<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一貫性のあるズーム可能なヒート マップ</a>をご覧ください。
</iframe>

> [!TIP]
> データ ソースに対するクラスタリングを有効にすると、相互に近いポイントはクラスター化されたポイントとしてグループ化されます。 各クラスターのポイント数は、ヒート マップの重み式として使用できます。また、レンダリングする必要があるポイント数を大幅に減らすことができます。 クラスターのポイント数は、ポイント フィーチャーの `point_count` プロパティに格納されています。 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> クラスタリングの半径がわずか数ピクセルの場合、レンダリングによる視覚的な違いはほとんどありません。 半径が大きいほど各クラスター内でグループ化されるポイントが増えるので、ヒート マップのパフォーマンスは向上します。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
