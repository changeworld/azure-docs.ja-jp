---
title: "Node.js でリモート監視するために Raspberry Pi をプロビジョニング - Azure | Microsoft Docs"
description: "Node.js で記述されたアプリケーションを使用して、Raspberry Pi デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: dobett
ms.openlocfilehash: d401dde25bf4ab430ac045fb6cfd90050a7ec2e7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Raspberry Pi デバイスをリモート監視構成済みソリューションに接続する (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、リモート監視構成済みソリューションに物理デバイスを接続する方法について説明します。 このチュートリアルでは、リソース制約が少ない環境に適したオプションである Node.js を使用します。

### <a name="required-hardware"></a>必要なハードウェア

Raspberry Pi でコマンド ラインにリモート接続するためのデスクトップ コンピューター。

[Raspberry Pi 3 用 Microsoft IoT スタート キット](https://azure.microsoft.com/develop/iot/starter-kits/)または同等のコンポーネント。 このチュートリアルでは、キット内の次のものを使用します。

- Raspberry Pi 3
- microSD カード (NOOBS をインストール済み)
- USB ミニ ケーブル
- イーサネット ケーブル

### <a name="required-desktop-software"></a>必要なデスクトップ ソフトウェア

Raspberry Pi でコマンド ラインにリモートでアクセスするための SSH クライアントがデスクトップ コンピューターに必要です。

- Windows には SSH クライアントは含まれていません。 [PuTTY](http://www.putty.org/) を使用することをお勧めします。
- ほとんどの Linux ディストリビューションと Mac OS には、コマンド ライン SSH ユーティリティが含まれています。 詳細については、「[SSH Using Linux or Mac OS (Linux または Mac OS を使用した SSH 接続)](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)」を参照してください。

### <a name="required-raspberry-pi-software"></a>必要な Raspberry Pi ソフトウェア

まだインストールしていない場合は、Raspberry Pi に Node.js 4.0.0 以上をインストールします。 次の手順では、Node.js v6.11.4 を Raspberry Pi にインストールする方法を示します。

1. `ssh` を使用して Raspberry Pi に接続します。 詳細については、[Raspberry Pi の Web サイト](https://www.raspberrypi.org/)の [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) のセクションを参照してください。

1. 次のコマンドを使用して Raspberry Pi を更新します。

    ```sh
    sudo apt-get update
    ```

1. 次のコマンドを使用して、Node.js のバイナリを Raspberry Pi にダウンロードします。

    ```sh
    wget https://nodejs.org/dist/v6.11.4/node-v6.11.4-linux-armv7l.tar.gz
    ```

1. 次のコマンドを使用して、バイナリをインストールします。

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.11.4-linux-armv7l.tar.gz
    ```

1. 次のコマンドを使用して、Node.js v6.11.4 が正常にインストールされたことを確認します。

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Node.js ソリューションを作成する

Raspberry Pi への `ssh` 接続を使用して、次の手順を実行します。

1. `RemoteMonitoring` という名前のフォルダーを Raspberry Pi のホーム フォルダーで作成します。 コマンド ラインでこのフォルダーに移動します。

    ```sh
    cd ~
    mkdir RemoteMonitoring
    cd RemoteMonitoring
    ```

1. サンプル アプリケーションを完成させるために必要なパッケージをダウンロードしてインストールするには、次のコマンドを実行します。

    ```sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. `RemoteMonitoring` フォルダーで、**remote_monitoring.js** という名前のファイルを作成します。 このファイルをテキスト エディターで開きます。 Raspberry Pi では、`nano` または `vi` テキスト エディターを使用できます。

1. **remote_monitoring.js** ファイルに次の `require` ステートメントを追加します。

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. `require` ステートメントの後に次の変数宣言を追加します。 プレースホルダー値 `{Device Id}` と `{Device Key}` を、リモート監視ソリューションでプロビジョニングしたデバイス用の値に置き換えます。 ソリューションからの IoT Hub ホスト名を使用して、`{IoTHub Name}` を置き換えます。 たとえば、IoT Hub のホスト名が `contoso.azure-devices.net` の場合、`{IoTHub Name}` を `contoso` に置き換えます。

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. 基本のテレメトリ データを定義するには、次の変数を追加します。

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. プロパティ値を定義するには、次の変数を追加します。

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. ソリューションに送信する、報告されたプロパティを定義するには、次の変数を追加します。 これらのプロパティには、デバイスが使用するメソッドとテレメトリを記述するメタデータが含まれます。

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. 操作結果を出力するには、次のヘルパー関数を追加します。

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. テレメトリの値をランダム化するために使用する次のヘルパー関数を追加します。

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. ソリューションからのダイレクト メソッドの呼び出しを処理するため、次の関数を追加します。 ソリューションは、デバイス上で動作するダイレクト メソッドを使用します。

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. ソリューションにテレメトリ データを送信するには、次のコードを追加します。 クライアント アプリは、メッセージ スキーマを識別するために、メッセージにプロパティを追加します。

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. クライアント インスタンスを作成するために次のコードを追加します。

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 次の処理を行うために、以下のコードを追加します。

    - 接続を開きます。
    - 必要なプロパティのハンドラーを設定します。
    - 報告されたプロパティを送信します。
    - ダイレクト メソッドのハンドラーを登録します。
    - テレメトリの送信を開始します。

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. 変更を **remote_monitoring.js** ファイルに保存します。

1. Raspberry Pi 上のコマンド プロンプトで次のコマンドを実行して、サンプル アプリケーションを起動します。

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
