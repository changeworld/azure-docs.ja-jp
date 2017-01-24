---
title: "Azure IoT Hub 操作の監視 | Microsoft Docs"
description: "Azure IoT Hub 操作の監視を使用して、IoT Hub に対する操作の状態をリアルタイムで監視する方法。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: f6f894157a31641b1d0294e84795563c727caaac


---
# <a name="operations-monitoring"></a>操作の監視
IoT Hub の操作の監視では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。 IoT Hub は、複数のカテゴリにまたがる操作のイベントを追跡します。 1 つ以上のカテゴリから IoT ハブのエンドポイントにイベントを送信して処理するように選択することができます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

IoT Hub では、次の 5 つのカテゴリのイベントを監視します。

* デバイス ID の操作
* デバイス テレメトリ
* クラウドからデバイスへのメッセージ
* 接続
* ファイルのアップロード

## <a name="how-to-enable-operations-monitoring"></a>操作の監視を有効にする方法
1. IoT Hub を作成します。 IoT ハブの作成方法の手順については、[使用開始][lnk-get-started]に関するガイドを参照してください。
2. IoT Hub のブレードを開きます。 このブレードで、 **[操作の監視]**をクリックします。
   
    ![][1]
3. 監視する監視カテゴリを選択し、 **[保存]**をクリックします。 イベントは、 **[監視の設定]**に一覧表示された Event Hub 対応のエンドポイントから読み取ることができます。 IoT Hub エンドポイントの名前は `messages/operationsmonitoringevents`です。
   
    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>イベント カテゴリとその使用方法
操作監視の各カテゴリでは、IoT Hub との各種のやり取りを追跡します。各監視カテゴリは、カテゴリ内のイベントの構成方法を定義するスキーマを備えています。

### <a name="device-identity-operations"></a>デバイス ID の操作
デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>デバイス テレメトリ
デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできないので注意してください。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド
C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、クラウドからデバイスへのメッセージの送信時のエラー (許可されていない送信者など)、クラウドからデバイスへのメッセージの受信時のエラー (配信数が上限を超えているなど)、クラウドからデバイスへのメッセージ フィードバックの受信時のエラー (フィードバックの有効期限切れなど) が含まれます。 このカテゴリでは、クラウドからデバイスへのメッセージが正常に配信されてもクラウドからデバイスへのメッセージを適切に処理しないデバイスのエラーはキャッチしません。

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>接続
接続のカテゴリでは、デバイスが IoT Hub に接続したときに発生する、または IoT Hub から切断したときのエラーを追跡します。 このカテゴリの追跡は、許可されていない接続の試行を識別する場合、および接続状態が悪い領域内で接続が失われたタイミングを突き止める場合に便利です

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>ファイルのアップロード

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

- SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。
- デバイスによって報告されたアップロード エラー。
- IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

デバイスがファイルをストレージにアップロードしているときに直接発生したエラーについては、このカテゴリではキャッチできないことに注意してください。


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>次のステップ
IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Dec16_HO1-->


