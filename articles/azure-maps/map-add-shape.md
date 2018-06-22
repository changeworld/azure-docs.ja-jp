---
title: Azure Maps を使ってシェイプを追加する | Microsoft Docs
description: Javascript マップにシェイプを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: dec9b7289927365faa9c58522df2571db99f0494
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599373"
---
# <a name="add-a-shape-to-a-map"></a>マップにシェイプを追加する

この記事では、マップに線、円、および多角形を追加する方法について説明します。 

<a id="addALine"></a>

## <a name="add-a-line"></a>線を追加する

<iframe height='500' scrolling='no' title='マップに線を追加する' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 つ目のブロックでは、線が作成されます。 線は、Feature プロパティに LineStringProperties を使った、LineString の[フィーチャー](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)です。 線を作成してそのプロパティを定義するには、`new atlas.data.Feature(new atlas.data.LineString())` を使います。 

線レイヤーは、複数の線の配列です。 コードの最後のブロックでは、マップ クラスの [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) 関数を使用して、マップに線レイヤーを追加し、線レイヤーのプロパティを定義しています。 線レイヤーのプロパティについては、[LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

<a id="addACircle"></a>

## <a name="add-a-circle"></a>円を追加する

<iframe height='500' scrolling='no' title='マップに円を追加する' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 つ目のブロックでは、円が作成されます。 円は、Feature プロパティに [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) を使った、[Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) の[フィーチャー](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)です。 円を作成してそのプロパティを定義するには、`new atlas.data.Feature(new atlas.data.Point())` を使います。

円レイヤーは、円の配列です。 コードの最後のブロックでは、マップ クラスの [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) 関数を使用して、マップに円レイヤーを追加し、円レイヤーのプロパティを定義しています。 円レイヤーのプロパティについては、[CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>多角形を追加する
<iframe height='500' scrolling='no' title='マップに多角形を追加する ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 つ目のブロックでは、多角形が作成されます。 多角形は、Feature プロパティに [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) を使った、[Polygon](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) の[フィーチャー](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)です。 多角形を作成してそのプロパティを定義するには、`new atlas.data.Feature(new atlas.data.Polygon())` を使います。 多角形のコンストラクターでは、多角形のパスを、順序付けられた座標で指定します。

多角形レイヤーは、多角形の配列です。 コードの最後のブロックでは、マップ クラスの [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) 関数を使用して、マップに多角形レイヤーを追加し、プロパティを定義しています。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 

## <a name="next-steps"></a>次の手順
マップに追加するコード例の詳細については、次の記事を参照してください。
* [カスタム HTML を追加する](./map-add-custom-html.md)
* [検索結果を表示する](./map-search-location.md)


