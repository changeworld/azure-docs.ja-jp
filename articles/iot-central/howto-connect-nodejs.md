---
title: 汎用の Node.js クライアント アプリケーションを Azure IoT Central に接続する | Microsoft Docs
description: デバイス開発者として、汎用の Node.js デバイスを Azure IoT Central アプリケーションに接続する方法。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 55ce85702804d99d806220d7f0a4ea0820975f4f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206039"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>汎用のクライアント アプリケーションを Azure IoT Central アプリケーションに接続する (Node.js)

この記事では、デバイス開発者として、物理デバイスを表す汎用の Node.js アプリケーションを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

1. Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
1. [Node.js](https://nodejs.org/) バージョン 4.0.0 以降がインストールされた開発用コンピューター。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。

## <a name="create-a-device-template"></a>デバイス テンプレートを作成する

Azure IoT Central アプリケーションでは、次の測定およびデバイスのプロパティが定義されたデバイス テンプレートが必要です。

### <a name="telemetry-measurements"></a>テレメトリ測定

**[Measurements] (測定)** ページで、次のテレメトリを追加します。

| 表示名 | フィールド名  | Units | Min | max | 小数点以下の桁数 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 気温  | 温度 | F     | 60  | 110 | 0              |
| 湿度     | 湿度    | %     | 0   | 100 | 0              |
| 圧力     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  テレメトリ測定のデータ型は倍精度浮動小数点です。

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が一致しない場合は、テレメトリをアプリケーションに表示できません。

### <a name="state-measurements"></a>状態測定

**[Measurements] (測定)** ページで、次の状態を追加します。

| 表示名 | フィールド名  | 値 1 | 表示名 | 値 2 | 表示名 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fan Mode     | fanmode     | 1       | 実行中      | 0       | 停止済み      |

> [!NOTE]
  状態測定のデータ型は文字列です。

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が一致しない場合は、状態をアプリケーションに表示できません。

### <a name="event-measurements"></a>イベント測定

**[Measurements] (測定)** ページで、次のイベントを追加します。

| 表示名 | フィールド名  | severity |
| ------------ | ----------- | -------- |
| 過熱  | overheat    | エラー    |

> [!NOTE]
  イベント測定のデータ型は文字列です。

### <a name="device-properties"></a>デバイスのプロパティ

**プロパティ ページ** で、次のデバイスのプロパティを追加します。

| 表示名        | フィールド名        | データ型 |
| ------------------- | ----------------- | --------- |
| Serial Number       | serialNumber      | text      |
| デバイスの製造元 | manufacturer      | text      |

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が一致しない場合は、アプリケーションでプロパティ値を表示できません。

### <a name="settings"></a>設定

**設定ページ**で、次の**数値**の設定を追加します。

| 表示名    | フィールド名     | Units | 10 進数 | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| ファン速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Set Temperature | setTemperature | F     | 0        | 20  | 200  | 80      |

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が一致しない場合は、デバイスで設定値を受信できません。

## <a name="add-a-real-device"></a>実デバイスを追加する

Azure IoT Central アプリケーションでは、作成したデバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

次の手順は、アプリケーションに追加した実デバイスを実装するクライアント アプリケーションを作成する方法を示しています。

1. コンピューター上で `connected-air-conditioner-adv` という名前のフォルダーを作成します。 コマンドライン環境でそのフォルダーに移動します。

1. Node.js プロジェクトを初期化するには、次のコマンドを実行します。

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. `connected-air-conditioner-adv` フォルダー内に **connectedAirConditionerAdv.js** という名前のファイルを作成します。

1. **connectedAirConditionerAdv.js** ファイルの先頭に次の `require` ステートメントを追加します。

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. このファイルに次の変数宣言を追加します。

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    プレースホルダー `{your device connection string}` をデバイスの接続文字列に更新します。 この値は、実デバイスを追加したときに接続の詳細ページからコピーしました。 このサンプルでは、`targetTemperature` を 0 に初期化します。必要に応じて、デバイスから現在の読み取りを取得したり、デバイス ツインから値を取得したりできます。 

1. テレメトリ、状態、およびイベント測定を Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. デバイスのプロパティを Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. デバイスが応答する設定を定義するには、次の定義を追加します。

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central アプリケーションから更新された設定を処理するには、ファイルに次を追加します。

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. 次を追加して Azure IoT Central への接続を完了し、クライアント コード内の関数を起動します。

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Node.js アプリケーションを実行する

コマンドライン環境で次のコマンドを実行します。

```cmd/sh
node connectedAirConditionerAdv.js
```

Azure IoT Central アプリケーションのオペレーターとして、実デバイスに関して次のことを実行できます。

* **[Measurements] (測定)** ページで、テレメトリを表示します。

    ![利用統計情報データを表示する](media/howto-connect-nodejs/viewtelemetry.png)

* **[プロパティ]** ページで、デバイスから送信されたデバイス プロパティ値を表示します。

    ![デバイスのプロパティを表示する](media/howto-connect-nodejs/viewproperties.png)

* **[設定]** ページから、ファン速度とターゲット温度を設定します。

    ![ファン速度を設定する](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>次の手順

ここでは、汎用の Node.js クライアントを Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は以下のとおりです。
* [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
