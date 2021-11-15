---
title: Microsoft Azure Maps でラスター マップ上にカスタム データをレンダリングする
description: ラスター マップにプッシュピン、ラベル、幾何学図形を追加する方法について説明します。 この目的のために Azure Maps で静的画像サービスを使用する方法を確認してください。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9197090d405210db485d1ac8d471d899f58b8e43
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443644"
---
# <a name="render-custom-data-on-a-raster-map"></a>ラスター マップ上にカスタム データをレンダリングする

この記事では、[静的画像サービス](/rest/api/maps/render/getmapimage)を画像合成機能と共に使用する方法について説明します。 画像合成機能は、カスタム データを含む静的ラスター タイルの取得をサポートします。

カスタム データの例を次に示します。

- カスタム プッシュピン
- ラベル
- ジオメトリのオーバーレイ

> [!Tip]
> Web ページ上にシンプルなマップを表示するには、多くの場合、静的イメージ サービスを使用するよりも、Azure Maps Web SDK を使用する方がコスト効率に優れています。 Web SDK では、マップ タイルを使用します。ユーザーがマップをパンしたりズームしたりしない限り、多くの場合、マップの読み込みごとにトランザクションのごく一部のみが生成されます。 Azure Maps Web SDK には、パンとズームを無効にするオプションがあります。 また、Azure Maps Web SDK には、静的なマップの Web サービスよりも豊富なデータ視覚化オプションが用意されています。  

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

この記事では [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境も使用できます。

オーバーレイの格納とレンダリングには、Azure Maps [Data Service API](/rest/api/maps/data) を使用します。

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>ラベルとカスタム画像を使ってプッシュピンをレンダリングする

> [!Note]
> このセクションの手順には、Gen 1 または Gen 2 価格レベルの Azure Maps アカウントが必要です。
Azure Maps アカウント Gen 1 Standard S0 レベルでは、`pins` パラメーターのインスタンスが 1 つだけサポートされます。 カスタム画像を利用し、URL 要求で指定されている最大 5 つのプッシュピンをレンダリングできます。

### <a name="get-static-image-with-custom-pins-and-labels"></a>カスタムのピンとラベルを使用して静的画像を取得する

カスタムのピンとラベルを使用して静的画像を取得するには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Static Image*」など)。


4. **GET** HTTP メソッドを選択します。


5. 次の URL を入力します ({`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに置き換えます)。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```

6. **[Send]** を選択します。

7. サービスから次のような画像が返されます。

    :::image type="content" source="./media/how-to-render-custom-data/render-pins.png" alt-text="ラベル付きのカスタム プッシュピン。":::

## <a name="upload-pins-and-path-data"></a>ピンとパスのデータをアップロードする

> [!Note]
> このセクションの手順には、Gen 1 (S1) または Gen 2 価格レベルの Azure Maps アカウントが必要です。

このセクションでは、パスとピンのデータを Azure Map データ ストレージにアップロードします。

ピンとパスのデータをアップロードするには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*POST Path and Pin Data*」など)。

4. **POST** HTTP メソッドを選択します。

5. 次の URL を入力します ({`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに置き換えます)。

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

6. **[Body]** タブを選択します。

7. ドロップダウン リストで、 **[raw]** と **[JSON]** を選択します。

8. アップロードするデータとして、次の JSON データをコピーし、 **[Body]\(本文\)** ウィンドウに貼り付けます。
  
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

9. **[Send]** を選択します。

10. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

11. **Operation-Location** キーの値である `status URL` をコピーします。 この `status URL` は次のセクションでアップロード要求の状態を確認するために使用します。 `status URL` の形式は次のとおりです。

   ```HTTP
   https://us.atlas.microsoft.com/mapData/operations/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?api-version=2.0
   ```

>[!TIP]
>独自のパスとピンの位置情報を取得するには、[Data Upload API](/rest/api/maps/data-v2/upload-preview) を使用します。

### <a name="check-pins-and-path-data-upload-status"></a>ピンとパスのデータのアップロード状態を確認する

データ アップロードの状態を確認し、その一意の ID (`udid`) を取得するには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Data Upload Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[ピンとパスのデータをアップロードする](#upload-pins-and-path-data)」でコピーした `status URL` を入力します。 要求は次のような URL になります ({`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに置き換えます)。

   ```HTTP
     https://us.atlas.microsoft.com/mapData/operations/{statusUrl}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Resource-Location** キーの値である `resource location URL` をコピーします。 `resource location URL` には、Drawing パッケージ リソースの一意の識別子 (`udid`) が含まれています。

    :::image type="content" source="./media/how-to-render-custom-data/resource-location-url.png" alt-text="リソースの場所の URL をコピーします。":::

### <a name="render-uploaded-features-on-the-map"></a>アップロードされた地物をマップにレンダリングする

アップロードしたピンとパスのデータをマップにレンダリングするには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Data Upload Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を [[Render Service]](/rest/api/maps/render/get-map-image) に入力します ({`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに、`udid` はアップロードしたデータの `udid` に置き換えます)。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. サービスから次のような画像が返されます。

    :::image type="content" source="./media/how-to-render-custom-data/uploaded-path.png" alt-text="アップロードされたデータを静的マップ画像にレンダリングします。":::

## <a name="render-a-polygon-with-color-and-opacity"></a>色と不透明度が指定された多角形をレンダリングする

> [!Note]
> このセクションの手順には、Gen 1 (S1) または Gen 2 価格レベルの Azure Maps アカウントが必要です。

多角形の外観は、[path パラメーター](/rest/api/maps/render/getmapimage#uri-parameters)のスタイル修飾子を使用して変更できます。

色と不透明度が指定された多角形をレンダリングするには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[要求名]** を入力します (「*GET Polygon*」など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を [[Render Service]](/rest/api/maps/render/get-map-image) に入力します ( はプライマリ サブスクリプション キー、`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに置き換えます)。
  
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. サービスから次のような画像が返されます。

    :::image type="content" source="./media/how-to-render-custom-data/opaque-polygon.png" alt-text="不透明な多角形をレンダリングします。":::

## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>カスタム ラベルが付いた円とプッシュピンをレンダリングする

> [!Note]
> このセクションの手順には、Gen 1 (S1) または Gen 2 価格レベルの Azure Maps アカウントが必要です。

ピンの外観を変更するには、スタイル修飾子を追加します。 たとえば、プッシュピンとそのラベルを大きくまたは小さくするには、`sc` "スケール スタイル" 修飾子を使用します。 この修飾子は 0 より大きい値を受け取ります。 1 の値が標準のスケールです。 1 より大きい値にするとピンが大きくなり、1 より小さい値にすると小さくなります。 スタイル修飾子の詳細については、[静的画像サービス パスのパラメーター](/rest/api/maps/render/getmapimage#uri-parameters)に関するページを参照してください。

カスタム ラベルが付いた円とプッシュピンをレンダリングするには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[要求名]** を入力します (「*GET Polygon*」など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を [[Render Service]](/rest/api/maps/render/get-map-image) に入力します ( はプライマリ サブスクリプション キー、`Your-Azure-Maps-Primary-Subscription-key}` はプライマリ サブスクリプション キーに置き換えます)。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. **[Send]** を選択します。

7. サービスから次のような画像が返されます。

    :::image type="content" source="./media/how-to-render-custom-data/circle-custom-pins.png" alt-text="カスタム プッシュピンが付いた円をレンダリングします。":::

8. 次に、`co` スタイル修飾子を変更して、プッシュピンの色を変更します。 `pins` パラメーターの値 (`pins=default|la15+50|al0.66|lc003C62|co002D62|`) を見ると、現在の色が `#002D62` であることがわかります。 色を `#41d42a` に変更するには、`#002D62` を `#41d42a` に置き換えます。  `pins` パラメーターは `pins=default|la15+50|al0.66|lc003C62|co41D42A|` になりました。 要求は次の URL のようになります。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

9. **[Send]** を選択します。

10. サービスから次のような画像が返されます。

    :::image type="content" source="./media/how-to-render-custom-data/circle-updated-pins.png" alt-text="更新されたプッシュピンで円をレンダリングします。":::

同様に、他のスタイル修飾子を変更、追加、および削除することもできます。

## <a name="next-steps"></a>次の手順

* [Azure Maps Get Map Image API](/rest/api/maps/render/getmapimage) のドキュメントを確認します。
* Azure Maps Data Service の詳細については、[サービスのドキュメント](/rest/api/maps/data)を参照してください。
