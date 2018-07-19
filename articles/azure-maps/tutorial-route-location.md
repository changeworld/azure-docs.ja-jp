---
title: Azure Maps を使ってルートを検索する | Microsoft Docs
description: Azure Maps を使って目的地へのルートを検索する
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 09828fade464c3b7b5f6eedaa16513e9eab49467
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989644"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Azure Maps を使って目的地へのルートを検索する

このチュートリアルでは、Azure Maps アカウントと Route Service SDK を使って、目的地までのルートを検索する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * 住所の座標を設定する
> * Route Service で目的地までの指示を照会する

## <a name="prerequisites"></a>前提条件

先に進む前に、前のチュートリアルの手順に従って [Azure Maps アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)してください。 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>新しいマップの作成 
次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。 

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
    HTML ヘッダーでは、CSS のリソースの場所と、Azure Maps ライブラリの JavaScript ファイルが埋め込まれています。 HTML ファイル本体の *script* セグメントには、Azure Maps API にアクセスするためのインライン JavaScript コードが記述されます。

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

   ![基本的なマップの表示](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>起点と終点の設定

このチュートリアルでは、Microsoft を起点として設定し、シアトルにあるガソリン スタンドを終点として設定します。 

1. **MapRoute.html** ファイルの同じ *script* ブロックに次の JavaScript コードを追加して、ルートの起点と終点を作成します。

    ```JavaScript
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
    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。 

2. 次の JavaScript コードを追加して、起点と終点のピンをマップに追加します。

    ```JavaScript
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
    **map.setCameraBounds** では、起点と終点の座標に従ってマップ ウィンドウを調整しています。 API **map.addPins** は、点をビジュアル コンポーネントとしてマップ コントロールに追加します。

7. **MapRoute.html** ファイルを保存し、ブラウザーを更新します。 マップの中心がシアトルに設定され、起点を示す丸い青色のピンと、終点を示す青色のピンが表示されます。

   ![起点と終点が表示されたマップ](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>道順の取得

ここでは、Maps のルート サービス API を使って、指定した起点から目的地までのルートを検索する方法について説明します。 ルート サービスの API では、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳しくは、「[Get route directions (ルートの道順を取得する)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)」をご覧ください。


1. まずは、マップ上に新しいレイヤーを追加して、ルートのパス (*ラインストリング*) を表示します。 *script* ブロックに、次の JavaScript コードを追加します。

    ```JavaScript
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

2. [XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、Maps ルート サービスによって送信された JSON 応答を解析するための、イベント ハンドラーを追加します。 このコードでは、ルートの線分に対する座標の配列を作成し、マップのラインストリング レイヤーにそれらの座標を追加しています。 

    ```JavaScript
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

3. 次のコードを追加してクエリを構築し、Maps ルート サービスに XMLHttpRequest を送信します。

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. **MapRoute.html** ファイルを保存し、Web ブラウザーを更新します。 Maps API と正常に接続されると、次のようなマップが表示されます。 

    ![Azure マップ コントロールと Route Service](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * 住所の座標を設定する
> * ルート サービスで目的地までの道順を照会する

次のチュートリアルでは、移動のモードや積み荷の種類など、制限を設定したルート クエリを作成し、同じマップ上に複数のルートを表示する方法について説明します。 

> [!div class="nextstepaction"]
> [異なる移動モードでルートを検索する](./tutorial-prioritized-routes.md)
