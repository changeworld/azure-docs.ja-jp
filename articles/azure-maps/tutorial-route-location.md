---
title: Azure Maps を使ってルートを検索する | Microsoft Docs
description: Azure Maps を使って目的地へのルートを検索する
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a3807dc792c2e56c3e7c1b74f7d3e8f73ac0f4b0
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705091"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Azure Maps を使って目的地へのルートを検索する

このチュートリアルでは、Azure Maps アカウントと Route Service SDK を使って、目的地までのルートを検索する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

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
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    `atlas.Map` は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。

   ![基本的なマップの表示](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>ルートのレンダリング方法を定義する

このチュートリアルでは、ルートの起点と終点の記号アイコンおよびルート経路の線を使用して、単純なルートをレンダリングします。

1. GetMap 関数で、マップの初期化の後に、次の JavaScript コードを追加します。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    読み込みイベントがマップに追加されます。これは、マップ リソースが完全に読み込まれたときに発生します。 マップの読み込みイベントのハンドラーで、ルートの線および起点と終点を格納するためのデータ ソースを作成します。 線レイヤーを作成してデータ ソースにアタッチすることで、ルートの線のレンダリング方法を定義します。 ルートの線は、青色でレンダリングされます。線幅は 5 ピクセルで、線の接合箇所と線端には丸みを与えています。 このレイヤーのレンダリング対象を GeoJSON LineString データに限定するために、フィルターを追加しています。 このレイヤーをマップに追加する際に、`'labels'` という値の第 2 パラメーターを渡します。これにより、このレイヤーをマップ ラベルの下にレンダリングするよう指定します。 この結果、ルートの線で道路のラベルが覆い隠されないようになります。 記号レイヤーを作成し、データ ソースにアタッチします。 起点と終点のレンダリング方法は、このレイヤーで指定します。ここでは、各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式を追加しています。 
    
2. このチュートリアルでは、Microsoft を起点として設定し、シアトルにあるガソリン スタンドを終点として設定します。 マップの読み込みイベントのハンドラーに、次のコードを追加します。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON ポイント オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。 それぞれの点に、`title` プロパティと `icon` プロパティを追加します。
    
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
    
    起点と終点をデータ ソースに追加します。 起点と終点の境界ボックスは、`atlas.data.BoundingBox.fromData` 関数を使用して計算されます。 この境界ボックスと **map.setCamera** 関数を使用して、起点と終点の上にマップのカメラ ビューを設定します。 記号アイコンの画像サイズ (ピクセル) を相殺するためにパディングを追加しています。

3. **MapRoute.html** ファイルを保存し、ブラウザーを更新します。 マップの中心がシアトルに設定され、起点を示す丸い青色のピンと、終点を示す青色のピンが表示されます。

   ![起点と終点が表示されたマップ](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>道順の取得

ここでは、Maps のルート サービス API を使って、指定した起点から目的地までのルートを検索する方法について説明します。 ルート サービスの API では、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳しくは、「[Get route directions (ルートの道順を取得する)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)」をご覧ください。 **マップを読み込む eventListener 内に**以下の機能をすべて追加し、マップが完全に読み込まれた後に、それらが読み込まれるようにする必要があります。

1. マップの読み込みイベントのハンドラーに次の JavaScript コードを追加することで、サービス クライアントをインスタンス化します。

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. ルートを取得するには、次のコード ブロックをスクリプトに追加します。 これにより、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) メソッドを通じて Azure Maps ルーティング サービスに対してクエリが実行され、[getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) を使用してその応答が GeoJSON 形式に解析されます。 その後、応答に含まれるルートの線をデータ ソースに追加すれば、マップ上に自動的にレンダリングされます。

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. **MapRoute.html** ファイルを保存し、Web ブラウザーを更新します。 Maps API と正常に接続されると、次のようなマップが表示されます。

    ![Azure マップ コントロールと Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * 住所の座標を設定する
> * ルート サービスで目的地までの道順を照会する

以下から、このチュートリアルのコード サンプルにアクセスできます。

> [Azure Maps を使ってルートを検索する](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[こちらでライブ サンプルをご覧いただけます](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

次のチュートリアルでは、移動のモードや積み荷の種類など、制限を設定したルート クエリを作成し、同じマップ上に複数のルートを表示する方法について説明します。

> [!div class="nextstepaction"]
> [異なる移動モードでルートを検索する](./tutorial-prioritized-routes.md)
