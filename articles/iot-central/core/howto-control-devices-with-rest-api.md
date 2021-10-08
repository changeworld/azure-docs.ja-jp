---
title: Azure IoT Central での REST API を使用したデバイスの管理
description: IoT Central REST API を使用してアプリケーションでデバイスを制御する方法
author: dominicbetts
ms.author: dobett
ms.date: 08/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 052dc4d0d275c120d7bb9379937fadfe0e005bdd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656723"
---
# <a name="how-to-use-the-iot-central-rest-api-to-control-devices"></a>IoT Central REST API を使用してデバイスを制御する方法

IoT Central REST API を使用して、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 REST API を使用して、IoT Central アプリケーションでデバイスを制御できます。 REST API を使用すると、次を行うことができます。

- デバイスから最後の既知のテレメトリの値を読み取る。
- デバイスからプロパティ値を読み取る。
- デバイスに書き込み可能なプロパティを設定する。
- デバイスでコマンドを呼び出す。

この記事では、`/devices/{device_id}` API を使用して個々のデバイスを制御する方法について説明します。 ジョブを使用してデバイスを一括管理することもできます。

デバイスは、サポートしているプロパティ、テレメトリ、コマンドを、_コンポーネント_ と _モジュール_ にグループ化できます。

すべての IoT Central REST API 呼び出しに承認ヘッダーが必要です。 詳細については、「[IoT Central REST API 呼び出しを認証および承認する方法](howto-authorize-rest-api.md)」を参照してください。

IoT Central REST API のリファレンス ドキュメントについては、「[Azure IoT Central REST API リファレンス](/rest/api/iotcentral/)」をご覧ください。

> [!TIP]
> [プレビュー API](/rest/api/iotcentral/1.1-previewdataplane/devices) には、新しい[組織機能](howto-create-organizations.md)のサポートが含まれています。

## <a name="components-and-modules"></a>コンポーネントとモジュール

コンポーネントを使用して、デバイスの機能をグループ化して再利用できます。 コンポーネントとデバイス モデルについて詳しくは、「[IoT プラグ アンド プレイ モデリング ガイド](../../iot-develop/concepts-modeling-guide.md)」を参照してください。

すべてのデバイス テンプレートでコンポーネントが使用されるわけではありません。 次のスクリーンショットは、単純な [サーモスタット](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-2.json)のデバイス テンプレートを示しています。ここでは、すべての機能が **ルート コンポーネント** と呼ばれる 1 つのインターフェイスで定義されています。

:::image type="content" source="media/howto-control-devices-with-rest-api/thermostat-device.png" alt-text="コンポーネントを使用しない単純なサーモスタット デバイスを示すスクリーンショット。":::

次のスクリーンショットは、コンポーネントを使用する[温度コントローラー](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json) デバイスのテンプレートを示しています。 この温度コントローラーには、2 つのサーモスタット コンポーネントと 1 つのデバイス情報コンポーネントがあります。

:::image type="content" source="media/howto-control-devices-with-rest-api/temperature-controller-device.png" alt-text="2 つのサーモスタット コンポーネントと 1 つのデバイス情報コンポーネントを備えた温度コントローラー デバイスを示すスクリーンショット。":::

IoT Central では、モジュールは接続された IoT Edge デバイス上で実行されている IoT Edge モジュールを指します。 モジュールには、コンポーネントを使用しないサーモスタットのような単純なモデルを使用できます。 モジュールでは、コンポーネントを使用して、より複雑な機能セットを編成することもできます。 次のスクリーンショットは、モジュールを使用するデバイス テンプレートの例を示しています。 この環境センサー デバイスには、`SimulatedTemperatureSensor` という名前のモジュールと、`management` という名前の継承インターフェイスがあります。

:::image type="content" source="media/howto-control-devices-with-rest-api/environmental-sensor-device.png" alt-text="モジュールを含む環境センサー デバイスを示すスクリーンショット。":::

## <a name="get-a-device-component"></a>デバイス コンポーネントの取得

`temperature-controller-01` という名前のデバイスからコンポーネントを取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components?api-version=1.0
```

この要求に対する応答は、次の例のようになります。 配列 `value` には、各デバイス コンポーネントの詳細が含まれています。

```json
{
  "value": [
    {
      "@type": "Component",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type": "Component",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

## <a name="get-a-device-module"></a>デバイス モジュールの取得

`environmental-sensor-01` という名前の接続された IoT Edge デバイス上で実行されているモジュールのリストを取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

この要求に対する応答は、次の例のようになります。 モジュールの配列には、組み込まれている `$edgeAgent` および `$edgeHub` モジュールではなく、IoT Edge デバイス上で実行されているカスタム モジュールのみが含まれています。

```json
{
  "value": [
    {
      "@type": [
        "Relationship",
        "EdgeModule"
      ],
      "name": "SimulatedTemperatureSensor",
      "displayName": "SimulatedTemperatureSensor"
    }
  ]
}
```

`SimulatedTemperatureSensor` という名前のモジュール上のコンポーネントのリストを取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

## <a name="read-telemetry"></a>テレメトリの読み取り

コンポーネントを使用しないデバイスから最後の既知のテレメトリ値を取得するには、次の要求を使用します。 この例では、デバイス名として `thermostat-01`、テレメトリ名として `temperature` を使用しています。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/telemetry/temperature?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "timestamp": "2021-03-24T12:33:15.223Z",
  "value": 40.10993804456927
}
```

コンポーネントを使用するデバイスから最後の既知のテレメトリ値を取得するには、次の要求を使用します。 この例では、デバイス名として `temperature-controller-01`、コンポーネント名として `thermostat2`、テレメトリ名として `temperature` を使用しています。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/telemetry/temperature?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "timestamp": "2021-03-24T12:43:44.968Z",
  "value": 70.29168040339141
}
```

デバイスが IoT Edge デバイスの場合、モジュールから最後の既知のテレメトリ値を取得するには、次の要求を使用します。 この例では、`SimulatedTemperatureSensor` という名前のモジュールと `ambient` という名前のテレメトリを使用する `environmental-sensor-01` という名前のデバイスを使用します。 `ambient` というテレメトリの種類には、温度と湿度の値があります。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/telemetry/ambient?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "timestamp": "2021-03-25T15:44:34.955Z",
  "value": {
    "temperature": 21.18032378129676,
    "humidity": 25
  }
}
```

> [!TIP]
> モジュール内のコンポーネントからテレメトリにアクセスするには、`/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/telemetry/{telemetryName}` を使用します。

## <a name="read-properties"></a>プロパティの読み取り

コンポーネントを使用しないデバイスからプロパティ値を取得するには、次の要求を使用します。 この例では、デバイス名として `thermostat-01` を使用しています。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

この要求に対する応答は、次の例のようになります。 これは、デバイスが 1 つのプロパティ値を報告していることを示しています。

```json
{
  "maxTempSinceLastReboot": 93.95907131817654,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T12:47:46.7571438Z"
    }
  }
}
```

すべてのコンポーネントからプロパティ値を取得するには、次の要求を使用します。 この例では、デバイス名として `temperature-controller-01` を使用しています。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/properties?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "serialNumber": "Explicabo animi nihil qui facere sit explicabo nisi.",
  "$metadata": {
    "serialNumber": {
      "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
    }
  },
  "thermostat1": {
    "maxTempSinceLastReboot": 79.7290121339184,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "thermostat2": {
    "maxTempSinceLastReboot": 54.214860556320424,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "deviceInformation": {
    "manufacturer": "Eveniet culpa sed sit omnis.",
    "$metadata": {
      "manufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "model": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "swVersion": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "osName": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorArchitecture": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorManufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalStorage": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalMemory": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    },
    "model": "Necessitatibus id ab dolores vel eligendi fuga.",
    "swVersion": "Ut minus ipsum ut omnis est asperiores harum.",
    "osName": "Atque sit omnis eum sapiente eum tenetur est dolor.",
    "processorArchitecture": "Ratione enim dolor iste iure.",
    "processorManufacturer": "Aliquam eligendi sit ipsa.",
    "totalStorage": 36.02825898541592,
    "totalMemory": 55.442695395750505
  }
}
```

個々のコンポーネントからプロパティ値を取得するには、次の要求を使用します。 この例では、デバイス名として `temperature-controller-01`、コンポーネント名として `thermostat2` を使用しています。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "maxTempSinceLastReboot": 24.445128131004935,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T14:03:53.787491Z"
    }
  }
}
```

デバイスが IoT Edge デバイスの場合、モジュールからプロパティ値を取得するには、次の要求を使用します。 この例では、`SimulatedTemperatureSensor` という名前のモジュールを使用する `environmental-sensor-01` という名前のデバイスを使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "$metadata": {
    "SendData": {
      "desiredValue": true,
      "desiredVersion": 1
    },
    "SendInterval": {
      "desiredValue": 10,
      "desiredVersion": 1
    }
  }
}
```

> [!TIP]
> モジュール内のコンポーネントからプロパティにアクセスするには、`/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties` を使用します。

## <a name="write-properties"></a>プロパティの書き込み

一部のプロパティは書き込み可能です。 たとえば、サーモスタット モデルで、`targetTemperature` プロパティは書き込み可能なプロパティです。

コンポーネントを使用しないデバイスに個々のプロパティ値を書き込むには、次の要求を使用します。 この例では、デバイス名として `thermostat-01` を使用しています。

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

要求の本文は、次の例のようになります。

```json
{
  "targetTemperature": 65.5
}
```

この要求に対する応答は、次の例のようになります。

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> デバイス上のすべてのプロパティを更新するには、`PATCH` ではなく、`PUT` を使用します。

コンポーネントを使用するデバイスに個々のプロパティ値を書き込むには、次の要求を使用します。 この例では、デバイス名として `temperature-controller-01`、コンポーネント名として `thermostat2` を使用しています。

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

要求の本文は、次の例のようになります。

```json
{
  "targetTemperature": 65.5
}
```

この要求に対する応答は、次の例のようになります。

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> コンポーネント上のすべてのプロパティを更新するには、`PATCH` ではなく、`PUT` を使用します。

デバイスが IoT Edge デバイスの場合、モジュールに個々のプロパティ値を書き込むには、次の要求を使用します。 この例では、`environmental-sensor-01` という名前のデバイス、`SimulatedTemperatureSensor` という名前のモジュール、`SendInterval` という名前のプロパティを使用します。

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

要求の本文は、次の例のようになります。

```json
{
  "SendInterval": 20
}
```

この要求に対する応答は、次の例のようになります。

```json
{
  "$metadata": {
    "SendInterval": {
      "desiredValue": 20
    }
  }
}
```

> [!TIP]
> モジュール上のすべてのプロパティを更新するには、`PATCH` ではなく、`PUT` を使用します。

### <a name="update-module-properties"></a>モジュールのプロパティを更新する

IoT Edge デバイスを使用している場合、モジュールからプロパティ値を取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/properties?api-version=1.0
```

IoT Edge デバイスを使用している場合、モジュールのコンポーネントからプロパティ値を取得するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties?api-version=1.0
```

## <a name="call-commands"></a>コマンドの呼び出し

REST API を使用してデバイスのコマンドを呼び出し、デバイスの履歴を取得できます。

コンポーネントを使用しないデバイスでコマンドを呼び出すには、次の要求を使用します。 この例では、デバイス名として `thermostat-01`、コマンド名として `getMaxMinReport` を使用しています。

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

要求の本文は、次の例のようになります。

```json
{
  "since": "2021-03-24T12:55:20.789Z"
}
```

この要求に対する応答は、次の例のようになります。

```json
{
  "response": {
    "maxTemp": 21.002000799562367,
    "minTemp": 73.09674605264892,
    "avgTemp": 59.54553991653756,
    "startTime": "2022-02-28T15:02:56.789Z",
    "endTime": "2021-05-05T03:50:56.412Z"
  },
  "responseCode": 200
}
```

このコマンドの履歴を表示するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

この要求に対する応答は、次の例のようになります。

```json
{
  "value": [
    {
      "response": {
        "maxTemp": 71.43744908819954,
        "minTemp": 51.29986610160005,
        "avgTemp": 39.577384387771744,
        "startTime": "2021-06-20T00:38:17.620Z",
        "endTime": "2022-01-07T22:30:41.104Z"
      },
      "responseCode": 200
    }
  ]
}
```

コンポーネントを使用するデバイスでコマンドを呼び出すには、次の要求を使用します。 この例では、デバイス名として `temperature-controller-01`、コンポーネント名として `thermostat2`、コマンド名として `getMaxMinReport` を使用しています。

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

要求ペイロードと応答の形式は、コンポーネントを使用しないデバイスの場合と同じです。

このコマンドの履歴を表示するには、次の要求を使用します。

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

> [!TIP]
> モジュール内のコンポーネントのコマンドを呼び出すには、`/devices/{deviceId}/modules/{moduleName}/components/{componentName}/commands/{commandName}` を使用します。

## <a name="next-steps"></a>次のステップ

これで REST API を使用してデバイスを制御する方法を学習したので、推奨される次のステップは [IoT Central REST API を使用してジョブを作成および管理する方法](howto-manage-jobs-with-rest-api.md)を学習することです。
