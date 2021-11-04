---
title: Azure Maps Elevation Service を使用して標高データを要求する
description: Azure Maps Elevation Service を使用して標高データを要求する方法について説明します。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
ms.custom: mvc
ms.openlocfilehash: a9920d3b512bda0b2710ad9c0c6daf5c5801602a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432170"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Azure Maps Elevation Service を使用して標高データを要求する

Azure Maps [Elevation Service](/rest/api/maps/elevation) では、地表の任意の場所の標高データのクエリを実行するための API が提供されています。 サンプリングされた標高データは、パス沿い、定義された境界ボックス内、または特定の座標を指定して要求できます。 また、[Render V2 - Get Map Tile API](/rest/api/maps/renderv2) を使用して、タイル形式で標高データを取得することもできます。 タイルは、GeoTIFF ラスター形式で提供されます。 この記事では、Azure Maps Elevation Service と Get Map Tile API を使用して標高データを要求する方法について説明します。 標高データは、GeoJSON 形式と GeoTiff 形式の両方で要求できます。

## <a name="prerequisites"></a>前提条件

1. [Gen 1 (S1) または Gen 2 の価格レベルで Azure Maps アカウントを作成](quick-demo-map-app.md#create-an-azure-maps-account)します。
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

この記事では [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境も使用できます。

## <a name="request-elevation-data-in-raster-tile-format"></a>ラスター タイル形式で標高データを要求する

ラスター タイル形式で標高データを要求するには、[Render V2 - Get Map Tile API](/rest/api/maps/renderv2) を使用します。 タイルが見つかると、API によってタイルが GeoTIFF として返されます。 それ以外の場合、API によって 0 が返されます。 すべてのラスター DEM タイルで、ジオイド (海水面) 地球モードが使用されます。 この例では、エベレスト山の標高データを要求します 。

>[!TIP]
>世界地図の特定の領域にあるタイルを取得するには、適切なズーム レベルの正しいタイルを見つけます。 WorldDEM には世界中の陸地がすべて含まれていますが、海は含まれていないことにも注意してください。  詳細については、「[Zoom levels and tile grid](zoom-levels-and-tile-grid.md)」(ズーム レベルとタイル グリッド) を参照してください。

Postman アプリを使用してラスター タイル形式の標高データを要求するには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]\(新規作成\)** ウィンドウで **[HTTP Request]\(HTTP 要求\)** を選択します。

3. 要求の **[要求名]** を入力します。

4. **[ビルダー]** タブで **GET** HTTP メソッドを選択し、次の URL を入力してラスター タイルを要求します。

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

    >[!Important]
    >この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

5. **[送信]** ボタンを選択します。

    標高データを含むラスター タイルが GeoTIFF 形式で返されます。 ラスター タイルの生データ内の各ピクセルは、`float` 型です。 各ピクセルの値は、メートル単位での標高を表します。

## <a name="request-elevation-data-in-geojson-format"></a>GeoJSON 形式で標高データを要求する

GeoJSON 形式で標高データを要求するには、Elevation Service API を使用します。 このセクションでは、次の各 API について説明します。

* [Get Data for Points](/rest/api/maps/elevation/getdataforpoints)
* [Post Data for Points](/rest/api/maps/elevation/postdataforpoints)
* [Get Data for Polyline](/rest/api/maps/elevation/getdataforpolyline)
* [Post Data for Polyline](/rest/api/maps/elevation/postdataforpolyline)
* [Get Data for Bounding Box](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> データを返すことができない場合、すべての API で **0** が返されます。

### <a name="request-elevation-data-for-points"></a>ポイントの標高データを要求する

この例では、[Get Data for Points API](/rest/api/maps/elevation/getdataforpoints) を使用して、 エベレスト山とチャムラン山の標高データを要求します。 その後、[Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) 使用して、同じ 2 つの地点を使用して標高データを要求します。 URL の緯度と経度は、10 進法の WGS84 (世界測地系) 数が想定されています。

 >[!IMPORTANT]
 >URL の文字数の制限が 2,048 であるため、URL GET 要求では、100 を超える座標をパイプラインで区切られた文字列として渡すことはできません。 パイプラインで区切られた文字列として 100 を超える座標を渡す場合は、Post Data for Points API を使用します。

要求を作成するには:

1. Postman アプリで、もう一度 **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します。

4. **[ビルダー]** タブで **GET** HTTP メソッドを選択し、次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をご自分のプライマリ サブスクリプション キーに置き換えてください)。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

5. **[送信]** ボタンを選択します。  次の JSON 応答が返されます。

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

6. 次に、[Post Data for Points API](/rest/api/maps/elevation/postdataforpoints) を呼び出して、同じ 2 つの地点の標高データを取得します。 **[ビルダー]** タブで **POST** HTTP メソッドを選択し、次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をご自分のプライマリ サブスクリプション キーに置き換えてください)。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

7. **POST** 要求の **Headers** フィールドで、`Content-Type` を `application/json` に設定します。 

8. **Body** フィールドに、次の座標点の情報を指定します。

    ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

9. **[Send]** を選択します。

### <a name="request-elevation-data-samples-along-a-polyline"></a>ポリラインに沿った標高データ サンプルの要求

この例では、[Get Data for Polyline](/rest/api/maps/elevation/getdataforpolyline) を使用して、 エベレスト山とチャムラン山の座標を結ぶ直線に沿う、等間隔での 5 つの標高データを要求します。 どちらの座標も、経度と緯度の形式で定義する必要があります。 `samples` パラメーターに値を指定しない場合、サンプルの数は既定で 10 に設定されます。 サンプルの最大数は 2,000 です。

次に、Get Data for Polyline API を使用して、パスに沿って 3 つの等間隔の標高データのサンプルを要求します。 ここでは、3 つの経度と緯度の座標ペアを渡すことで、サンプルの正確な位置を定義します。

最後に、[Post Data For Polyline API](/rest/api/maps/elevation/postdataforpolyline) を使用して、同じ 3 つの等間隔のサンプルの標高データを要求します。

URL の緯度と経度は、10 進法の WGS84 (世界測地系) 数が想定されています。

 >[!IMPORTANT]
 >URL の文字数の制限が 2,048 であるため、URL GET 要求では、100 を超える座標をパイプラインで区切られた文字列として渡すことはできません。 パイプラインで区切られた文字列として 100 を超える座標を渡す場合は、Post Data for Points API を使用します。

要求を作成するには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. **[要求名]** を入力します。

4. **[ビルダー]** タブで **GET** HTTP メソッドを選択し、次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をご自分のプライマリ サブスクリプション キーに置き換えてください)。

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

5. **[送信]** ボタンを選択します。  次の JSON 応答が返されます。

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

6. 次に、エベレスト山、チャムラン山、ジャンヌ山の座標間のパスに沿った標高データのサンプルを 3 つ要求します。 **[パラメーター]** フィールドに、`lines` クエリ キーの値として次の座標配列をコピーします。

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

7. `samples` クエリ キーの値を `3` に変更します。  次の画像は、新しい値を示しています。

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="3 つの標高データ サンプルを取得します。":::

8. **[Send]** を選択します。 次の JSON 応答が返されます。

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

9. 次に、[Post Data For Polyline API](/rest/api/maps/elevation/postdataforpolyline) を呼び出して、同じ 3 つの地点の標高データを取得します。  **[ビルダー]** タブで **POST** HTTP メソッドを選択し、次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をご自分のプライマリ サブスクリプション キーに置き換えてください)。

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&samples=5
    ```

10. **POST** 要求の **Headers** フィールドで、`Content-Type` を `application/json` に設定します。 

11. **Body** フィールドに、次の座標点の情報を指定します。

    ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

12. **[Send]** を選択します。

### <a name="request-elevation-data-by-bounding-box"></a>境界ボックスによる標高データの要求

次に、[Get Data for Bounding Box](/rest/api/maps/elevation/getdataforboundingbox) を使用して、 ワシントン州レーニア山近くの標高データを要求します。 標高データは、境界ボックス内の等間隔の位置から返されます。 境界領域は 2 つの緯度と経度の座標のセット (南緯度, 西経度 | 北緯度, 東経度) によって定義され、行と列に分割されます。 境界ボックスの端は、2 つの行と 2 つの列で構成されます。 行と列の交点で作成されたグリッドの頂点の標高が返されます。 1 つの要求で最大 2,000 の標高を返すことができます。

この例では、行 = 3、列 = 6 を指定します。 応答では、18 の標高値が返されます。 次の図では、標高の値は南西隅から順に並べられ、西から東、南から北へと続きます。  標高ポイントは、返された順に番号が振られます。

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="NE および SE の角の境界ボックスの座標。":::

要求を作成するには:

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. **[要求名]** を入力します。

4. **[ビルダー]** タブで **GET** HTTP メソッドを選択し、次の URL を入力します (`{Azure-Maps-Primary-Subscription-key}` をご自分のプライマリ サブスクリプション キーに置き換えてください)。

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

5. **[Send]** を選択します。  応答では、グリッドの各頂点に 1 つずつ、18 の標高データ サンプルが返されます。

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>サンプル: Azure Maps Control で Elevation Service API を使用する

### <a name="get-elevation-data-by-coordinate-position"></a>座標位置を使用して標高データを取得する

次のサンプル Web ページで、マップ コントロールを使用して、座標点の標高データを表示する方法について説明します。 ユーザーがマーカーをドラッグすると、マップのポップアップ ウィンドウに標高データが表示されます。

<br/>

<iframe height="500" scrolling="no" title="位置の標高を取得する" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>位置の標高を取得する</a>」Pen を表示します。
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>境界ボックスを使用して標高データを取得する

次のサンプル Web ページで、マップ コントロールを使用して、境界ボックス内の標高データを表示する方法について説明します。 ユーザーは、左上隅にある `square` アイコンを選択してマップ上の任意の場所に四角を描画することで境界ボックスを定義します。 その後、マップ コントロールによって、右上隅にあるキーに指定されている色に従って、標高データがレンダリングされます。

<br/>

<iframe height="500" scrolling="no" title="境界ボックスごとの標高" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>境界ボックスごとの標高</a>」を参照してください。
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>ポリライン パスを使用して標高データを取得する

次のサンプル Web ページで、マップ コントロールを使用して、パスに沿って標高データを表示する方法について説明します。 ユーザーは、左上隅にある `Polyline` アイコンを選択し、マップ上にポリラインを描画することでパスを定義します。 その後、マップ コントロールによって、右上隅にあるキーに指定されている色で、標高データがレンダリングされます。

<br/>

<iframe height="500" scrolling="no" title="標高パスのグラデーション" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>標高パスのグラデーション</a>」を参照してください。
</iframe>


## <a name="next-steps"></a>次のステップ

Azure Maps Elevation API の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Elevation - 緯度と経度の座標データを取得する](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevation - 境界ボックスのデータを取得する](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevation - ポリラインのデータを取得する](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2 – Get Map Tile](/rest/api/maps/renderv2)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps REST API シリーズ](/rest/api/maps/)
