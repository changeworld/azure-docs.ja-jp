---
title: Azure Maps を使用したシンボルとマーカーの追加 | Microsoft Docs
description: Javascript のマップにシンボルとマーカーを追加する方法
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d80d9a4d39a3f21539e5e6e498fc52df213a19
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248814"
---
# <a name="add-symbols-and-markers-to-a-map"></a>マップにシンボルとマーカーを追加する

この記事では、データ ソースを使用して、マップにシンボルとマーカーを追加する方法を示します。

## <a name="add-a-symbol-marker"></a>シンボル マーカーを追加する

<iframe height='500' scrolling='no' title='ピンの場所の切り替え' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a>」Pen を表示します。
</iframe>

上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 ポイントが作成され、データ ソースに追加されます。 ポイントは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) の [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) です。

3 番目のコード ブロックでは、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)が作成され、マウス クリック時にシェイプ クラスの [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) メソッドを使用して、ポイントの座標が更新されます。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  データ ソース、クリック イベント リスナー、およびシンボル レイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、ポイントが表示されます。

## <a name="add-a-custom-symbol"></a>カスタム シンボルを追加する

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) クラスの [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) プロパティを使用して、マップに [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) が追加されます。 HtmlMarker は[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、HtmlMarker が表示されます。

## <a name="add-bubble-markers"></a>バブル マーカーを追加する

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、位置の配列が定義され、[MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) オブジェクトが作成されます。 次に、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成され、MultiPoint オブジェクトがデータ ソースに追加されます。

[バブル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)によって、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)にラップされたポイントベースのデータが円としてマップにレンダリングされます。 最後のコード ブロックでは、バブル レイヤーが作成され、マップに追加されます。 バブル レイヤーのプロパティについては、[BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

MultiPoint オブジェクト、データ ソース、および多角形レイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、円が表示されます。

## <a name="add-bubble-markers-with-label"></a>ラベル付きのバブル マーカーを追加する

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>」Pen を表示します。
</iframe>

上記のコードでは、マップ上のデータを視覚化し、ラベル付けする方法を示します。 上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) オブジェクトが作成されます。 次に、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成され、そのデータ ソースにポイントが追加されます。

[バブル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)によって、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)にラップされたポイントベースのデータが円としてマップにレンダリングされます。 3 番目のコード ブロックでは、バブル レイヤーが作成され、マップに追加されます。 バブル レイヤーのプロパティについては、[BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 最後のコード ブロックでは、シンボル レイヤーが作成され、マップに追加されて、バブルのテキスト ラベルがレンダリングされます。 シンボル レイヤーのプロパティについては、[SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.symbollayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

データ ソースとレイヤーが作成され、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、データが表示されます。


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [ポップアップを追加する](./map-add-popup.md)

> [!div class="nextstepaction"]
> [図形を追加する](./map-add-shape.md)