---
title: マップにバブル レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、マップにバブル レイヤーを追加する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 462d820824ad6c53ad4b93ad5c88c66128619467
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933665"
---
# <a name="add-a-bubble-layer-to-a-map"></a>マップにバブル レイヤーを追加する

この記事では、データ ソースからのポイント データをマップ上のバブル レイヤーにレンダリングする方法について説明します。 バブル レイヤーは、決まったピクセル半径を持つ円としてポイントをマップ上にレンダリングするものです。 

> [!TIP]
> バブル レイヤーの既定では、データ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。または、MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

## <a name="add-a-bubble-layer"></a>バブル レイヤーを追加する

次のコードでは、ポイントの配列がデータ ソースに読み込まれます。 次に、データ ポイントが[バブル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)に接続されます。 バブル レイヤーには、各バブルを 5 ピクセルの半径、白の塗りつぶしの色、青のストロークの色、6 ピクセルのストロークの幅でレンダリングするオプションがあります。 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>」Pen を表示します。
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>バブル レイヤーでラベルを表示する

次のコードは、バブル レイヤーを使用してマップ上にポイントをレンダリングする方法と、シンボル レイヤーを使用してラベルをレンダリングする方法を示しています。 シンボル レイヤーのアイコンを非表示にするには、アイコン オプションの `image` プロパティを `'none'` に設定します。

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>」Pen を表示します。
</iframe>

## <a name="customize-a-bubble-layer"></a>バブル レイヤーをカスタマイズする

バブル レイヤーにはごくわずかのスタイル オプションしかありません。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='バブル レイヤーのオプション' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Bubble Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)
