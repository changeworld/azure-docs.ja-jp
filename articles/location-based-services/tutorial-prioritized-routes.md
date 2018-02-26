---
title: "Azure Location Based Services を使用した複数のルート | Microsoft Docs"
description: "Azure Location Based Services を使用してさまざまなトラベルのモードのルートを見つけます"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: ac57996e7dced5ef0f0a993889bfc13b1d064167
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Azure Location Based Services を使用してさまざまなトラベルのモードのルートを見つけます

このチュートリアルは、Azure Location Based Services アカウントと Route Service SDK を使って、トラベルのモードの優先順に目的地までのルートを探す方法を示します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Route Service クエリを構成する
> * トラベルのモードの優先順にルートを表示する

## <a name="prerequisites"></a>前提条件

先に進む前に、[Azure Location Based Services アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのキーを取得](./tutorial-search-location.md#getkey)してください。 マップ コントロールと Search Service API の使用方法については、チュートリアル「[Search nearby point of interest using Azure Location Based Services](./tutorial-search-location.md)」(Azure Location Based Services を使用して近い目的地を検索する) を参照してください。また、Route Service API の基本的な使用方法については、チュートリアル「[Route to a point of interest using Azure Location Based Services](./tutorial-route-location.md)」(Azure Location Based Services を使用して目的地までのルートを検索する) を参照してください。


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Route Service クエリを構成する

次の手順のようにして、Location Based Services のマップ コントロール API で埋め込まれる静的な HTML ページを作成します。 

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapTruckRoute.html** にします。 
2. 次の HTML コンポーネントをファイルに追加します。

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
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
    CSS のリソースの場所および Azure Location Based Services ライブラリの JavaScript ファイルは HTML ヘッダーにどのように埋め込まれています。 また、HTML ファイルの本文に追加される *script* セグメントにも注意してください。このセグメントには、Azure Map Control API にアクセスするためのインライン JavaScript コードが含まれています。
3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 プレースホルダー *<insert-key>* は、ご自分の Location Based Services アカウントの主キーに置き換えます。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. 次の JavaScript コードを *script* ブロックに追加して、トラフィック フローの表示をマップに追加します。

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    このコードで、`relative` へのトラフィック フローを設定します。これは、フリーフローを基準とした道路の速度です。 道路の速度を `absolute` に設定するか、フリーフローとの差を示す相対的速度を表示する `relative-delay` に設定することもできます。 

5. 次の JavaScript コードを追加して、ルートの起点と終点のピンを作成します。

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

6. 次の JavaScript コードを追加して、*linestrings* のレイヤーをマップ コントロールに追加し、_car_ や _truck_ など、移動手段のモードに基づいてルートを表示します。

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

7. 次の JavaScript コードを追加して、起点と終点をマップに追加します。

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

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    API **map.setCameraBounds** は、起点と終点の座標に従ってマップ ウィンドウを調整します。 API **map.addPins** は、点をビジュアル コンポーネントとしてマップ コントロールに追加します。

8. **MapTruckRoute.html** ファイルをお使いのコンピューターに保存します。 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>トラベルのモードの優先順にルートを表示する

ここでは、Azure Location Based Services の Route Service API を使って、移動手段のモードに基づいて、指定した起点から目的地までの複数のルートを検索する方法を説明します。 Route Service には、リアルタイムの交通状況を考慮して、2 つの場所の間の最も速いルート、最も短いルート、最も経済的なルートを計画するための API が用意されています。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 

1. 前のセクションで作成した **MapTruckRoute.html** ファイルを開き、次の JavaScript コードを *script* ブロックに追加して、Route Service を使用してトラックのルートを取得します。

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    このコード スニペットは、[XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、受信した応答を解析するためのイベント ハンドラーを追加します。 成功応答の場合、返されたルートの座標の配列が作成され、マップの `truckRouteLayerName` レイヤーが追加されます。 
    
    また、このコード スニペットで、Route Service に対してクエリが送信され、アカウントのキーに指定された起点と終点のルートが取得されます。 次の省略可能なパラメーターは、大型トラックのルートを示すために使用します。パラメーター `travelMode=truck` で、トラベルのモードを *truck* と指定します。 サポートされる他のトラベルのモードは、*taxi*、*bus*、*van*、*motorcycle*、既定の *car* です。
        - パラメーター `vehicleWidth`、`vehicleHeight`、`vehicleLength` で、車両の寸法 (メートル) を指定します。これらのパラメーターは、トラベルのモードが *truck* の場合にのみ考慮されます。
        - `vehicleLoadType` は、積み荷を一部の道路で危険であり制限されるものと分類します。 現在、このパラメーターも *truck* モードの場合にのみ考慮されます。

2. 次の JavaScript コードを追加して、Route Service を使用して car のルートを取得します。

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    このコード スニペットで、別の [XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、受信した応答を解析するためのイベント ハンドラーを追加します。 成功応答の場合、返されたルートの座標の配列が作成され、マップの `carRouteLayerName` レイヤーが追加されます。 
    
    また、このコード スニペットで、Route Service に対してクエリが送信され、アカウントのキーに指定された起点と終点のルートが取得されます。 他のパラメーターは使用されていないので、既定のトラベルのモードである *car* のルートが返されます。 

3. **MapTruckRoute.html** ファイルをローカルに保存してから、好みの Web ブラウザーで開き、結果を確認します。 Location Based Services の API と正常に接続できると、次のようなマップが表示されます。 

    ![Azure Route Service を使用した優先順のルート](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    truck のルートは青色ですが、car のルートは紫色です。

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Route Service クエリを構成する
> * トラベルのモードの優先順にルートを表示する

**概念**と**方法**に関する記事に進み、Azure Location Based Services SDK について詳しく学習します。 
