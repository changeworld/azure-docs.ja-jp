---
title: "Azure Location Based Services でルートを検索する | Microsoft Docs"
description: "Azure Location Based Services を使用して目的地までのルートを検索します"
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
ms.openlocfilehash: 0f784e8ecd8fc94c12df1a819055718e06547b6b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Azure Location Based Services を使用して目的地までのルートを検索する

このチュートリアルは、Azure Location Based Services アカウントと Route Service SDK を使って、目的地までのルートを探す方法を示します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 住所の座標を取得する
> * Route Service で目的地までの指示を照会する

## <a name="prerequisites"></a>前提条件

先に進む前に、[Azure Location Based Services アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)してください。 また、「[Azure Location Based Services を使用して近くの目的地を検索する](./tutorial-search-location.md)」チュートリアルで説明されているような、マップ コントロールと Search Service API を使用する方法もわかります。


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>住所の座標を取得する

次の手順のようにして、Location Based Services のマップ コントロール API で埋め込まれる静的な HTML ページを作成します。 

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapRoute.html** にします。 
2. 次の HTML コンポーネントをファイルに追加します。

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    CSS のリソースの場所および Azure Location Based Services ライブラリの JavaScript ファイルが HTML ヘッダーにどのように埋め込まれているのかに注意してください。 また、HTML ファイルの本体の *script* セグメントにも注意してください。このセグメントには、Azure Location Based Services の API にアクセスするためのインライン JavaScript コードが含まれます。

3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 プレースホルダー *<insert-key>* は、ご自分の Location Based Services アカウントの主キーに置き換えます。

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    **atlas.Map** は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. 次の JavaScript コードを、*script* ブロックに追加します。 これは、ルートを表示するためのマップ コントロールに、*linestrings* のレイヤーを追加します。

    ```HTML
            // Initialize the linestring layer for routes on the map
            var routeLinesLayerName = "routes";
            map.addLinestrings([], {
                name: routeLinesLayerName,
                color: "#2272B9",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    ```

5. 次の JavaScript コードを追加して、ルートの起点と終点を作成します。

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Microsoft",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Contoso Oil & Gas",
                icon: "pin-blue"
            });
    ```
    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。 終点は、前のチュートリアル「[Azure Location Based Services を使用して近くの目的地を検索する](./tutorial-search-location.md)」で検索した "*ガソリン スタンド*" の 1 つに対する緯度/経度の組み合わせです。

6. 次の JavaScript コードを追加して、起点と終点のピンをマップに追加します。

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    API **map.setCameraBounds** は、起点と終点の座標に従ってマップ ウィンドウを調整します。 API **map.addPins** は、点をビジュアル コンポーネントとしてマップ コントロールに追加します。

7. **MapRoute.html** ファイルをお使いのコンピューターに保存します。 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Route Service で目的地までの指示を照会する

ここでは、Azure Location Based Services の Route Service API を使って、指定した起点から目的地までのルートを検索する方法を説明します。 Route Service には、リアルタイムの交通状況を考慮して、2 つの場所の間の最も速いルート、最も短いルート、最も経済的なルートを計画するための API が用意されています。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 

1. 前のセクションで作成した **MapRoute.html** ファイルを開き、Route Service を示すための次の JavaScript コードを *script* ブロックに追加します。

    ```HTML
            // Perform a request to the route service and draw the resulting route on the map
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(xhttp.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
                }
            };
    ```
    このコード スニペットは、[XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、受信した応答を解析するためのイベント ハンドラーを追加します。 正常な応答の場合は、返された最初のルートの線分セグメントに対する座標の配列を作成します。 その後、このルートの座標のセットを、マップの *linestrings* レイヤーに追加します。

2. 次のコードを *script* ブロックに追加します。このコードは、XMLHttpRequest を Azure Location Based Services の Route Service に送信します。

    ```HTML
            var url = "https://atlas.microsoft.com/route/directions/json?";
            url += "&api-version=1.0";
            url += "&subscription-key=" + subscriptionKey;
            url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    
            xhttp.open("GET", url, true);
            xhttp.send();
    ```
    上の要求では必要なパラメーターが示されており、お使いのアカウントのサブスクリプション キーと、特定の順序になった起点と終点の座標です。 

3. **MapRoute.html** ファイルをローカルに保存した後、任意の Web ブラウザーで開いて結果を確認します。 Location Based Services の API と正しく接続された場合、次のようなマップが表示されます。 

    ![Azure マップ コントロールと Route Service](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 住所の座標を取得する
> * Route Service で目的地までの指示を照会する

チュートリアル「[Find routes for different modes of travel using Azure Location Based Services](./tutorial-prioritized-routes.md)」(Azure Location Based Services を使用して異なる移動モードのルートを検索する) に進み、Azure Location Based Services を使用し、移動モードに基づいて、目的地までのルートの優先順位を設定する方法を学習してください。 
