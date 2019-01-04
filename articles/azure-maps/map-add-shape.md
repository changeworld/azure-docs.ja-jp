---
title: Azure Maps を使ってシェイプを追加する | Microsoft Docs
description: Javascript マップにシェイプを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0753e4f2bee1259356f1c27c3b9967a914b27798
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888641"
---
# <a name="add-a-shape-to-a-map"></a>マップにシェイプを追加する

この記事では、マップに [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) を追加して、マップ上の既存のシェイプのプロパティを更新する方法を説明します。

<a id="addALine"></a>

## <a name="add-a-line"></a>線を追加する

<iframe height='500' scrolling='no' title='マップに線を追加する' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) オブジェクトが作成され、データ ソースに追加されます。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされた線オブジェクトがレンダリングされます。 最後のコード ブロックでは、線レイヤーが作成され、マップに追加されます。 線レイヤーのプロパティについては、[LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linestringlayeroptions?view=azure-iot-typescript-latest) に関するページを参照してください。 データ ソースと線レイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、線が表示されます。

## <a name="customize-a-line-layer"></a>線レイヤーをカスタマイズする

線レイヤーにはいくつかのスタイル オプションがあります。 次のツールでそれらを試してください。

<br/>

<iframe height='700' scrolling='no' title='線レイヤーのオプション' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a>」Pen を表示します。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>円を追加する

<iframe height='500' scrolling='no' title='マップに円を追加する' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 円の [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) は [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) であり、`subType` プロパティは "circle"、`radius` プロパティ値は meters に設定されています。 subType が circle の Point フィーチャーがデータ ソースに追加されると、マップ内で円の多角形に変換されます。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 最後のコード ブロックでは、多角形レイヤーが作成され、マップに追加されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソースと多角形レイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、円が表示されます。

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>多角形を追加する

多角形をマップに追加できる方法は 2 つあります。 次の例で両方の方法について説明します。

### <a name="use-polygon-layer"></a>多角形レイヤーを使用する 

<iframe height='500' scrolling='no' title='マップに多角形を追加する ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) は座標の配列から作成され、データ ソースに追加されます。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 最後のコード ブロックでは、多角形レイヤーが作成され、マップに追加されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソースと多角形レイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、多角形が表示されます。

### <a name="use-polygon-and-line-layer"></a>多角形と線レイヤーを使用する

<iframe height='500' scrolling='no' title='多角形と多角形を追加する線レイヤー' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) は座標の配列から作成され、データ ソースに追加されます。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) は、複数の線の配列です。 線レイヤーのプロパティについては、[LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) に関するページを参照してください。 3 番目のコード ブロックでは、多角形と線レイヤーが作成されます。

最後のコード ブロックでは、マップに多角形と線レイヤーが追加されます。 データ ソースとレイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、多角形が表示されます。

## <a name="customize-a-polygon-layer"></a>ポリゴン レイヤーをカスタマイズする

Polygon レイヤーにはごくわずかのスタイル オプションしかありません。 次のツールでそれらを試してください。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>」Pen を表示します。
</iframe>

## <a name="update-a-shape"></a>シェイプを更新する

Shape クラスは [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) または [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) をラップしているため、更新や管理が簡単です。
`new Shape(data: Feature<data.Geometry, any>)` はシェイプ オブジェクトを作成し、指定したフィーチャーで初期化します。

<br/>

<iframe height='500' scrolling='no' title='シェイプのプロパティを更新する' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a>」Pen を表示します。
</iframe>

上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

ポイントは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) クラスの [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) です。 2 番目のコード ブロックでは、HTML スライダー要素の半径の値が初期化されて、ポイント オブジェクトが作成され、[Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラス オブジェクトにラップされます。

3 番目のコード ブロックでは、HTML 範囲スライダー要素から値が取得され、shape クラスの [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) メソッドを使用して、半径値が変更されます。

4 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 ポイントがデータ ソースに追加されます。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 3 番目のコード ブロックでは、多角形レイヤーが作成されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソース、クリック イベント リスナー、および多角形レイヤーが作成され、マップの [イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内に追加されるため、マップが完全に読み込まれた後に、ポイントが表示されます。

## <a name="next-steps"></a>次の手順

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [カスタム HTML を追加する](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [検索結果を表示する](./map-search-location.md)
