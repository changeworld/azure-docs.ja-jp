---
title: Azure Maps を使った道順の表示 | Microsoft Docs
description: 2 つの場所の間の道順を Javascript マップ上に表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729062"
---
# <a name="show-directions-from-a-to-b"></a>A から B までのルートを表示する

この記事では、ルート要求を実行し、マップにルートを表示する方法について説明します。

これには 2 つ方法があります。 最初の方法は、サービス モジュールを介して [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) にクエリを行う方法です。 2 番目の方法は、API に対して [XMLHttpRequest](https://xhr.spec.whatwg.org/) を実行する方法です。 以下で両方の方法を説明します。

## <a name="query-the-route-via-service-module"></a>サービス モジュールを使用してルートをクエリする

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RBZbep/'>A から B までのルートをマップに表示する (サービス モジュール)</a>」を表示します。
</iframe>

コードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックの行で、サービス クライアントがインスタンス化されます。

3 番目のコード ブロックで、マップ上の[ライン ストリング レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)が初期化されます。

4 番目のコード ブロックでは、ルートの起点と終点を表すピンを作成し、マップ上に追加しています。 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) の使用方法については、[マップにピンを追加する](map-add-pin.md)に関する記事を参照してください。

次のコード ブロックでは、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 関数を使い、ルートの起点と終点に基づいて、マップの境界ボックスを設定しています。

6 番目のコード ブロックでは、ルート クエリを構築しています。

最後のコード ブロックでは、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを使って Azure Maps ルーティング サービスを照会し、起点と終点との間のルートを取得しています。 その後、応答は、[getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) メソッドを使って GeoJSON 形式に解析されます。 それらすべての線をマップ上に追加して、ルートがレンダリングされます。 詳しくは、[マップに線を追加する方法](./map-add-shape.md#addALine)に関するページをご覧ください。

## <a name="query-the-route-via-xmlhttprequest"></a>XMLHttpRequest を使用してルートをクエリする

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>」Pen を表示します。
</iframe>

コードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

コードの 2 番目のブロックでは、ルートの起点と終点を表すピンを作成し、マップ上に追加しています。 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) の使用方法については、[マップにピンを追加する](map-add-pin.md)に関する記事を参照してください。

コードの 3 番目のブロックでは、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 関数を使い、ルートの起点と終点に基づいて、マップの境界ボックスを設定しています。

コードの 4 番目のブロックでは、[Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) に [XMLHttpRequest](https://xhr.spec.whatwg.org/) を送信しています。

コードの最後のブロックでは、受信した応答を解析しています。 応答が正常に返された場合は、各通過地点の緯度と経度の情報が収集されます。 各通過地点をその後続の通過地点に接続することで、線の配列が作成されます。 それらすべての線をマップ上に追加して、ルートがレンダリングされます。 手順については、「[線を追加する](./map-add-shape.md#addALine)」をご覧ください。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [マップ上にトラフィックを表示する](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [マップの連動 – マウス イベント](./map-events.md)
