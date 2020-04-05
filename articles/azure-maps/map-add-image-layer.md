---
title: マップに画像レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用してマップ上に画像をオーバーレイする方法を示します。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209717"
---
# <a name="add-an-image-layer-to-a-map"></a>イメージ レイヤーをマップに追加する

この記事では、固定された一連の座標に画像をオーバーレイする方法について説明します。 以下、マップでオーバーレイできるさまざまな種類の画像のうち、いくつか例を挙げます。

* ドローンからキャプチャされた画像
* ビルのフロアプラン
* 歴史などの目的に特化したマップ画像
* 仕事の現場のブループリント
* 気象レーダーの画像

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) は、マップに画像をオーバーレイする簡単な方法です。 大きな画像を読み込む場合、ブラウザーに遅延が発生する可能性があります。 その場合は、お客様の画像をタイルに分割したうえで、[TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) としてマップに読み込むことを検討してください。

イメージ レイヤーでは、次のイメージ形式がサポートされています。

- JPEG
- PNG
- BMP
- GIF (アニメーションなし)

## <a name="add-an-image-layer"></a>イメージ レイヤーを追加する

次のコードでは、[1922 年のニュージャージー州ニューアークの地図](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)の画像がマップにオーバーレイされています。 画像の URL と四隅の座標を [ 形式で渡すことによって、](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)ImageLayer`[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` が作成されています。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

上記のコードの完全な実行コード サンプルを次に示します。

<br/>

<iframe height='500' scrolling='no' title='単純なイメージ レイヤー' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/eQodRo/'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io'>Simple Image Layer</a>」Pen を表示します。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML ファイルをグラウンド オーバーレイとしてインポートする

このサンプルからは、KML グラウンド オーバーレイ情報を画像レイヤーとしてマップに追加する方法がわかります。 KML グランド オーバーレイからは、東西南北の座標が反時計回りで与えられます。 しかしながら、画像レイヤーでは、画像の四隅の座標が求められます。 このサンプルの KML グラウンド オーバーレイはシャルトル大聖堂のものであり、その出典は [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml) です。

このコードでは、`getCoordinatesFromEdges`ImageLayer[ クラスの静的 ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 関数が使用されています。 KML グラウンド オーバーレイの東西南北と回転の情報が利用され、画像の四隅が計算されます。

<br/>

<iframe height='500' scrolling='no' title='イメージ レイヤーとしての KML グラウンド オーバーレイ' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/EOJgpj/'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io'>KML Ground Overlay as Image Layer</a>」Pen を表示します。
</iframe>

## <a name="customize-an-image-layer"></a>イメージ レイヤーをカスタマイズする

イメージ レイヤーには、さまざまなスタイル設定オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='イメージ レイヤーのオプション' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/RqOGzx/'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io'>Image Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](./map-add-tile-layer.md)