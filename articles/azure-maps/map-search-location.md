---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascript マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c68b4bdffde5f987fe07d50d76fa83e7bdfa5235
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755050"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。

目的地の検索には、2 つ方法があります。 1 つは、サービス モジュールを使用して検索要求を行う方法です。 もう 1 つは、[XMLHttpRequest](https://xhr.spec.whatwg.org/) から [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に検索要求を行う方法です。 以下で両方の方法を説明します。

## <a name="make-a-search-request-via-service-module"></a>サービス モジュールを使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

上記のコードで最初のコード ブロックでは、マップ オブジェクトが作成され、クライアント サービスが初期化されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、[サービス モジュール](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1)内で [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) メソッドが使用されています。 そのため、[あいまい検索 REST API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を介して自由形式のテキスト検索を実行して関心のあるポイントを検索できます。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 あいまい検索サービスからの応答は、[getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest) メソッドを使用して GeoJSON 形式に解析されます。 

2 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用してデータ ソース オブジェクトが作成され、検索結果がそこに追加されます。 [シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  次にシンボル レイヤーが作成され、データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

最後のコード ブロックでは、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用してマップのカメラ境界が調整されます。

マップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内で検索要求、データ ソース、シンボル レイヤー、およびカメラ境界が作成および設定されます。


## <a name="make-a-search-request-via-xmlhttprequest"></a>XMLHttpRequest を使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、[Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に [XMLHttpRequest](https://xhr.spec.whatwg.org/) が送信され、POI が検索されます。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 

3 つ目のコード ブロックでは、検索応答が解析され、境界計算する配列に結果が格納されます。 次に検索結果が返されます。

4 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用してデータ ソース オブジェクトが作成され、検索結果がそこに追加されます。 [シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 次にシンボル レイヤーが作成され、データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

最後のコード ブロックでは、結果の配列を使用して [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) オブジェクトが作成され、Map の [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) を使用してマップのカメラ境界が調整されます。 次に結果のピンがレンダリングされます。

マップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内で検索要求、データ ソース、シンボル レイヤー、およびカメラ境界が設定されます。

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
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)
