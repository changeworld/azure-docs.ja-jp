<properties
 pageTitle="IoT Hub 操作の監視"
 description="Azure IoT Hub の操作の監視に関する概要 (ユーザーは IoT Hub に対する操作の状態をリアルタイムで監視できる)"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/07/2016"
 ms.author="nberdy"/>

# 操作の監視の概要

IoT Hub の操作の監視では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。IoT Hub では複数のカテゴリの操作にわたってイベントを追跡するので、ユーザーは、処理のために IoT Hub のエンドポイントに送信された 1 つまたは複数のカテゴリのイベントの使用を有効にすることがでます。ユーザーは、データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

IoT Hub では、次の 5 つのカテゴリのイベントを監視します。

- デバイス ID の操作
- デバイス テレメトリ
- クラウドからデバイスへのコマンド
- 接続
- ファイルのアップロード

## 操作の監視を有効にする方法

1. IoT Hub を作成します。IoT Hub の作成方法の手順については、[使用開始][lnk-get-started]に関するガイドをご覧ください。

2. IoT Hub のブレードを開きます。ブレードの **[すべての設定]** をクリックし、**[操作の監視]** をクリックします。

    ![][1]

3. 監視する監視カテゴリを選択し、**[保存]** をクリックします。イベントは、**[監視の設定]** に一覧表示された Event Hub 対応のエンドポイントから読み取ることができます。IoT Hub エンドポイントの名前は `messages/operationsmonitoringevents` です。

    ![][2]

## イベント カテゴリとその使用方法

操作監視の各カテゴリでは、IoT Hub との各種のやり取りを追跡します。各監視カテゴリは、カテゴリ内のイベントの構成方法を定義するスキーマを備えています。

### デバイス ID の操作

デバイス ID の操作のカテゴリでは、ユーザーが IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### デバイス テレメトリ

デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。これには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできないので注意してください。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### クラウドからデバイスへのコマンド

C2D コマンド カテゴリでは、IoT Hub で発生し、かつデバイスのコマンド パイプラインに関連しているエラーを追跡します。これには、コマンドの送信時のエラー (許可されていない送信者など)、コマンドの受信時のエラー (配信数が上限を超えているなど)、コマンド フィードバックの受信時のエラー (フィードバックの有効期限切れなど) が含まれます。このカテゴリでは、コマンドが正常に配信されてもコマンドを適切に処理しないデバイスのエラーはキャッチしません。

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### 接続

接続のカテゴリでは、デバイスが IoT Hub に接続したとき、または IoT Hub から切断したときのエラーを追跡します。このカテゴリの追跡は、許可されていない接続の試行を識別する場合、および接続状態が悪い領域内で接続が失われたタイミングを突き止める場合に便利です

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### ファイルのアップロード

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。これには、SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)、デバイスによって報告されたアップロード エラー、IoT Hub 通知メッセージの作成中、ストレージでファイルが見つからない、などのエラーが含まれます。デバイスがファイルをストレージにアップロードしているときに直接発生したエラーについては、このカテゴリではキャッチできないことに注意してください。

    {
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## 次のステップ

操作の監視の概要については以上です。IoT Hub の管理に関する詳細については、[IoT Hub へのアクセス管理][lnk-itpro]をご覧ください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0713_2016-->