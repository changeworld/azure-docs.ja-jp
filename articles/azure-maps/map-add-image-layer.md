---
title: マップに画像レイヤーを追加する | Microsoft Azure Maps
description: マップに画像を追加する方法について説明します。 Azure Maps Web SDK を使用し、固定の座標セット上で画像レイヤーやオーバーレイ画像をカスタマイズする方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: a7d343a3ee5a37e3d96a1aadaef6bc5ab2743057
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437096"
---
# <a name="add-an-image-layer-to-a-map"></a>イメージ レイヤーをマップに追加する

この記事では、固定された一連の座標に画像をオーバーレイする方法について説明します。 以下、マップでオーバーレイできるさまざまな種類の画像のうち、いくつか例を挙げます。

* ドローンからキャプチャされた画像
* ビルのフロアプラン
* 歴史などの目的に特化したマップ画像
* 仕事の現場のブループリント
* 気象レーダーの画像

> [!TIP]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) は、マップに画像をオーバーレイする簡単な方法です。 大きな画像を読み込む場合、ブラウザーに遅延が発生する可能性があります。 その場合は、お客様の画像をタイルに分割したうえで、[TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) としてマップに読み込むことを検討してください。

イメージ レイヤーでは、次のイメージ形式がサポートされています。

- JPEG
- PNG
- BMP
- GIF (アニメーションなし)

## <a name="add-an-image-layer"></a>イメージ レイヤーを追加する

次のコードでは、1922 年のニュージャージー州ニューアークの地図の画像がマップにオーバーレイされています。 画像の URL と四隅の座標を `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 形式で渡すことによって、[ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) が作成されています。

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

<iframe height='500' scrolling='no' title='単純なイメージ レイヤー' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a>」Pen を表示します。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML ファイルをグラウンド オーバーレイとしてインポートする

このサンプルからは、KML グラウンド オーバーレイ情報を画像レイヤーとしてマップに追加する方法がわかります。 KML グランド オーバーレイからは、東西南北の座標が反時計回りで与えられます。 しかしながら、画像レイヤーでは、画像の四隅の座標が求められます。 このサンプルの KML グラウンド オーバーレイはシャルトル大聖堂のものであり、その出典は [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml) です。

このコードでは、[ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) クラスの静的 `getCoordinatesFromEdges` 関数が使用されています。 KML グラウンド オーバーレイの東西南北と回転の情報が利用され、画像の四隅が計算されます。

<br/>

<iframe height='500' scrolling='no' title='イメージ レイヤーとしての KML グラウンド オーバーレイ' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a>」Pen を表示します。
</iframe>

> [!TIP]
> イメージ レイヤー クラスの `getPixels` と `getPositions` の関数を使用して、配置されたイメージ レイヤーの地理的座標とローカル画像のピクセル座標の間で変換を行います。

## <a name="customize-an-image-layer"></a>イメージ レイヤーをカスタマイズする

イメージ レイヤーには、さまざまなスタイル設定オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='イメージ レイヤーのオプション' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](./map-add-tile-layer.md)
