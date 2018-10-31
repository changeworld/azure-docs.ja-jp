---
title: Azure Maps での検索 | Microsoft Docs
description: Azure Maps を使用して近くの目的地を検索する
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 10fb30b77cc3cd18cbb6b3def9682349474fba71
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645816"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Azure Maps を使用して近くの目的地を検索する

このチュートリアルでは、Azure Maps でアカウントを設定してから、Maps API を使って目的地を検索する方法を示します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Maps アカウントを作成する
> * Maps アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Maps 検索サービスを使って近くの目的地を検索する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com) にサインインします。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps でアカウントを作成する

次の手順で新しい Maps アカウントを作成します。

1. [Azure Portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** をクリックします。
2. *[Marketplace を検索]* ボックスに「**Maps**」と入力します。
3. *[結果]* から **[Maps]** を選択します。 マップの下に表示される **[作成]** ボタンをクリックします。
4. **[Azure Maps アカウントの作成]** ページで、次の値を入力します。
    * 新しいアカウントの "*名前*"。
    * このアカウントで使う "*サブスクリプション*"。
    * このアカウントの "*リソース グループ*" の名前。 *[新規作成]* を選んで新しく作成することも、*[既存のものを使用]* を選んで既存のリソース グループを使うこともできます。
    * "*リソース グループの場所*" を選びます。
    * *[ライセンス]* と *[プライバシーに関する声明]* の内容を読み、チェック ボックスをオンにして条件に同意します。
    * **[作成]** ボタンをクリックします。

    ![ポータルでの Azure Maps アカウントの作成](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>アカウントの主キーを取得する

Maps アカウントが正常に作成されたら、Maps API のクエリを実行できるキーを取得します。

1. ポータルで、Maps アカウントを開きます。
2. [設定] セクションで **[キー]** を選択します。
3. **[主キー]** をクリップボードにコピーします。 このチュートリアルで後ほど使用するためにローカルに保存します。

    ![ポータルで主キーを取得する](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>新しいマップの作成

マップ コントロール API は、Maps を Web アプリケーションに簡単に統合できる便利なクライアント ライブラリです。 ベア REST サービスの呼び出しの複雑さを隠ぺいし、スタイルの設定とカスタマイズが可能なコンポーネントにより生産性を高めます。 次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapSearch.html** にします。
2. 次の HTML コンポーネントをファイルに追加します。

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    HTML ヘッダーに、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれることに注意してください。 HTML ファイルの *body* に追加された *script* セグメントに注意してください。 このセグメントには、Azure Maps API にアクセスするためのインライン JavaScript コードが含まれます。

3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 **\<your account key\>** の文字列は、Maps アカウントからコピーした主キーに置き換えてください。

    ```JavaScript
    // Instantiate map to the div with id "map"
    atlas.setSubscriptionKey("<your account key>");
    var map = new atlas.Map("map");
    ```

    このセグメントは、Azure Maps アカウント キーに対するマップ コントロール API を開始します。 **atlas** は、API および関連するビジュアル コンポーネントを含む名前空間です。 **atlas.Map** は、ビジュアルと対話型 Web マップに対するコントロールを提供します。

4. 変更をファイルに保存し、ブラウザーで HTML ページを開きます。 これは、**atlas.map** を呼び出し、アカウント キーを使用することで作成できる最も基本的なマップです。

   ![マップの表示](./media/tutorial-search-location/basic-map.png)

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>検索機能の追加

ここでは、Maps Search API を使って、マップ上で目的地を検索する方法を示します。 これは、開発者向けに設計された、住所、目的地、その他の地理的な情報を検索するための RESTful API です。 Search サービスは、指定された住所に緯度と経度の情報を割り当てます。 後述する**サービス モジュール**は、Maps Search API を使用して場所を検索する場合に使用できます。

### <a name="service-module"></a>サービス モジュール

1. マップに新しいレイヤーを追加して検索結果を表示します。 マップを初期化するコードの後の script ブロックに次の Javascript コードを追加します。

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    ```

2. クライアント サービスをインスタンス化するには、マップを初期化するコードの後の script ブロックに次の Javascript コードを追加します。

    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. マップが読み込まれた後、マップ上のすべての関数を読み込む必要があります。 マップの eventListener ブロック内にすべての map 関数を配置することで確認できます。 次のコードの行を追加して [eventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) をマップに追加し、機能を追加する前にマップが完全に読み込まれるようにします。
    
    ```JavaScript
         map.events.add("load", function() {
         });
    ```

4. **マップを読み込むイベント内に**次のスクリプト ブロックを追加してクエリをビルドします。 Search Service の基本的な検索 API であるファジー検索サービスを使用します。 ファジー検索サービスは、アドレスと目的地 (POI) トークンの任意の組み合わせなど、ほとんどのファジー入力を処理します。 指定された半径内の近くのガソリン スタンドを検索します。 応答は GeoJSON 形式に解析され、ポイント フィーチャに変換され、ピンとしてマップに追加されます。 スクリプトの最後の部分では、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) プロパティを使用してマップのカメラ境界を追加します。

    ```JavaScript

            // Execute a POI search query then add pins to the map for each result once a response is received
            client.search.getSearchFuzzy("gasoline station", {
            lat: 47.6292,
            lon: -122.2337,
            radius: 100000
            }).then(response => {
       
            // Parse the response into GeoJSON 
            var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);

            // Create the point features that will be added to the map as pins
            var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => {
               var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat];
               return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                name: poiResult.properties.poi.name,
                address: poiResult.properties.address.freeformAddress,
                position: poiPosition[1] + ", " + poiPosition[0]
               });
            });

            // Add pins to the map for each POI
            map.addPins(searchPins, {
               name: searchLayerName,
               cluster: false, 
               icon: "pin-round-darkblue" 
            });

            // Set the camera bounds
            map.setCameraBounds({
               bounds: geojsonResponse.getGeoJsonResults().bbox,
               padding: 50
            });
    });
    ```
5. **MapSearch.html** ファイルを保存し、ブラウザーを更新します。 領域内のガソリン スタンドの位置が青いピンでマークされたシアトルを中心としたマップが表示されます。

   ![検索結果でのマップの表示](./media/tutorial-search-location/pins-map.png)

6. ブラウザーに次の HTTPRequest を入力すると、マップがレンダリングしている生のデータを確認することができます。 \<your account key\> を主キーで置き換えます。

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

この時点で、MapSearch ページに、あいまい検索クエリから返される目的地の場所を表示できます。 いくつかの対話型機能と場所の詳細情報を追加しましょう。

## <a name="add-interactive-data"></a>対話型のデータの追加

作成したマップでは、これまで検索結果の緯度/経度データだけを参照してきました。 しかし、Maps 検索サービスが返す生の JSON を参照すると、名前や番地など、各ガソリン スタンドに関する追加情報が含まれていることがわかります。 対話型のポップアップ ボックスで、そのデータをマップに組み込むことができます。

1. 次の行を *script* ブロックに追加して、Search Service によって返される目的地のポップアップを作成します。

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API **atlas.Popup** は、マップ上の目的の位置に固定された情報ウィンドウを提供します。 このコード スニペットは、ポップアップの内容と位置を設定します。 また、_マウス_がポップアップをロールオーバーするのを待機するイベント リスナーを `map` コントロールに追加します。

2. ファイルを保存し、ブラウザーを更新します。 いずれかの検索ピンをポイントすると、ブラウザーのマップに情報ポップアップが表示されます。

    ![Azure マップ コントロールと Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Maps でアカウントを作成する
> * アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Search Service を使って近くの目的地を検索する

以下から、このチュートリアルのコード サンプルにアクセスできます。

> [Azure Maps での場所の検索](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/search.html)

次のチュートリアルでは、2 つの場所間のルートを表示する方法を示します。

> [!div class="nextstepaction"]
> [行き先へのルートの決定](./tutorial-route-location.md)
