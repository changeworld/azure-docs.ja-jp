---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 6a6baa14d7521f4a85350af7b08b5fcbe82ddf6b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033838"
---
## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](../articles/iot-central/core/quick-deploy-iot-central.md)に関するページをご覧ください。 アプリケーションは、2020 年 7 月 14 日以降に作成されている必要があります。
* [Node.js](https://nodejs.org/) バージョン 6 以降がインストールされた開発用マシン。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 このチュートリアルの手順は、Windows コマンド プロンプトから **node** コマンドを実行することを前提としています。 ただし、他の多くのオペレーティング システムで Node.js を使用できます。
* サンプル コードが格納された [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub リポジトリのローカル コピー。 このリンク ([ZIP のダウンロード](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)) を使用してリポジトリのコピーをダウンロードしてください。 次に、ローカル コンピューター上の適切な場所にファイルを解凍します。

## <a name="review-the-code"></a>コードの確認

先ほどダウンロードした Microsoft Azure IoT SDK for Node.js のコピーにある *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* ファイルをテキスト エディターで開きます。

このサンプルを実行して IoT Central に接続すると、Device Provisioning Service (DPS) を使用してデバイスが登録され、接続文字列が生成されます。 このサンプルでは、必要な DPS 接続情報がコマンドライン環境から取得されます。

`main` メソッド:

* `client` オブジェクトを作成し、接続が開かれる前に `dtmi:com:example:Thermostat;1` モデル ID を設定します。 IoT Central では、モデル ID を使用して、このデバイスのデバイス テンプレートを識別または生成します。 詳細については、「[デバイス テンプレートへのデバイスの関連付け](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)」をご覧ください。
* コマンド ハンドラーを作成します。
* 10 秒ごとに温度テレメトリを送信するループを開始します。
* `maxTempSinceLastReboot` プロパティを IoT Central に送信します。 `serialNumber` プロパティはデバイス モデルには属さないため、IoT Central によって無視されます。
* 書き込み可能なプロパティのハンドラーを作成します。

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 関数は、このデバイスが DPS を使用して IoT Central に登録、接続する方法を示しています。 ペイロードには、[デバイスをデバイス テンプレートに関連付ける](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)ために IoT Central が使用するモデル ID が含まれています。

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 関数は、温度テレメトリをデバイスから IoT Central に送信する方法を示しています。 `getCurrentTemperatureObject` メソッドからは、`{ temperature: 45.6 }` のようなオブジェクトが返されます。

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` メソッドは、次の 2 つのメソッドを使用して、`maxTempSinceLastReboot` プロパティを IoT Central に送信します。 `main` メソッドは、`{maxTempSinceLastReboot: 80.9}` のようなオブジェクトを使用して `createReportPropPatch` を呼び出します。

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main` メソッドは、次の 2 つのメソッドを使用して、IoT Central から受け取った書き込み可能なプロパティ (_target temperature_) の更新を処理します。 `propertyUpdateHandle` からの応答が、バージョンと状態コードを使用して構築されていることがわかります。

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

`main` メソッドは、次の 2 つのメソッドを使用して、`getMaxMinReport` コマンドの呼び出しを処理します。 `getMaxMinReportObject` メソッドからは、レポートが JSON オブジェクトとして生成されます。

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>接続情報の取得

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>コードの実行

サンプル アプリケーションを実行するには、コマンドライン環境を開き、*simple_thermostat.js* サンプル ファイルがある *azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

必要なパッケージをインストールします。

```cmd/sh
npm install
```

サンプルを実行します。

```cmd/sh
node simple_thermostat.js
```

次の出力は、デバイスが IoT Central に登録して接続するようすを示しています。 その後、`maxTempSinceLastReboot` プロパティを送信した後、テレメトリの送信が開始されます。

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
