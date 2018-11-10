---
title: Azure Maps を使った複数のルート | Microsoft Docs
description: Azure Maps を使用してさまざまな移動モードのルートを検索します
author: walsehgal
ms.author: v-musehg
ms.date: 10/29/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 67b68489f2e06b9149f842f293a769fa7f688be0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412705"
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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    HTML ヘッダーに、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれることに注意してください。 ページ本体の `onload` イベントに注目してください。ページの本体が読み込まれると、このイベントによって `GetMap` 関数が呼び出されます。 この関数には、Azure Maps API にアクセスするためのインライン JavaScript コードが含まれます。

3. `GetMap` 関数に、次の JavaScript コードを追加します。 **\<Your Azure Maps Key\>** という文字列を、Maps アカウントからコピーした主キーに置き換えてください。

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    **atlas.Map** は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。

   ![基本的なマップの表示](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>トラフィック フローを表示する

1. マップにトラフィック フローの表示を追加します。 `map.events.add` は、マップが完全に読み込まれた後、マップに追加されたすべてのマップ関数が確実に読み込まれるようにしています。

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     読み込みイベントがマップに追加されます。これは、マップ リソースが完全に読み込まれたときに発生します。 マップの読み込みイベントのハンドラーで、マップのトラフィック フロー設定を `relative` (フリーフローを基準とした道路の速度) に設定します。 道路の速度を `absolute` に設定することもできますし、`relative-delay` に設定して、フリーフローとの差を示す相対的速度を表示することもできます。

2. **MapTruckRoute.html** ファイルを保存し、ブラウザーでページを更新します。 ロサンゼルスの道路の、現在のトラフィック データが表示されます。

   ![トラフィック マップの表示](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>ルートのレンダリング方法を定義する

このチュートリアルでは、2 つのルートが計算されてマップ上にレンダリングされます。 1 つは乗用車が通行可能な道路を使ったルートで、もう 1 つはトラックが通行可能な道路を使ったルートです。 レンダリングの際、ルートの起点と終点を示す記号アイコンを表示し、ルートの経路ごとに異なる色で線を表示します。

1. GetMap 関数で、マップの初期化の後に、次の JavaScript コードを追加します。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    読み込みイベントがマップに追加されます。これは、マップ リソースが完全に読み込まれたときに発生します。 マップの読み込みイベントのハンドラーで、ルートの線および起点と終点を格納するためのデータ ソースを作成します。 線レイヤーを作成してデータ ソースにアタッチすることで、ルートの線のレンダリング方法を定義します。 式を使って、線の幅と色をルートの線の特徴に関するプロパティから取得します。 このレイヤーで GeoJSON LineString データだけがレンダリングされるように、フィルターを追加しています。 このレイヤーをマップに追加する際に、`'labels'` という値の第 2 パラメーターを渡します。これにより、このレイヤーをマップ ラベルの下にレンダリングするよう指定します。 この結果、ルートの線で道路のラベルが覆い隠されないようになります。 記号レイヤーを作成し、データ ソースにアタッチします。 起点と終点のレンダリング方法は、このレイヤーで指定します。ここでは、各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式を追加しています。 
    
2. このチュートリアルでは、シアトルにある架空の会社 (Fabrikam) を起点とし、Microsoft のオフィスを終点に設定します。 マップの読み込みイベントのハンドラーに、次のコードを追加します。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。 それぞれの点に、`title` プロパティと `icon` プロパティを追加します。

3. 続けて、次の JavaScript コードを追加して、起点と終点のピンをマップに追加します。

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    起点と終点をデータ ソースに追加します。 起点と終点の境界ボックスは、`atlas.data.BoundingBox.fromData` 関数を使用して計算されます。 この境界ボックスと `map.setCamera` 関数を使用して、起点と終点の上にマップのカメラ ビューを設定します。 記号アイコンの画像サイズ (ピクセル) を相殺するためにパディングを追加しています。

4. ファイルを保存し、ブラウザーを更新してマップ上にピンを表示します。 マップの中心がシアトルに設定され、起点を示す丸い青色のピンと、終点を示す青色のピンが表示されます。

   ![起点と終点が表示されたマップ](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>トラベルのモードの優先順にルートを表示する

このセクションでは、Maps のルート サービス API を使って、指定した起点から目的地までの複数のルートを、移動のモードに基づいて検索する方法について説明します。 ルーティング サービスの API では、現在のトラフィック状況を考慮したうえで、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳細については、[ルートの道順の取得](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)に関するページを参照してください。 **マップを読み込む eventListener 内に**以下のコード ブロックをすべて追加し、マップが完全に読み込まれた後に、それらが読み込まれるようにする必要があります。

1. マップの読み込みイベントのハンドラーに次の JavaScript コードを追加することで、サービス クライアントをインスタンス化します。

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. ルート クエリ文字列を構築するために、次のコード ブロックを追加します。

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. 次の JavaScript コードを追加して、USHazmatClass2 区分の貨物を運搬するトラックのルートを要求し、結果を表示します。

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    上記のコード スニペットは、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを通じて Azure Maps ルーティング サービスへのクエリを実行し、[getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) を使用してその応答を解析し、GeoJSON 形式にします。 その後、返されたルートの座標の配列を作成し、それをデータ ソースに追加します。ただし、それがデータ ソース内の他のどの線よりも先にレンダリングされるよう、インデックス 0 も追加します。 トラックのルート計算は、えてして乗用車のルート計算よりも時間がかかるものであり、トラックのルートの線が乗用車のルートよりも遅れてデータ ソースに追加された場合、その上にレンダリングされてしまうためです。 トラックのルートの線には、2 つのプロパティを追加します。ストローク カラー (繊細な青色) とストロークの幅 (9 ピクセル) です。 

4. 次の JavaScript コードを追加して、乗用車のルートを要求し、結果を表示します。

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    このコード スニペットでは、乗用車に対して、トラックと同じルートのクエリを使用します。 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを通して Azure Maps ルーティング サービスへのクエリを実行し、[getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) を使用してその応答を解析して、GeoJSON 形式にします。 その後、返されたルートの座標の配列を作成し、それをデータ ソースに追加します。 乗用車のルートの線には、2 つのプロパティを追加します。ストローク カラー (紫色) とストロークの幅 (5 ピクセル) です。 

5. **MapTruckRoute.html** ファイルを保存し、ブラウザーを更新して結果を表示します。 Maps の API と正常に接続されると、次のようなマップが表示されます。

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

> [Azure Maps を使った複数のルート](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[こちらでライブ サンプルをご覧いただけます](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Azure Maps の対象範囲と機能について詳しくは、以下を参照してください。

> [!div class="nextstepaction"]
> [ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)
