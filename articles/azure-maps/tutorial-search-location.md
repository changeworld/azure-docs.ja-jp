---
title: Azure Maps での検索 | Microsoft Docs
description: Azure Maps を使用して近くの目的地を検索する
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ffc4b7625a6c43f8e2801313c61f14c785a3ec5f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988876"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Azure Maps を使用して近くの目的地を検索する

このチュートリアルでは、Azure Maps でアカウントを設定してから、Maps API を使って目的地を検索する方法を示します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Maps アカウントを作成する
> * Maps アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Maps 検索サービスを使って近くの目的地を検索する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
[Azure Portal](https://portal.azure.com) にログインします。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps でアカウントを作成する

次の手順で新しい Maps アカウントを作成します。

1. [Azure Portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** をクリックします。
2. *[Marketplace を検索]* ボックスに「**Maps**」と入力します。
3. *[結果]* から **[Maps]** を選択します。 マップの下に表示される **[作成]** ボタンをクリックします。 
4. **[Azure Maps アカウントの作成]** ページで、次の値を入力します。
    - 新しいアカウントの "*名前*"。 
    - このアカウントで使う "*サブスクリプション*"。
    - このアカウントの "*リソース グループ*" の名前。 *[新規作成]* を選んで新しく作成することも、*[既存のものを使用]* を選んで既存のリソース グループを使うこともできます。
    - "*リソース グループの場所*" を選びます。
    - *[ライセンス]* と *[プライバシーに関する声明]* の内容を読み、チェック ボックスをオンにして条件に同意します。 
    - **[作成]** ボタンをクリックします。
   
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
    HTML ヘッダーに、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれることに注意してください。 HTML ファイルの *body* に追加された *script* セグメントに注意してください。 このセグメントには、Azure Maps API にアクセスするためのインライン JavaScript コードが含まれます。
 
3. 次の JavaScript コードを、HTML ファイルの *script* ブロックに追加します。 **\<your account key\>** の文字列は、Maps アカウントからコピーした主キーに置き換えてください。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    このセグメントは、Azure Maps アカウント キーに対するマップ コントロール API を開始します。 **atlas** は、API および関連するビジュアル コンポーネントを含む名前空間です。 **atlas.Map** は、ビジュアルと対話型 Web マップに対するコントロールを提供します。 
    
4. 変更をファイルに保存し、ブラウザーで HTML ページを開きます。 これは、**atlas.map** を呼び出し、アカウント キーを提供することで作成できる最も基本的なマップです。 

   ![マップの表示](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>検索機能の追加

ここでは、Maps Search API を使って、マップ上で目的地を検索する方法を示します。 これは、開発者向けに設計された、住所、目的地、その他の地理的な情報を検索するための RESTful API です。 Search サービスは、指定された住所に緯度と経度の情報を割り当てます。 

1. マップに新しいレイヤーを追加して検索結果を表示します。 マップを初期化するコードの後の *script* ブロックに次の Javascript コードを追加します。 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. [XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成し、Maps 検索サービスによって送信される JSON 応答を解析するイベント ハンドラーを追加します。 このコード スニペットは、`searchPins` 変数で返される各場所の住所、名前、緯度、経度情報を収集するイベント ハンドラーを構築します。 最後に、この場所のコレクションをピンとして `map` コントロールに追加します。 

    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```

3. 次のコードを *script* ブロックに追加してクエリを構築し、XMLHttpRequest を Maps 検索サービスに送信します。

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    このスニペットは、**あいまい検索**と呼ばれる、Search Service の基本的な検索 API を使います。 住所または目的地 (POI) トークンの任意の組み合わせを含む入力のほとんどのあいまいさを処理します。 特定の緯度と経度の座標の指定した半径内で、近くにある**ガソリン スタンド**を検索します。 前にサンプル ファイルで提供されたお使いのアカウントの主キーを使って、Maps への呼び出しを行います。 結果は、見つかった場所の緯度/経度のペアとして返されます。 
    
4. **MapSearch.html** ファイルを保存し、ブラウザーを更新します。 シアトルを中心としたマップが表示され、領域内のガソリン スタンドの位置が青いピンでマークされます。 

   ![検索結果でのマップの表示](./media/tutorial-search-location/pins-map.png)

5. ファイルをビルドする XMLHTTPRequest を取得し、ブラウザーに入力することで、マップがレンダリングしている生データを確認できます。 \<your account key\> を主キーで置き換えます。 

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
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API **atlas.Popup** は、マップ上の目的の位置に固定された情報ウィンドウを提供します。 このコード スニペットは、ポップアップの内容と位置を設定するだけでなく、"_マウス_" がポップアップをロールオーバーするのを待機するイベント リスナーを `map` コントロールに追加します。 

4. ファイルを保存し、ブラウザーを更新します。 いずれかの検索ピンをポイントすると、ブラウザーのマップに情報ポップアップが表示されます。 

    ![Azure マップ コントロールと Search Service](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Maps でアカウントを作成する
> * アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Search Service を使って近くの目的地を検索する

次のチュートリアルでは、2 つの場所間のルートを表示する方法を示します。 

> [!div class="nextstepaction"]
> [行き先へのルートの決定](./tutorial-route-location.md)
