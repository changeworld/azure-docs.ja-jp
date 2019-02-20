---
title: Azure Maps 内のラスター マップ上にカスタム データをレンダリングする方法 | Microsoft Docs
description: Azure Maps 内のラスター マップ上にカスタム データをレンダリングします。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119160"
---
# <a name="render-custom-data-on-raster-map"></a>ラスター マップ上にカスタム データをレンダリングする

この記事では、[静的イメージ サービス](https://docs.microsoft.com/rest/api/maps/render/getmapimage)とイメージ コンポジション機能を使用して、ラスター マップの上にオーバーレイを作成する方法について説明します。 画像コンポジションには、カスタムのプッシュピン、ラベル、ジオメトリのオーバーレイなどの追加データを使ってラスター タイルを元に戻す機能があります。 カスタムのプッシュピン、ラベル、ジオメトリをレンダリングには、Postman アプリケーションを使用します。 Postman アプリを開き、[New]\(新規\)、[Create New]\(新規作成\) の順にクリックし、保存先のコレクションまたはフォルダーを選択して名前を付け、[Save]\(保存\) をクリックします。

オーバーレイの格納とレンダリングには、Azure Maps [Data Service API](https://docs.microsoft.com/rest/api/maps/data) を使用します。 


## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

このガイドの手順を実行する前に、[アカウントとキーの管理](how-to-manage-account-keys.md)に関するページを参照して、S1 価格レベルのアカウント サブスクリプションを作成、管理する必要があります。

## <a name="render-pushpins-with-labels-and-custom-image"></a>ラベルとカスタム画像を使ってプッシュピンをレンダリングする

> [!Note]
> この例では、価格レベルが S0 または S1 の Azure Maps アカウントが必要です。 

Azure Maps アカウントの S0 SKU は 1 インスタンスの `pins` パラメーターのみをサポートしているため、ユーザーは、カスタム画像を使って url 要求に指定した最大 5 つのプッシュピンをレンダリングできます。

ラベルとカスタムの画像を使ってプッシュ ピンをレンダリングするには、次の手順を実行します。

1. Postman アプリを開き、[new]\(新規\)、[Create new]\(新規作成\) の順にクリックして、[Request]\(要求\) を選択します。 プッシュピンのレンダリングに対する要求名を入力して、保存先のコレクションまたはフォルダーを選択し、[Save]\(保存\) をクリックします。
    
    ![Postman を使用してジオフェンスをアップロードする](./media/tutorial-geofence/postman-new.png)

2. [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、GET 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    受け取る応答の画像を次に示します。

    ![ラベルを使ってカスタムのプッシュピンをレンダリングする](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps データ ストレージからデータを取得する

> [!Note]
> この例では、価格レベル S1 の Azure Maps アカウントが必要です。

パスとピンの位置情報は [Data Upload API](https://docs.microsoft.com/rest/api/maps/mapdata/upload) からも取得できます。 パスとピンのデータをアップロードするには、次の手順を実行します。

1. Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、POST 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. [Params]\(パラメーター\) をクリックして、POST 要求の URL に使用する次のキーと値のペアを入力します。 subscription-key の値は、実際の Azure Maps のサブスクリプション キーに置き換えてください。
    
    ![Postman のキーと値のペアから成るパラメーター](./media/how-to-render-custom-data/postman-key-vals.png)

3. **[Body]\(本文\)** をクリックして未加工入力形式を選択し、入力形式として JSON をドロップダウン リストから選択します。 アップロードするデータとして次の JSON を入力します。
    
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

4. [send]\(送信\) をクリックして、応答ヘッダーを確認します。 今後使用するためにデータをダウンロードしたりデータにアクセスしたりするための URI は、location ヘッダーに格納されます。 また、アップロードされたデータの一意の `udId` もそこに格納されます。   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. マップ上にフィーチャーをレンダリングするには、Data Upload API から受け取った `udid` の値を使用します。 これを行うには、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、GET 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 応答の画像は次のようになります。

    ![アップロードされたデータをレンダリングする](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>色と不透明度が指定された多角形をレンダリングする

> [!Note]
> この例では、価格レベル S1 の Azure Maps アカウントが必要です。

多角形の外観は、[path パラメーター](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters)のスタイル修飾子を使用して変更できます。

1. Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、GET 要求を行う次の URL を入力して、色と不透明度を指定した多角形をレンダリングします。
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

応答の画像は次のようになります。

![不透明な多角形をレンダリングする](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>円を使う多角形とカスタム ラベルを使うプッシュ ピンをレンダリングする

> [!Note]
> この例では、価格レベル S1 の Azure Maps アカウントが必要です。

'sc' スケール スタイル修飾子を使用して、プッシュピンとそのラベルを大きくまたは小さくすることができます。 これは 0 より大きい値です。 1 の値が標準のスケールです。 1 より大きい値にするとピンが大きくなり、1 より小さい値にすると小さくなります。 スタイル修飾子の詳細については、[静的イメージ サービス パスのパラメーター](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)に関するページを参照してください。

円を使う多角形とカスタム ラベルを使うプッシュ ピンをレンダリングするには、次の手順を実行します。

1. Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択し、GET 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

応答の画像は次のようになります。

![カスタムのピンを使う円をレンダリングする](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>次の手順

* [Azure Maps Get Map Image API](https://docs.microsoft.com/rest/api/maps/search) のドキュメントを確認します。
* Azure Maps Data Service 機能の詳細については、[サービスのドキュメント](https://docs.microsoft.com/rest/api/maps/data)を参照してください。