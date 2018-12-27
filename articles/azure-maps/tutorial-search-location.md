---
title: Azure Maps での検索 | Microsoft Docs
description: Azure Maps を使用して近くの目的地を検索する
author: walsehgal
ms.author: v-musehg
ms.date: 12/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c11274b8f9d6c3b8c934201b4ec4acd6842d7c0b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435363"
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
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

         function GetMap(){
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

3. 次の JavaScript コードを、HTML ファイルの `GetMap` 関数に追加します。 **\<Your Azure Maps Key\>** という文字列を、Maps アカウントからコピーした主キーに置き換えてください。

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   このセグメントは、Azure Maps アカウント キーに対するマップ コントロール API を開始します。 **atlas** は、API および関連するビジュアル コンポーネントを含む名前空間です。 **atlas.Map** は、ビジュアルと対話型 Web マップに対するコントロールを提供します。 

4. 変更をファイルに保存し、ブラウザーで HTML ページを開きます。 これは、**atlas.map** を呼び出し、アカウント キーを使用することで作成できる最も基本的なマップです。

   ![マップの表示](./media/tutorial-search-location/basic-map.png)

5. `GetMap` 関数で、マップを初期化した後、次の JavaScript コードを追加します。 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

      //Create a popup but leave it closed so we can update it and display it later.
      popup = new atlas.Popup();

      //Add a mouse over event to the result layer and display a popup when this event fires.
      map.events.add('mouseover', resultLayer, showPopup);
   });
   ```

   読み込みイベントがマップに追加されます。これは、マップ リソースが完全に読み込まれたときに発生します。 マップの読み込みイベントのハンドラーで、結果データを格納するためのデータ ソースを作成します。 記号レイヤーを作成し、データ ソースにアタッチします。 データ ソースに格納された結果データのレンダリング方法は、このレイヤーで指定します。ここでは、濃い青色の円形のピン アイコンが、結果座標上に中央揃えで、結果データと共に表示されます。また、このピン アイコン上には他のアイコンを重ねて表示することができます。 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>検索機能の追加

ここでは、Maps Search API を使って、マップ上で目的地を検索する方法を示します。 これは、開発者向けに設計された、住所、目的地、その他の地理的な情報を検索するための RESTful API です。 Search サービスは、指定された住所に緯度と経度の情報を割り当てます。 後述する**サービス モジュール**は、Maps Search API を使用して場所を検索する場合に使用できます。

### <a name="service-module"></a>サービス モジュール

1. マップの読み込みイベントのハンドラーに次の JavaScript コードを追加して、クライアント サービスをインスタンス化します。

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. 続けて次のスクリプト ブロックを追加して検索クエリを作成します。 Search Service の基本的な検索 API であるファジー検索サービスを使用します。 ファジー検索サービスは、住所、場所、目的地 (POI) など、ほとんどのファジー入力を処理します。 このコードは、指定された半径内の近くのガソリン スタンドを検索します。 その後、応答は GeoJSON 形式に解析され、データ ソースに追加されます。これにより、データが記号レイヤーを介してマップ上に自動でレンダリングされます。 スクリプトの最後の部分では、結果の境界ボックスとマップの [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用して、マップのカメラ ビューを設定しています。 境界ボックスは座標に基づいて計算されるため、記号アイコンの画像サイズ (ピクセル) を相殺するためにパディングを追加しています。 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. **MapSearch.html** ファイルを保存し、ブラウザーを更新します。 領域内のガソリン スタンドの位置が青いピンでマークされたシアトルを中心としたマップが表示されます。

   ![検索結果でのマップの表示](./media/tutorial-search-location/pins-map.png)

4. ブラウザーに次の HTTPRequest を入力すると、マップがレンダリングしている生のデータを確認することができます。 \<Your Azure Maps Key\> は、実際の主キーに置き換えてください。

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

この時点で、MapSearch ページに、あいまい検索クエリから返される目的地の場所を表示できます。 いくつかの対話型機能と場所の詳細情報を追加しましょう。

## <a name="add-interactive-data"></a>対話型のデータの追加

作成したマップでは、これまで検索結果の経度/緯度データだけを参照してきました。 しかし、Maps 検索サービスが返す生の JSON を参照すると、名前や番地など、各ガソリン スタンドに関する追加情報が含まれていることがわかります。 対話型のポップアップ ボックスで、そのデータをマップに組み込むことができます。

1. マップの読み込みイベントのハンドラーに次のコード行を追加します。ファジー検索サービスを照会するコードの後に追加してください。 これにより、Popup のインスタンスが作成され、mouseover イベントが記号レイヤーに追加されます。

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    API **atlas.Popup** は、マップ上の目的の位置に固定された情報ウィンドウを提供します。 
      
2. *script* タグの `GetMap` 関数に続けて、次のコードを追加します。これは、マウス オーバーした結果の情報をポップアップに表示するものです。 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.
        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
   }
   ```

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

> [Azure Maps での場所の検索](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[こちらでライブ サンプルをご覧いただけます](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

次のチュートリアルでは、2 つの場所間のルートを表示する方法を示します。

> [!div class="nextstepaction"]
> [行き先へのルートの決定](./tutorial-route-location.md)
