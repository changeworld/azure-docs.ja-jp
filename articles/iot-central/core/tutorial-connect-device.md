---
title: チュートリアル - 汎用の Node.js クライアント アプリを Azure IoT Central に接続する | Microsoft Docs
description: このチュートリアルでは、デバイス開発者を対象に、Node.js クライアント アプリが実行されているデバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 デバイス機能モデルをインポートしてデバイス テンプレートを作成し、接続されているデバイスを対話的に操作できるビューを追加します。
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624338"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>チュートリアル:Node.js クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Node.js)

このチュートリアルでは、デバイス開発者を対象に、Node.js クライアント アプリケーションを Azure IoT Central アプリケーションに接続する方法を説明します。 この Node.js アプリケーションは、実デバイスの動作をシミュレートします。 IoT Central で、環境センサー デバイス用のサンプル "_デバイス機能モデル_" を使用して "_デバイス テンプレート_" を作成します。 このデバイス テンプレートに、デバイス テレメトリの視覚化、デバイス プロパティの管理、デバイスの制御コマンドの使用を実行できるビューを追加します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス機能モデルをインポートしてデバイス テンプレートを作成する。
> * 既定のビューとカスタム ビューをデバイス テンプレートに追加する。
> * デバイス テンプレートを発行し、Azure IoT Central アプリケーションに実デバイスを追加する。
> * Node.js デバイス コードを作成して実行し、IoT Central アプリケーションへの接続を確認する。
> * デバイスが送信するシミュレートされたテレメトリを表示する。
> * ビューを使用してデバイス プロパティを管理する。
> * コマンドを呼び出してデバイスを制御する。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
* [Node.js](https://nodejs.org/) バージョン 10.0.0 以降がインストールされた開発用マシン。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。 このチュートリアルの手順は、Windows コマンド プロンプトから **node** コマンドを実行することを前提としています。 Node.js はさまざまなオペレーティング システムで使用できます。

## <a name="create-a-device-template"></a>デバイス テンプレートを作成する

ローカル コンピューターに `environmental-sensor` という名前のフォルダーを作成します。

[環境センサー機能モデル](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)の JSON ファイルをダウンロードして `environmental-sensor` フォルダーに保存します。

ダウンロードした `EnvironmentalSensorInline.capabilitymodel.json` ファイル内で 2 か所出現する `{YOUR_COMPANY_NAME_HERE}` を、テキスト エディターを使用して実際の会社名に置き換えます。

お使いの Azure IoT Central アプリケーションで、`EnvironmentalSensorInline.capabilitymodel.json` デバイス機能モデル ファイルをインポートして、"*環境センサー*" というデバイス テンプレートを作成します。

![デバイス機能モデルがインポートされたデバイス テンプレート](./media/tutorial-connect-device/device-template.png)

このデバイス機能モデルには、標準の **[デバイス情報]** インターフェイスとカスタムの **[環境センサー]** インターフェイスという 2 つのインターフェイスが含まれています。 **[環境センサー]** インターフェイスには、次の機能が定義されています。

| Type | 表示名 | 説明 |
| ---- | ------------ | ----------- |
| プロパティ | デバイス状態     | デバイスの状態。 オンラインとオフラインの 2 つの状態があります。 |
| プロパティ | Customer Name    | 現在デバイスを操作しているユーザーの名前。 |
| プロパティ | Brightness Level (明るさのレベル) | デバイスのライトに使用する明るさのレベル。 1 (高)、2 (中)、3 (低) を指定できます。 |
| テレメトリ | 気温 | デバイスの現在の温度。 |
| テレメトリ | 湿度    | デバイスの現在の湿度。 |
| command | blink          | 特定の間隔での LED の点滅を開始します。 |
| command | turnon         | デバイスの LED ライトをオンにします。 |
| command | turnoff        | デバイスの LED ライトをオフにします。 |
| command | rundiagnostics | このコマンドで、診断を開始します。 |

IoT Central アプリケーションにおける **[デバイスの状態]** プロパティの表示方法をカスタマイズするには、デバイス テンプレートの **[カスタマイズ]** を選択します。 **[デバイスの状態]** エントリを展開し、 **[True の名前]** として「_Online_」、 **[False の名前]** として「_Offline_」を入力します。 変更を保存します。

![デバイス テンプレートをカスタマイズする](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>ビューの作成

IoT Central アプリケーションに接続されたデバイスは、ビューを使って操作できます。 たとえば、テレメトリを表示するビュー、プロパティを表示するビュー、書き込み可能なプロパティやクラウドのプロパティを編集できるビューが考えられます。 ビューは、デバイス テンプレートの構成要素です。

**[環境センサー]** デバイス テンプレートに既定のビューを追加するには、お使いのデバイス テンプレートに移動し、 **[ビュー]** を選択して、 **[既定のビューの生成]** タイルを選択します。 **[概要]** と **[About]\(情報\)** が **[オン]** になっていることを確認し、 **[既定のダッシュボード ビューの生成]** を選択します。 これでテンプレートには、既定のビューが 2 つ定義されました。

**[環境センサー]** インターフェイスには、 **[Customer Name]\(カスタマー名\)** と **[Brightness Level]\(明るさのレベル\)** という、書き込み可能なプロパティが 2 つあります。 これらのプロパティの編集に使用できるビューを作成するには、次の手順に従います。

1. **[ビュー]** を選択し、 **[デバイスとクラウドのデータの編集]** タイルを選択します。

1. フォーム名として「_Properties_」を入力します。

1. **[Brightness Level]\(明るさのレベル\)** および **[Customer Name]\(カスタマー名\)** プロパティを選択します。 次に、 **[セクションの追加]** を選択します。

1. 変更を保存します。

![プロパティを編集できるビューを追加する](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>テンプレートを発行する

**[環境センサー]** デバイス テンプレートを使用する IoT Central アプリケーションにデバイスを追加するには、あらかじめそのテンプレートを発行しておく必要があります。

デバイス テンプレートで **[発行]** を選択します。 発行対象の変更が表示されるパネルで、 **[発行]** を選択します。

テンプレートを使用する準備が整っていることを確認するために、IoT Central アプリケーションの **[デバイス]** ページに移動します。 発行済みデバイスの一覧がアプリケーションの **[デバイス]** セクションに表示されます。

![[デバイス] ページの発行済みテンプレート](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションで、前のセクションで作成したデバイス テンプレートに実デバイスを追加します。

1. **[デバイス]** ページで、 **[環境センサー]** デバイス テンプレートを選択します。

1. **[+新規]** を選択します。

1. **[Simulated]\(シミュレート済み\)** が **[Off]\(オフ\)** になっていることを確認します。 **[作成]** を選択します。

デバイス名をクリックし、 **[接続]** を選択します。 **[デバイスの接続]** ページで、デバイスの接続情報を書き留めておきます ( **[ID スコープ]** 、 **[デバイス ID]** 、 **[プライマリ キー]** )。 デバイス コードを作成する際に、これらの値が必要になります。

![デバイスの接続情報](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

以降の手順では、アプリケーションに追加した実デバイスを実装する Node.js クライアント アプリケーションの作成方法を説明します。 この Node.js アプリケーションは、実デバイスの動作をシミュレートします。

1. コマンドライン環境で、先ほど作成した `environmental-sensor` フォルダーに移動します。

1. Node.js プロジェクトを初期化し、必要な依存関係をインストールするために、次のコマンドを実行します。`npm init` を実行する際は、既定のオプションをすべてそのまま使用してください。

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. `environmental-sensor` フォルダーに、**environmentalSensor.js** という名前のファイルを作成します。

1. **environmentalSensor.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. このファイルに次の変数宣言を追加します。

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    `{your Scope ID}`、`{your Device ID}`、`{your Primary Key}` の各プレースホルダーを、前に書き留めた値で更新します。 このサンプルでは、`targetTemperature` を 0 に初期化します。デバイスから現在の読み取りを取得したり、デバイス ツインから値を取得したりできます。

1. Azure IoT Central アプリケーションにテレメトリを送信するために、次の関数をファイルに追加します。

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. デバイスのプロパティを Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. デバイス側で対応する書き込み可能なプロパティを定義して処理するために、次のコードを追加します。

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. IoT Central アプリケーションから送信されたコマンドを処理するために次のコードを追加します。

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Azure IoT Central への接続を完了し、クライアント コードの関数をフックする次のコードを追加します。

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Node.js アプリケーションを実行する

デバイス クライアント アプリケーションを起動するには、コマンドライン環境で次のコマンドを実行します。

```cmd/sh
node environmentalSensor.js
```

デバイスが Azure IoT Central アプリケーションに接続して、テレメトリを送信し始めたことがわかります。

![クライアント アプリケーションを実行する](media/tutorial-connect-device/run-application.png)

Azure IoT Central アプリケーションのオペレーターとして、次のことを実行できます。

* デバイスから送信されたテレメトリを **[概要]** ページで確認します。

    ![利用統計情報データを表示する](media/tutorial-connect-device/view-telemetry.png)

* 書き込み可能なプロパティの値を **[プロパティ]** ページで更新します。

    ![プロパティを更新する](media/tutorial-connect-device/update-properties.png)

    ![デバイスのプロパティを更新する](media/tutorial-connect-device/update-properties-device.png)

* **[コマンド]** ページからコマンドを呼び出します。

    ![blink コマンドを呼び出す](media/tutorial-connect-device/call-command.png)

    ![デバイスの blink コマンドを呼び出す](media/tutorial-connect-device/call-command-device.png)

* **[About]\(情報\)** ページでデバイスのプロパティを確認します。

    ![プロパティの表示](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>次のステップ

デバイス機能モデルと、独自のデバイス テンプレートの作成方法の詳細については、攻略ガイドに進んでください。

> [!div class="nextstepaction"]
> [新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)
