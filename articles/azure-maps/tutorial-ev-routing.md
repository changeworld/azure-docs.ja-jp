---
title: Azure Notebooks を使用した電気自動車のルート案内 (Python) | Microsoft Docs
description: Azure Maps のルート案内 API シリーズと Azure Notebooks を使用した電気自動車のルート案内。
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 30751bebd397b378924453987462c9e2b3b55ebf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803896"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Azure Notebooks を使用した電気自動車のルート案内 (Python)

Azure Maps は、Azure とネイティブに統合されている地理空間サービス API のポートフォリオです。開発者、企業、ISV はこれらを利用して、位置情報を認識するアプリや IoT、モビリティ、ロジスティックス、資産追跡といった各種ソリューションを構築できます。 Azure Maps REST API シリーズを Python や R などの言語で呼び出して、地理空間データ解析や機械学習のシナリオを実現できます。 Azure Maps には、ユーザーがさまざまな条件 (車種や到達可能領域など) に基づいて複数のデータ ポイント間のルートを計算できる、堅牢な[ルート案内 API]([https://docs.microsoft.com/rest/api/maps/route) セットが用意されています。 このチュートリアルでは、バッテリ残量が低下している電気自動車のドライバーに対して、走行時間的に最も近い充電スタンドを案内するシナリオについて説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * クラウドの [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) 上で Jupyter ノートブックを作成して実行する
> * Python で Azure Maps REST API シリーズを呼び出す
> * 電気自動車の消費モデルに基づいて到達可能範囲を調べる。
> * 到達可能範囲 (つまり等時線) 内にある電気自動車充電スタンドを検索する。
> * 到達可能範囲の境界線と充電スタンドをマップ上にレンダリングする。
> * 時間に基づいて最も近い電気自動車充電スタンドまでのルートを特定し、視覚化する。


## <a name="prerequisites"></a>前提条件 

このチュートリアルの手順を完了するには、まず Azure Maps アカウントを作成して、主キー (サブスクリプション キー) を取得する必要があります。 [アカウントの管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)に関するページの手順に従って、S1 価格レベルで Azure Maps アカウントのサブスクリプションを作成します。さらに、[主キーの取得](./tutorial-search-location.md#getkey)に関するページの手順に従って、お使いのアカウントのプライマリ サブスクリプション キーを取得します。

## <a name="create-an-azure-notebook"></a>Azure ノートブックを作成する

このチュートリアルに沿って作業を進めるには、Azure Notebook プロジェクトを作成して、Jupyter ノートブック ファイルをダウンロードし、実行する必要があります。 ノートブック ファイルには、本チュートリアルのシナリオを実装する Python コードが含まれています。 次の手順に従って、Azure Notebook プロジェクトを作成し、Jupyter ノートブック ドキュメントをそれにアップロードします。

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします 詳細については、[クイックスタート](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)に関するページを参照してください。
2. パブリック プロファイル ページで、ページ上部にある **[マイ プロジェクト]** を選択します。

    ![マイ プロジェクト](./media/tutorial-ev-routing/myproject.png)

3. **[マイ プロジェクト]** ページで **[新しいプロジェクト]** を選択します。
 
   ![新しいプロジェクト](./media/tutorial-ev-routing/create-project.png)

4. 表示された **[新しいプロジェクトの作成]** ポップアップで、次の情報を入力して **[作成]** をクリックします。
    * プロジェクト名
    * プロジェクト ID
 
    ![プロジェクトの作成](./media/tutorial-ev-routing/create-project-window.png)

5. プロジェクトが作成されたら、[Azure Maps の Jupyter ノートブック リポジトリ](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)から [Jupyter ノートブック ドキュメント ファイル](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb)をダウンロードします。 

6. **[マイ プロジェクト]** ページのプロジェクト一覧で、作成したプロジェクトを選択し、 **[アップロード]** をクリックして Jupyter ノートブック ドキュメント ファイルをアップロードします。 コンピューターからファイルをアップロードし、 **[完了]** をクリックします。

    ![ノートブックのアップロード](./media/tutorial-ev-routing/upload-notebook.png)

7. アップロードが正常に完了すると、プロジェクト ページにファイルが表示されます。 ノートブック ファイルをクリックし、Jupyter ノートブックとして開きます。

このノートブック ファイルで実装される機能についての理解を深めるために、ノートブックのコードは一度に 1 セルずつ実行することをお勧めします。 Notebook アプリの上部にある **[実行]** ボタンをクリックすれば、セルごとのコードを実行できます。

  ![[実行]](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>プロジェクト レベルのパッケージをインストールする

ノートブックのコードを実行するには、プロジェクト レベルでパッケージをインストールしておく必要があります。 次の手順に従って、必要なパッケージをインストールします。

1. [Azure Maps の Jupyter ノートブック リポジトリ](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)から ["requirements.txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) ファイルをダウンロードして、プロジェクトにアップロードします。
2. プロジェクト ダッシュボードで、 **[Project Settings]\(プロジェクトの設定\)** を選択します。 
3. 表示されたポップアップで **[環境]** タブを選択し、 **[追加]** を選択します。
4. **[Environment Setup Steps]\(環境セットアップ手順\)** で、次の手順を実行します。 
    * 最初のドロップダウン コントロールで、**Requirements.txt** を選択します。
    * 2 番目のドロップダウン コントロールで、"requirements.txt" ファイルを選択します。
    * 3 番目のドロップダウン コントロールで、Python のバージョンとして [Python Version 3.6]\(Python バージョン 3.6\) を選択します。
7. **[保存]** を選択します。

    ![パッケージのインストール](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>必要なモジュールとフレームワークを読み込む

次のスクリプトを実行して、必要なモジュールとフレームワークすべてを読み込みます。

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>到達可能範囲の境界線を要求する

今回のシナリオでは、電気自動車を何台か導入した配送会社を考えます。 日中は、倉庫に戻ることなく電気自動車を再充電しなければなりません。 電気自動車の現在の充電残量が 1 時間分を下回る (電気自動車のバッテリ残量が低下する) たびに、到達可能範囲内にある充電スタンドを検索して、その範囲の境界線情報を取得する必要があります。 会社は経済性とスピードのバランスがとれたルートを使いたいと考えているので、要求する routeType は "eco" になります。 次のスクリプトでは、車両の消費モデルをパラメーターとして指定して、Azure Maps Routing Service の [Get Route Range API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) を呼び出します。そして応答を解析し、車の最大到達可能範囲を表す GeoJSON 形式のポリゴン オブジェクトを作成します。

次のセル内のスクリプトを実行して、電気自動車の到達可能範囲の境界線を取得します。

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>到達可能範囲内にある電気自動車充電スタンドを検索する

電気自動車の到達可能範囲 (等時線) が得られたので、この範囲内にある充電スタンドを検索できます。 次のスクリプトでは、Azure Maps の [Post Search Inside Geometry API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) を呼び出して、自動車の到達可能範囲内にある電気自動車充電スタンドを検索し、応答を解析して、到達可能な場所の配列を取得します。

次のスクリプトを実行して、到達可能範囲内にある電気自動車充電スタンドを検索します。

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Azure Maps Data Service に到達可能範囲と充電ポイントをアップロードする

充電スタンドと電気自動車の最大到達可能範囲の境界線をマップ上で視覚化するには、[Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) を使用して、境界線データと充電スタンド データを GeoJSON オブジェクトとして Azure Maps Data Service にアップロードする必要があります。 

次の 2 つのセルを実行して、Azure Maps Data Service に境界線と充電ポイントのデータをアップロードします。

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>マップ上に充電スタンドと到達可能範囲をレンダリングする

データ サービスにデータをアップロードできたので、次のスクリプトを実行して Azure Maps [Get Map Image Service](https://docs.microsoft.com/rest/api/maps/render/getmapimage) を呼び出し、静的マップ画像の上に充電ポイントと最大到達可能範囲の境界線をレンダリングします。

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![場所の範囲](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>立ち寄るのに最適な充電スタンドを特定する

到達可能範囲内にある充電スタンド候補がすべてわかったので、最も短い時間で到達できるスタンドはどれかを調べましょう。 次のスクリプトでは、Azure Maps [Matrix Routing API](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) を呼び出します。この API は、指定した自動車の位置に対し、指定した各充電スタンドの場所までの走行時間と距離を返します。 次のセルのスクリプトでは、応答を解析して、時間的に最も近くにある到達可能な充電スタンドの場所を取得します。

次のセルを実行して、最も近くにあり最短時間で到達可能な充電スタンドを特定します。

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>最も近い充電スタンドへのルートを計算する

最も近い充電スタンドがわかったので、次は [Get Route Directions API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) を呼び出して、電気自動車の現在位置から充電スタンドまでの詳細なルートを要求します。

次のセルのスクリプトを実行して、ルートを取得し、応答を解析してルートを表す GeoJSON オブジェクトを作成します。

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>ルートを視覚化する

ルートを視覚化するには、まず Azure Maps の [Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) を使用して、Azure Maps Data Service にルートのデータを GeoJSON オブジェクトとしてアップロードします。 次に Render Service の [Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) を呼び出して、マップ上にルートをレンダリングして視覚化します。

次のスクリプトを実行して、マップ上でルートをレンダリングした画像を取得します。

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![ルート](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Python を使用して Azure Maps REST API シリーズを直接呼び出し、Azure Maps のデータを視覚化する方法について説明しました。

このチュートリアルで使用した Azure Maps API シリーズの詳細については、以下を参照してください。

* [Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [レンダリング - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Post Route Matrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Azure Maps REST API シリーズの完全な一覧については、次を参照してください。

* [Azure Maps REST API シリーズ](https://docs.microsoft.com/azure/azure-maps/#reference)

Azure Notebooks の詳細については、次を参照してください。

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)