---
title: Azure Maps を使った複数のルート | Microsoft Docs
description: Azure Maps を使用してさまざまな移動モードのルートを検索します
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 83ca46ecb8f0cce2ff8c749016eb3ad1ac7df7cf
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988971"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Azure Maps を使用してさまざまな移動モードのルートを検索します

このチュートリアルでは、Azure Maps のアカウントとルート サービスを使って、目的地までのルートを移動モードの優先順に検索する方法について説明します。 マップには 2 つのルートを表示します (乗用車用とトラック用)。これらのルートは、高さ、重量、積み荷の危険性を理由に制限される場合があります。 このチュートリアルで学習する内容は次のとおりです。

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
    HTML ヘッダーでは、CSS のリソースの場所と、Azure Maps ライブラリの JavaScript ファイルが埋め込まれています。 HTML 本体の *script* セグメントには、マップのインライン JavaScript コードが記述されます。
3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 **\<your account key\>** の文字列は、Maps アカウントからコピーした主キーに置き換えてください。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。 

   ![基本的なマップの表示](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>トラフィック フローを表示する

1. マップのフォーカスを指定しなかった場合は、全世界のビューが表示されます。 トラフィック データを表示できるようにするには、マップ上の中心点とズーム レベルを設定します。 次の JavaScript コードを使って、`new atlas.Map` を宣言するコードを置き換えます。 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    このコードでは、マップの中心点を設定し、ズーム レベルを宣言して、特定の領域が既定のフォーカスとして設定されるようにしています。 

1. マップにトラフィック フローの表示を追加します。

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
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

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** の呼び出しでは、起点と終点の座標に従ってマップ ウィンドウを調整しています。 API **map.addPins** は、点をビジュアル コンポーネントとしてマップ コントロールに追加します。

3. ファイルを保存し、ブラウザーを更新してマップ上にピンを表示します。 マップの中心点はロサンゼルスに設定しましたが、**map.setCameraBounds** によって、始点と終点を表示するビューに切り替わりました。 

   ![起点と終点が表示されたマップ](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>トラベルのモードの優先順にルートを表示する

このセクションでは、Maps のルート サービス API を使って、指定した起点から目的地までの複数のルートを、移動のモードに基づいて検索する方法について説明します。 ルーティング サービスの API では、現在のトラフィック状況を考慮したうえで、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳しくは、「[Get route directions (ルートの道順を取得する)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)」をご覧ください。


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
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
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
    
    また、このコード スニペットでは、アカウント キーを使用して、Maps のルート サービスに対するクエリを作成しています。 このクエリには、起点の座標と終点の座標に加えて、大型トラックのルートであることを示す、オプションのパラメーターが含まれています。

2. 次の JavaScript コードを追加して、乗用車のルートを要求し、結果を表示します。

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
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    このコード スニペットでは、別の [XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、受信した応答を解析するためのイベント ハンドラーを追加しています。 応答が正常に返された場合、返されたルートの座標の配列が作成され、マップの `carRouteLayerName` レイヤーに追加されます。 
    
    また、このコード スニペットでは、アカウント キーを使用して、Maps のルート サービスに対するクエリを作成しています。 このクエリには、始点の座標と終点の座標が含まれています。 追加のパラメーターは指定されていないので、ルート サービスの移動モードは既定値の *car* に設定されます。 

3. **MapTruckRoute.html** ファイルを保存し、ブラウザーを更新して結果を表示します。 Maps の API と正常に接続されると、次のようなマップが表示されます。 

    ![Azure Route Service を使用した優先順のルート](./media/tutorial-prioritized-routes/prioritized-routes.png)

    トラックのルートは青色の太い線で、乗用車のルートは紫色の細い線で表示されます。 乗用車のルートは、I-90 経由でワシントン湖を渡っています。この道路は住宅地の下のトンネルを通過するため、危険廃棄物の積み荷が制限されています。 トラックのルートは、積み荷の種類を USHazmatClass2 に指定したので、別の高速道路を使って適切にルート指定されています。 

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * マップ上にトラフィック フローを表示する
> * ルート クエリを作成し、移動モードを指定する
> * マップ上に複数のルートを表示する

Azure Maps の対象範囲と機能について詳しくは、「[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)」、および概念に関するその他の記事をご覧ください。 

他のコード例や対話型のコーディング エクスペリエンスについては、「[マップ コントロールの使用方法](how-to-use-map-control.md)」、および使用方法に関するその他のガイドをご覧ください。 
