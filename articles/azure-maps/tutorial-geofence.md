---
title: チュートリアル:Microsoft Azure Maps でジオフェンスを作成してデバイスを追跡する
description: ジオフェンスを設定する方法に関するチュートリアルです。 Azure Maps 空間サービスを使用して、ジオフェンスを基準にデバイスを追跡する方法を確認します
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: b3f98990a34ada3d832498a892d289323f485a28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455342"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>チュートリアル:Azure Maps を使用してジオフェンスを設定する

このチュートリアルでは、Azure Maps ジオフェンス サービスの作成と使用の基礎を確認します。

以下のシナリオについて考えてみます。

"*建設現場の管理者が、建設区域の境界を出入りする機材を追跡する必要があります。それらの境界を機材が出入りするたびに、メール通知が作業管理者に送信されます。* "

建設区域を出入りする機材の追跡をサポートするさまざまなサービスが Azure Maps には用意されています。 このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> * 監視対象の建設現場区域を定義する[ジオフェンシング GeoJSON データ](geofence-geojson.md)をアップロードする。 [Data Upload API](/rest/api/maps/data-v2/upload-preview) を使用して、ジオフェンスをポリゴン座標として自分の Azure Maps アカウントにアップロードします。
> * 機材がジオフェンス領域を出入りしたときにトリガーされ、メール通知を建設現場の作業管理者に送信する 2 つの[ロジック アプリ](../event-grid/handler-webhooks.md#logic-apps)を設定する。
> * [Azure Event Grid](../event-grid/overview.md) を使用して Azure Maps ジオフェンスの enter イベントと exit イベントをサブスクライブする。 ここでは、2 つのロジック アプリに定義された HTTP エンドポイントを呼び出す Webhook イベントのサブスクリプションを 2 つ設定します。 これらのロジック アプリから、ジオフェンスを出入りする機材に関する適切なメール通知が送信されます。
> * 機材がジオフェンス領域を出入りしたときに、[Search Geofence Get API](/rest/api/maps/spatial/getgeofence) を使用して通知を受信する。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)。
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

このチュートリアルでは、[Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を使用することもできます。

## <a name="upload-geofencing-geojson-data"></a>ジオフェンシング GeoJSON データをアップロードする

このチュートリアルでは、`FeatureCollection` を含んだジオフェンシング GeoJSON データをアップロードします。 `FeatureCollection` には、建設現場内の多角形領域を定義する 2 つのジオフェンスが含まれています。 1 つ目のジオフェンスには、時間の有効期限や制限はありません。 2 つ目は、照会できる期間が作業時間 (太平洋標準時の午前 9 時から午後 5 時) に限られ、2022 年 1 月 1 日を過ぎると無効となります。 GeoJSON 形式の詳細については、「[ジオフェンシング GeoJSON データ](geofence-geojson.md)」を参照してください。

>[!TIP]
>ジオフェンシング データはいつでも更新することができます。 詳細については、[Data Upload API](/rest/api/maps/data-v2/upload-preview) に関するページをご覧ください。

ジオフェンシング GeoJSON データをアップロードするには、次の手順を実行します。

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*POST GeoJSON Data Upload* など)。

4. **POST** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` をお使いのプライマリ サブスクリプション キーに置き換えます)。

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

    URL パス内の `geojson` パラメーターは、アップロードするデータの形式を表します。

6. **[Body]** タブを選択します。

7. ドロップダウン リストで、 **[raw]** と **[JSON]** を選択します。

8. 次の GeoJSON データをコピーして、 **[本文]** ウィンドウに貼り付けます。

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

9. **[Send]** を選択します。

10. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

11. **Operation-Location** キーの値である `status URL` をコピーします。 `status URL` を使用して、GeoJSON データのアップロード状態を確認します。

    ```http
    https://us.atlas.microsoft.com/mapData/operations/{operationId}?api-version=2.0
    ```

### <a name="check-the-geojson-data-upload-status"></a>GeoJSON データのアップロード状態を確認する

GeoJSON データの状態を確認し、その一意の ID (`udid`) を取得するには、次の手順を実行します。

1. **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[Request name]\(要求名\)** を入力します (「*GET Data Upload Status*」など)。

4. **GET** HTTP メソッドを選択します。

5. 「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」でコピーした `status URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` をお使いのプライマリ サブスクリプション キーに置き換えます)。

   ```HTTP
   https://us.atlas.microsoft.com/mapData/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. **[Send]** を選択します。

7. 応答ウィンドウで、 **[Headers]\(ヘッダー\)** タブを選択します。

8. **Resource-Location** キーの値である `resource location URL` をコピーします。 `resource location URL` には、アップロードされたデータの一意の識別子 (`udid`) が含まれています。 このチュートリアルの最後のセクションで Get Geofence API に対するクエリを実行できるように、`udid` を保存します。

    :::image type="content" source="./media/tutorial-geofence/resource-location-url.png" alt-text="リソースの場所の URL をコピーします。":::

### <a name="optional-retrieve-geojson-data-metadata"></a>(省略可能) GeoJSON データ メタデータを取得する

アップロードされたデータからメタデータを取得できます。 メタデータには、リソースの場所の URL、作成日、更新日、サイズ、アップロードの状態などの情報が含まれます。

コンテンツ メタデータを取得するには、次の手順を実行します。

1. **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*GET Data Upload Metadata* など)。

4. **GET** HTTP メソッドを選択します。

5. 「[GeoJSON データのアップロード状態を確認する](#check-the-geojson-data-upload-status)」でコピーした `resource Location URL` を入力します。 要求は次のような URL になります (`{Azure-Maps-Primary-Subscription-key}` をお使いのプライマリ サブスクリプション キーに置き換えます)。

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. 応答ウィンドウで、 **[本文]** タブを選択します。メタデータは、次の JSON フラグメントのようになるはずです。

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Azure Logic Apps でワークフローを作成する

次に、メール通知をトリガーする 2 つの[ロジック アプリ](../event-grid/handler-webhooks.md#logic-apps) エンドポイントを作成します。

ロジック アプリを作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal の左上隅にある **[リソースの作成]** を選択します。

3. **[Marketplace を検索]** ボックスに「**Logic App**」と入力します。

4. 結果から **[ロジック アプリ]** を選択します。 そのうえで **[Create]\(作成\)** を選択します。

5. **[Logic App]** ページで、次の値を入力します。
    * このロジック アプリに使用する "**サブスクリプション**"。
    * このロジック アプリの "**リソース グループ**" の名前。 **[新規作成]** を選んで新しく作成することも、 **[既存のものを使用]** を選んで既存のリソース グループを使うこともできます。
    * "**ロジック アプリ名**"。 この場合は、名前として `Equipment-Enter` を使用します。

    このチュートリアルの目的上、他の値はすべて、既定の設定のままにしてください。

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="ロジック アプリを作成する際のスクリーンショット。":::

6. **[確認および作成]** を選択します。 設定を確認し、 **[作成]** を選択します。

7. デプロイが正常に完了したら、 **[リソースに移動]** を選択します。

8. **[ロジック アプリ デザイナー]** で下にスクロールし、 **[一般的なトリガーで開始する]** セクションに移動します。 **[HTTP 要求の受信時]** を選択します。

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="ロジック アプリの HTTP トリガーを作成する際のスクリーンショット。":::

9. ロジック アプリ デザイナーの右上隅にある **[保存]** を選択します。 **HTTP POST の URL** が自動的に生成されます。 URL を保存します。 次のセクションでイベントのエンドポイントを作成する際に必要となります。

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="ロジック アプリの HTTP 要求の URL と JSON のスクリーンショット。":::

10. **[+ 新しいステップ]** を選択します。

11. 検索ボックスに「`outlook.com email`」と入力します。 **[アクション]** の一覧を下へスクロールし、 **[メールの送信 (V2)]** を選択します。
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="ロジック アプリ デザイナーを作成する際のスクリーンショット。":::

12. Outlook アカウントにサインインします。 アカウントへのアクセスをロジック アプリに許可するために、必ず **[はい]** を選択してください。 メール送信に関するフィールドに値を入力します。

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="ロジック アプリのメール送信作成ステップのスクリーンショット。":::

    >[!TIP]
    > `geometryId` や `deviceId` などの GeoJSON 応答データは、メール通知で取得できます。 Event Grid によって送信されたデータを読み取るように Logic Apps を構成できます。 イベント データを使用したり、イベント データをメール通知に渡したりするように Logic Apps を構成する方法については、「[チュートリアル: Event Grid および Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](../event-grid/publish-iot-hub-events-to-logic-apps.md)」を参照してください。

13. **[ロジック アプリ デザイナー]** の左上隅にある **[保存]** を選択します。

14. 建設現場から機材が出されたときに管理者に通知する 2 つ目のロジック アプリを作成するには、同じ手順を繰り返します。 このロジック アプリには `Equipment-Exit` という名前を付けます。

## <a name="create-azure-maps-events-subscriptions"></a>Azure Maps イベントのサブスクリプションを作成する

Azure Maps では、[3 種類のイベント](../event-grid/event-schema-azure-maps.md)をサポートしています。 このチュートリアルでは、次の 2 つのイベントに対するサブスクリプションを作成します。

* ジオフェンス開始イベント
* ジオフェンス終了イベント

ジオフェンス終了を作成し、イベント サブスクリプションに入るには、次の手順を実行します。

1. Azure Maps アカウントで、 **[サブスクリプション]** を選択します。

2. サブスクリプション名を選択します。

3. 設定メニューから、 **[イベント]** を選択します。

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Azure Maps アカウントのイベントに移動する際のスクリーンショット。":::

4. イベント ページで **[+ イベント サブスクリプション]** を選択します。

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Azure Maps イベントのサブスクリプションを作成する際のスクリーンショット。":::

5. **[イベント サブスクリプションの作成]** ページで、次の値を入力します。
    * イベント サブスクリプションの "**名前**"。
    * **[イベント スキーマ]** は *[イベント グリッド スキーマ]* にする必要があります。
    * このイベント サブスクリプションの "**システム トピック名**"。この場合は、`Contoso-Construction` となります。
    * **[イベントの種類のフィルター]** では、イベントの種類として `Geofence Entered` を選択します。
    * **[エンドポイントのタイプ]** では、`Web Hook` を選択します。
    * **[エンドポイント]** には、前のセクションで作成したロジック アプリの enter エンドポイントの HTTP POST の URL をコピーします。 保存し忘れていた場合は、ロジック アプリ デザイナーに戻って HTTP トリガーの手順からコピーしてください。

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps イベントのサブスクリプションの詳細のスクリーンショット。":::

6. **［作成］** を選択します

7. ジオフェンス終了イベントに対して同じ手順を繰り返します。 イベントの種類として必ず `Geofence Exited` を選択してください。

## <a name="use-spatial-geofence-get-api"></a>Spatial Geofence Get API を使用する

次に、[Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) を使用して、機材がジオフェンスを出入りしたときに電子メールによる通知を作業の管理者に送信します。

各機材には `deviceId` が指定されます。 このチュートリアルでは、一意の ID が `device_1` である 1 つの機材を追跡します。

次の図は、時間の経過に伴う機材の 5 つの位置を示したものです。ジオフェンス外部のどこかにある *Start* 位置が起点となります。 このチュートリアルの目的上、*Start* 位置は定義していません。その位置のデバイスに対してクエリを実行することはないからです。

ジオフェンスの最初の出入りを示す機材位置を指定して [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) に対するクエリを実行すると、Event Grid によって適切なロジック アプリ エンドポイントが呼び出されて、メール通知が作業管理者に送信されます。

以降の各セクションでは、5 つの異なる機材位置座標を使用して API 要求を実行します。

![Azure Maps のジオフェンス マップの図](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>機材位置 1 (47.638237,-122.132483)

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*Location 1* など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は、次の URL のようになるはずです (`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに置き換え、`{udid}` は「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えます)。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. **[Send]** を選択します。

7. 応答は次のような GeoJSON フラグメントになるはずです。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

前の GeoJSON 応答では、メイン サイト ジオフェンスからの距離が負数になっていますが、これは機材がメイン ジオフェンス内にあることを意味します。 サブサイト ジオフェンスからの距離は正数になっていることから、サブサイト ジオフェンスの外にあることがわかります。 このとき初めてこのデバイスがメイン サイト ジオフェンス内に配置されたため、`isEventPublished` パラメーターが `true` に設定されます。 作業管理者は、機材がジオフェンスに入ったというメール通知を受信します。

### <a name="location-2-4763800-122132531"></a>位置 2 (47.63800,-122.132531)

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*Location 2* など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は、次の URL のようになるはずです (`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに置き換え、`{udid}` は「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えます)。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. **[Send]** を選択します。

7. 応答は次のような GeoJSON フラグメントになるはずです。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

前の GeoJSON 応答では、機材はメイン サイト ジオフェンス内に留まっています。サブサイト ジオフェンスには入っていません。 その結果、`isEventPublished` パラメーターが `false` に設定され、作業管理者にメール通知は送信されません。

### <a name="location-3-4763810783315048-12213336020708084"></a>位置 3 (47.63810783315048,-122.13336020708084)

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*Location 3* など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は、次の URL のようになるはずです (`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに置き換え、`{udid}` は「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えます)。

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

6. **[Send]** を選択します。

7. 応答は次のような GeoJSON フラグメントになるはずです。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

上の GeoJSON 応答では、機材はメイン サイト ジオフェンス内に留まっていますが、サブサイト ジオフェンスに入っています。 その結果、`isEventPublished` パラメーターが `true` に設定されます。 作業管理者には、機材がジオフェンスに入ったことを示すメール通知が送信されます。

>[!NOTE]
>作業時間を過ぎてから機材がサブサイトに移動した場合は、イベントが発行されず、作業管理者に通知は送信されません。  

### <a name="location-4-47637988-1221338344"></a>場所 4 (47.637988,-122.1338344)

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*Location 4* など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は、次の URL のようになるはずです (`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに置き換え、`{udid}` は「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えます)。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. **[Send]** を選択します。

7. 応答は次のような GeoJSON フラグメントになるはずです。

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

上の GeoJSON 応答では、機材はメイン サイト ジオフェンス内には留まっていますが、サブサイト ジオフェンスからは出ています。 ただし、`userTime` 値は、ジオフェンス データに定義されている `expiredTime` を過ぎていることに注意してください。 その結果、`isEventPublished` パラメーターが `false` に設定され、作業管理者にメール通知は送信されません。

### <a name="location-5-4763799--122134505"></a>位置 5 (47.63799、-122.134505)

1. Postman アプリ内で **[新規]** を選択します。

2. **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。

3. 要求の **[要求名]** を入力します (*Location 5* など)。

4. **GET** HTTP メソッドを選択します。

5. 次の URL を入力します。 要求は、次の URL のようになるはずです (`{Azure-Maps-Primary-Subscription-key}` は実際のプライマリ サブスクリプション キーに置き換え、`{udid}` は「[ジオフェンシング GeoJSON データをアップロードする](#upload-geofencing-geojson-data)」セクションで保存した `udid` に置き換えます)。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. **[Send]** を選択します。

7. 応答は次のような GeoJSON フラグメントになるはずです。

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

前の GeoJSON 応答では、機材はメイン サイト ジオフェンスから出ています。 その結果、`isEventPublished` パラメーターが `true` に設定され、作業管理者には、機材がジオフェンスから出たことを示すメール通知が送信されます。

また、[Event Grid と Logic Apps を使用して電子メール通知を送信](../event-grid/publish-iot-hub-events-to-logic-apps.md)して、[Event Grid でサポートされているイベント ハンドラー](../event-grid/event-handlers.md)を確認することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Logic Apps における各種コンテンツの扱い](../logic-apps/logic-apps-content-type.md)
