---
title: Microsoft Azure Maps Mobility Service (プレビュー) を使用してリアルタイムの公共輸送データを要求する
description: 停留所への到着など、リアルタイムの公共輸送データを要求する方法について説明します。 この目的のために Azure Maps Mobility Service (プレビュー) を使用する方法について参照してください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6e7358e76cdcf07f39a9b5a5fcab7ab0151ebc32
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742623"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Azure Maps Mobility Service (プレビュー) を使用してリアルタイムの公共輸送データを要求する 

> [!IMPORTANT]
> Azure Maps Mobility Services のプレビューは廃止され、2021 年 10 月 5 日以降は使用できず、サポートも停止されます。 他のすべての Azure Maps API およびサービスは、この廃止の発表の影響を受けません。
> 詳細については、[Azure Maps Mobility のプレビューの廃止](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)に関するページを参照してください。


この記事では、Azure Maps [Mobility Service](/rest/api/maps/mobility) を使用してリアルタイムの公共輸送データを要求する方法を説明します。

この記事では、特定の停留所に到着するすべての路線について次のリアルタイムの到着を要求する方法を学習します

## <a name="prerequisites"></a>前提条件

Azure Maps で公共輸送 API を呼び出すには、最初に Azure Maps アカウントとサブスクリプション キーを用意する必要があります。 [こちらに](quick-demo-map-app.md#create-an-azure-maps-account)アカウントの作成方法が記載されているので、指示に従い、Azure Maps アカウントを作成してください。 [プライマリ キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)に関するセクションの手順に従って、アカウントのプライマリ キーを取得してください。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。

この記事では、[Postman アプリ](https://www.getpostman.com/apps)を使用して REST 呼び出しを構築します。 選択した任意の API 開発環境を使用できます。

## <a name="request-real-time-arrivals-for-a-stop"></a>停留所のリアルタイムの到着を要求する

特定の公共輸送機関の停留所にリアルタイムの到着データを要求するには、Azure Maps [Mobility Service (プレビュー)](/rest/api/maps/mobility) の [Real-time Arrivals API](/rest/api/maps/mobility/getrealtimearrivalspreview) に要求を送信する必要があります。 要求を完了するには **metroID** と **stopID** が必要です。 これらのパラメーターを要求する方法の詳細については、[公共輸送機関のルートの要求](./how-to-request-transit-data.md)方法に関するガイドを参照してください。

地下鉄 ID として "522" を使用しましょう。これは、"Seattle–Tacoma–Bellevue, WA" 地域の地下鉄 ID です。 停留所 ID として "522---2060603" を使用してください。このバス停は "Ne 24th St & 162nd Ave Ne, Bellevue WA" にあります。 この停留所での次のすべてのライブ到着について、次の 5 つのリアルタイム到着データを要求するには、次の手順を行います。

1. Postman アプリを開きます。 **[新規]** 選択して、要求を作成します。 **[新規作成]** ウィンドウで **[HTTP 要求]** を選択します。 要求の **[要求名]** を入力します。

2. [builder]\(ビルダー\) タブで **GET** HTTP メソッドを選択し、次の URL を入力して GET 要求を作成します。 `{subscription-key}` は実際の Azure Maps 主キーに置き換えてください。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

3. 要求に成功すると、次の応答が表示されます。  パラメーター 'scheduleType' では、到着予定時刻がリアルタイム データと静的データのどちらに基づいているかを定義する点に注目してください。

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

## <a name="next-steps"></a>次のステップ

Mobility Service (プレビュー) を使用して輸送データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [輸送データを要求する方法](how-to-request-transit-data.md)

Azure Maps Mobility Service (プレビュー) API のドキュメントを確認します。

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](/rest/api/maps/mobility)