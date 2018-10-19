---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascript マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729240"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。

目的地の検索には、2 つ方法があります。 1 つは、サービス モジュールを使用して検索要求を行う方法です。 もう 1 つは、[XMLHttpRequest](https://xhr.spec.whatwg.org/) から [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に検索要求を行う方法です。 以下で両方の方法を説明します。

## <a name="make-a-search-request-via-service-module"></a>サービス モジュールを使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

コードの最初のコード ブロックでは、マップ オブジェクトが作成され、クライアント サービスが初期化されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、[Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を使用して POI が検索されます。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 あいまい検索サービスからの応答は、[getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) メソッドを使用して GeoJSON 形式に解析されます。 地図上の目的地を示すピンがマップに追加されます。

コードの最後のブロックでは、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) プロパティを使用してマップのカメラ領域が調整されます。

## <a name="make-a-search-request-via-xmlhttprequest"></a>XMLHttpRequest を使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>」Pen を表示します。
</iframe>

コードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

コードの 2 番目のブロックでは、マップに検索結果レイヤーが追加されます。 検索結果レイヤーでは、マップ上に検索結果がピン留めして表示されます。 ピンは、[addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) を使用して追加されます。

コードの 3 番目のブロックでは、[Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に [XMLHttpRequest](https://xhr.spec.whatwg.org/) を送信し、POI を検索します。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。

コードの最後のブロックでは、応答が解析され、マップの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) が使用されて、結果のピンをレンダリングするためのマップのカメラ領域が調整されます。

## <a name="next-steps"></a>次の手順

あいまい検索の詳細については、**こちら**を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [座標から情報を取得する](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)
