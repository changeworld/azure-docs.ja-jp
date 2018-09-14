---
title: Azure Maps を使った道順の表示 | Microsoft Docs
description: 2 つの場所の間の道順を Javascript マップ上に表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781498"
---
# <a name="show-directions-from-a-to-b"></a>A から B までのルートを表示する 

この記事では、ルート要求を実行し、マップにルートを表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RBZbep/'>A から B までのルートをマップに表示する (サービス モジュール)</a>」を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックの行で、サービス クライアントがインスタンス化されます。

3 番目のコード ブロックで、マップ上の[ライン ストリング レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)が初期化されます。

4 番目のコード ブロックでは、ルートの起点と終点を表すピンを作成し、マップ上に追加しています。 手順については、「[マップにピンを追加する](map-add-pin.md)」をご覧ください。

次のコード ブロックでは、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 関数を使い、ルートの起点と終点に基づいて、マップの境界ボックスを設定しています。

6 番目のコード ブロックでは、ルート クエリを構築しています。

最後のコード ブロックでは、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを使って Azure Maps ルーティング サービスを照会し、起点と終点との間のルートを取得しています。 その後、応答は、[getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) メソッドを使って GeoJSON 形式に解析されます。 それらすべての線をマップ上に追加して、ルートがレンダリングされます。 詳しくは、[マップに線を追加する方法](./map-add-shape.md#addALine)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [ライン ストリング レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

マップに追加するコード例の詳細については、次の記事を参照してください。 
* [マップ上にトラフィックを表示する](./map-show-traffic.md)
* [マップの連動 – マウス イベント](./map-events.md)
