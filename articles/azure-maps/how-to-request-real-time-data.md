---
title: リアルタイムの輸送データを要求する | Microsoft Azure Maps
description: Microsoft Azure Maps Mobility Service を使用してリアルタイム データを要求します。
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335476"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Azure Maps Mobility Service を使用してリアルタイム データを要求する

この記事では、Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) を使用してリアルタイムの輸送データを要求する方法を説明します。

この記事では、以下を行う方法について説明します。


 * 特定の停留所に到着するすべての路線について次のリアルタイムの到着を要求する
 * 特定の自転車ドック ステーションのリアルタイム情報を要求する。


## <a name="prerequisites"></a>前提条件

Azure Maps で公共輸送 API を呼び出すには、最初に Azure Maps アカウントとサブスクリプション キーを用意する必要があります。 [こちらに](quick-demo-map-app.md#create-an-account-with-azure-maps)アカウントの作成方法が記載されているので、指示に従い、Azure Maps アカウントを作成してください。 [プライマリ キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)に関するセクションの手順に従って、アカウントのプライマリ キーを取得してください。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。


この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。


## <a name="request-real-time-arrivals-for-a-stop"></a>停留所のリアルタイムの到着を要求する

特定の公共輸送機関の停留所にリアルタイムの到着データを要求するには、Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityRealTimeArrivals) の [Real-time Arrivals API](https://aka.ms/AzureMapsMobilityService) に要求を送信する必要があります。 要求を完了するには **metroID** と **stopID** が必要です。 これらのパラメーターを要求する方法の詳細については、[公共輸送機関のルートの要求](https://aka.ms/AMapsHowToGuidePublicTransitRouting)方法に関するガイドを参照してください。 

地下鉄 ID として "522" を使用しましょう。これは、"Seattle–Tacoma–Bellevue, WA" 地域の地下鉄 ID です。 停留所 ID として "522---2060603" を使用してください。このバス停は "Ne 24th St & 162nd Ave Ne, Bellevue WA" にあります。 この停留所での次のすべてのライブ到着について、次の 5 つのリアルタイム到着データを要求するには、次の手順を行います。

1. Postman アプリを起動してください。要求を保存するコレクションを作成しましょう。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で要求の保存場所として作成したコレクションを選択します。 次に、 **[保存]** を選択します。

    ![Postman での要求の作成](./media/how-to-request-transit-data/postman-new.png)

3. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力して GET 要求を作成します。 `{subscription-key}` は実際の Azure Maps 主キーに置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 要求に成功すると、次の応答が表示されます。  パラメーター 'scheduleType' では、到着予定時刻がリアルタイム データと静的データのどちらに基づいているかを定義する点に注目してください。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>自転車ドック ステーションのリアルタイム データ

[Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) を利用すると、静的なリアルタイム情報を要求できます。 たとえば、自転車やスクーター置き場の空き情報を要求できます。 [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) は Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) にも含まれています。

[Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) への要求を行うためには、そのステーションの **dockId** が必要です。 "bikeDock" に割り当てられている [objectType](https://aka.ms/AzureMapsMobilityNearbyTransit) パラメーターで **Get Nearby Transit API** に検索要求を行うことでドック ID を取得できます。 自転車のドッキング ステーションのリアルタイム データを取得するには、次の手順に従います。


### <a name="get-dock-id"></a>ドック ID を取得する

**dockID** を取得するには、次の手順に従って Get Nearby Transit API への要求を行います。

1. Postman で、 **[新しい要求]**  |  **[GET 要求]** をクリックし、「**Get dock ID (ドック ID の取得)** 」 という名前を付けます。

2.  [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、次の要求 URL を入力して **[Send]\(送信\)** をクリックします。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 要求に成功すると、次の応答が表示されます。 応答に **id** が含まれていることに注目してください。これは、後で Get Transit Dock Info API への要求でクエリ パラメーターとして使用できます。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>リアルタイムの自転車ドックの状態を取得する

以下の手順に従って、Get Transit Dock Info API に対する要求を行い、選択したドックのリアルタイム データを取得します。

1. Postman で、 **[New Request]\(新しい要求\)**  |  **[GET request]\(GET 要求\)** をクリックし、「**Get Route info**」という名前を付けます。

2.  [Builder]\(ビルダー\) タブで、**GET** HTTP メソッドを選択し、次の要求 URL を入力して **[Send]\(送信\)** をクリックします。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 要求に成功すると、次の構造の応答を受け取ります。

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>次のステップ

Mobility Service を使用して輸送データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [輸送データを要求する方法](how-to-request-transit-data.md)

Azure Maps Mobility Service API のドキュメントを確認します。

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](https://aka.ms/AzureMapsMobilityService)
