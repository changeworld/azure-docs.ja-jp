---
title: Azure Maps を使った複数のルート | Microsoft Docs
description: Azure Maps を使用してさまざまな移動モードのルートを検索します
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 340bf83f07b9e730cc43baccc60a39f5ba1f9942
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815309"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Azure Maps を使用してさまざまな移動モードのルートを検索します

このチュートリアルでは、Azure Maps のアカウントとルート サービスを使って、目的地までのルートを移動モードの優先順に検索する方法について説明します。 マップには 2 つのルートを表示します (乗用車用とトラック用)。これらのルートは、高さ、重量、積み荷の危険性を理由に制限される場合があります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * マップ上にトラフィック フローを表示する
> * ルート クエリを作成し、移動モードを指定する
> * マップ上に複数のルートを表示する

## <a name="prerequisites"></a>前提条件

先に進む前に、最初のチュートリアルの手順に従って [Azure Maps アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)してください。

## <a name="create-a-new-map"></a>新しいマップの作成

次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapTruckRoute.html** にします。
2. 次の HTML コンポーネントをファイルに追加します。

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML ヘッダーでは、CSS のリソースの場所と、Azure Maps ライブラリの JavaScript ファイルが埋め込まれています。 HTML 本体の *script* セグメントには、マップのインライン JavaScript コードが記述されます。
3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 **\<your account key\>** の文字列は、Maps アカウントからコピーした主キーに置き換えてください。 マップのフォーカスを指定しなかった場合は、全世界のビューが表示されます。 このコードでは、マップの中心点を設定し、ズーム レベルを宣言して、特定の領域が既定のフォーカスとして設定されるようにしています。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
         center: [-118.2437, 34.0522],
         zoom: 12
    });
    ```
    **atlas.Map** は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。 

   ![基本的なマップの表示](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>トラフィック フローを表示する

1. マップにトラフィック フローの表示を追加します。  **map.addEventListener** は、マップが完全に読み込まれた後、マップに追加されたすべてのマップ関数が確実に読み込まれるようにしています。

    ```JavaScript
    map.addEventListener("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    このコードで、`relative` へのトラフィック フローを設定します。これは、フリーフローを基準とした道路の速度です。 道路の速度を `absolute` に設定することもできますし、`relative-delay` に設定して、フリーフローとの差を示す相対的速度を表示することもできます。

2. **MapTruckRoute.html** ファイルを保存し、ブラウザーでページを更新します。 ロサンゼルスの道路の、現在のトラフィック データが表示されます。

   ![トラフィック マップの表示](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>起点と終点の設定

このチュートリアルでは、シアトルにある架空の会社 (Fabrikam) を起点とし、Microsoft のオフィスを終点に設定します。

1. 次の JavaScript コードを追加して、ルートの起点と終点のピンを作成します。

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。

2. 次の JavaScript コードを追加して、起点と終点をマップに追加します。

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });
    
    map.addEventListener("load", function() { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    **map.setCameraBounds** の呼び出しでは、起点と終点の座標に従ってマップ ウィンドウを調整しています。 **map.addEventListener** は、マップが完全に読み込まれた後、マップに追加されたすべてのマップ関数が確実に読み込まれるようにしています。 API **map.addPins** は、点をビジュアル コンポーネントとしてマップ コントロールに追加します。

3. ファイルを保存し、ブラウザーを更新してマップ上にピンを表示します。 マップの中心点はロサンゼルスに設定しましたが、**map.setCameraBounds** によって、始点と終点を表示するビューに切り替わりました。

   ![起点と終点が表示されたマップ](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>トラベルのモードの優先順にルートを表示する

このセクションでは、Maps のルート サービス API を使って、指定した起点から目的地までの複数のルートを、移動のモードに基づいて検索する方法について説明します。 ルーティング サービスの API では、現在のトラフィック状況を考慮したうえで、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳しくは、「[Get route directions (ルートの道順を取得する)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)」をご覧ください。  **マップを読み込む eventListener 内に**以下のコード ブロックをすべて追加し、マップが完全に読み込まれた後に、それらが読み込まれるようにする必要があります。

1. まずは、マップ上に新しいレイヤーを追加して、ルートのパス (*ラインストリング*) を表示します。 このチュートリアルでは、2 つの異なるルート (**car-route** と **truck-route**) を使って、それぞれに独自のスタイルを設定します。 *script* ブロックに、次の JavaScript コードを追加します。

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. *script* ブロックに次の JavaScript コードを追加して、トラックのルートを要求し、マップに結果を表示します。

    ```JavaScript
    // Instantiate the service client  
    var client = new atlas.service.Client(MapsAccountKey);

    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];

    // Execute the truck route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery, {
        travelMode: "truck",
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: "USHazmatClass2"
    }).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDirectionsResponse(response);

        // Get the first in the array of routes and add it to the map
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: truckRouteLayerName
        });
    });
    ```
    上に示したこのコード スニペットは、サービス クライアントをインスタンス化して、ルート クエリ文字列を構築します。 次に、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを通して Azure Maps ルーティング サービスへのクエリを実行し、[getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) を使用してその応答を解析して、GeoJSON 形式にします。 その後、返されたルートの座標の配列を作成し、それをマップの `truckRouteLayerName` レイヤーに追加します。

3. 次の JavaScript コードを追加して、乗用車のルートを要求し、結果を表示します。

    ```JavaScript
    // Execute the car route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new tlas.service.geojson
            .GeoJsonRouteDiraectionsResponse(response);

        // Get the first in the array of routes and add it to the map 
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: carRouteLayerName
        });
    });
    ```
    このコード スニペットでは、乗用車に対して、トラックと同じルートのクエリを使用します。 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを通して Azure Maps ルーティング サービスへのクエリを実行し、[getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) を使用してその応答を解析して、GeoJSON 形式にします。 その後、返されたルートの座標の配列を作成し、それをマップの `carRouteLayerName` レイヤーに追加します。

4. **MapTruckRoute.html** ファイルを保存し、ブラウザーを更新して結果を表示します。 Maps の API と正常に接続されると、次のようなマップが表示されます。

    ![Azure Route Service を使用した優先順のルート](./media/tutorial-prioritized-routes/prioritized-routes.png)

    トラックのルートは青色の太い線で、乗用車のルートは紫色の細い線で表示されます。 乗用車のルートは、I-90 経由でワシントン湖を渡っています。この道路は住宅地の下のトンネルを通過するため、危険廃棄物の積み荷が制限されています。 トラックのルートは、積み荷の種類を USHazmatClass2 に指定したので、別の高速道路を使って適切にルート指定されています。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * マップ上にトラフィック フローを表示する
> * ルート クエリを作成し、移動モードを指定する
> * マップ上に複数のルートを表示する

以下から、このチュートリアルのコード サンプルにアクセスできます。

> [Azure Maps を使った複数のルート](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/truckRoute.html)

Azure Maps の対象範囲と機能について詳しくは、以下を参照してください。

> [!div class="nextstepaction"]
> [ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)