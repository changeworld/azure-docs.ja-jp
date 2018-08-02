---
title: Azure IoT Hub ジョブについて | Microsoft Docs
description: 開発者ガイド - IoT Hub に接続されている複数のデバイスで実行するジョブのスケジュール設定。 ジョブはタグと必要なプロパティを更新でき、複数のデバイス上でダイレクト メソッドを呼び出すことができます。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 460c7d24b2810de41e20ea803ded2ea988613f10
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223798"
---
# <a name="schedule-jobs-on-multiple-devices"></a>複数デバイスでのジョブをスケジュール設定する

Azure IoT Hub では、[デバイス ツインのプロパティとタグ][lnk-twin-devguide]や[ダイレクト メソッド][lnk-dev-methods]のような多数の構成要素を使用できます。  通常、デバイス管理者とオペレーターは、バックエンド アプリを使用して、IoT デバイスの更新と対話を、指定した時刻に一括で実行できます。  ジョブは、指定した時刻に一連のデバイスに対してデバイス ツインの更新とダイレクト メソッドを実行します。  たとえば、オペレーターは、ビル 43 の 3 階にある一連のデバイスを、ビルの運用に悪影響を与えることがない時刻に再起動するジョブを開始して追跡するバックエンド アプリを使用します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

一連のデバイスで次のアクティビティのスケジュールを設定し、その進行状況を追跡する必要があるときは、ジョブの使用を検討します。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

## <a name="job-lifecycle"></a>ジョブのライフサイクル
ジョブは、ソリューションのバック エンドによって開始され、IoT Hub によって管理されます。  ジョブは、サービス向け URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) を通して開始でき、実行中のジョブの進行状況は、サービス向け URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`) を通して照会できます。 ジョブが開始された後で実行中のジョブの状態を更新するには、ジョブ クエリを実行します。

> [!NOTE]
> ジョブを呼び出すとき、プロパティ名と値には `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT` を除く US-ASCII 印刷可能英数字のみを使用できます。

## <a name="jobs-to-execute-direct-methods"></a>ダイレクト メソッドを実行するジョブ
次のスニペットでは、ジョブを使ってデバイス上で[ダイレクト メソッド][lnk-dev-methods]を実行するための HTTPS 1.1 要求の詳細を示します。

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

クエリ条件は、1 つのデバイス ID に対するものであっても、次の例で示すようにデバイス ID のリストに対するものであってもかまいません。

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
IoT Hub クエリ言語の詳細については、[IoT Hub クエリ言語][lnk-query]に関するページをご覧ください。

## <a name="jobs-to-update-device-twin-properties"></a>デバイス ツインのプロパティを更新するジョブ
次のスニペットでは、ジョブを使ってデバイス ツインのプロパティを更新するための HTTPS 1.1 要求の詳細を示します。

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>ジョブの進行状況の照会
次のスニペットでは、ジョブを照会するための HTTPS 1.1 要求の詳細を示します。

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

ContinuationToken は、応答から提供されます。  

それぞれのデバイスにおけるジョブの実行状態は、[デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語][lnk-query]を使用して照会することができます。

## <a name="jobs-properties"></a>ジョブのプロパティ
次の一覧は、ジョブまたはジョブの結果を照会するときに使うことができるプロパティとその説明です。

| プロパティ | 説明 |
| --- | --- |
| **jobId** |アプリケーションが提供するジョブの ID。 |
| **startTime** |アプリケーションが提供するジョブの開始時刻 (ISO 8601)。 |
| **endTime** |IoT Hub が提供するジョブの完了時の日付 (ISO 8601)。 ジョブが 'completed' 状態に達した後でのみ有効です。 |
| **type** |ジョブの種類: |
| | **scheduledUpdateTwin**: 必要なプロパティまたはタグを更新するために使用するジョブ。 |
| | **scheduledDeviceMethod**: デバイス ツインでデバイス メソッドを呼び出すために使用するジョブ。 |
| **status** |ジョブの現在の状態。 状態の可能値: |
| | **pending**: スケジュールが設定され、ジョブ サービスによって選択されるために待機しています。 |
| | **scheduled**: 将来の時刻のスケジュールが設定されています。 |
| | **running**: 現在アクティブなジョブです。 |
| | **canceled**: ジョブは取り消されました。 |
| | **failed**: ジョブは失敗しました。 |
| | **completed**: ジョブは完了しています。 |
| **deviceJobStatistics** |ジョブの実行に関する統計情報。 |
| | **deviceJobStatistics** プロパティ: |
| | **deviceJobStatistics.deviceCount**: ジョブ内のデバイスの数。 |
| | **deviceJobStatistics.failedCount**: ジョブが失敗したデバイスの数。 |
| | **deviceJobStatistics.succeededCount**: ジョブが成功したデバイスの数。 |
| | **deviceJobStatistics.runningCount**: 現在ジョブが実行中であるデバイスの数。 |
| | **deviceJobStatistics.pendingCount**: ジョブの実行が保留中であるデバイスの数。 |

### <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-query]: IoT Hub クエリ言語について説明します。 このクエリ言語を使用して、IoT Hub からデバイス ツインとジョブに関する情報を取得します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次の手順
この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [Schedule and broadcast jobs (ジョブのスケジュールとブロードキャスト)][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
