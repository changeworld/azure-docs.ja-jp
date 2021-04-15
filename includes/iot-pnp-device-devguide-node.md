---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 0aa13d8d23f4f18004131a25f8eb42388d79834b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582767"
---
## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> モジュールと IoT Edge には、`Client` ではなく `ModuleClient` を使用します。

> [!TIP]
> デバイスでモデル ID を設定できるのは、このときだけです。デバイスを接続した後に更新することはできません。

## <a name="dps-payload"></a>DPS ペイロード

[Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) を使用するデバイスには、次の JSON ペイロードを使用して、プロビジョニング プロセス中に使用される `modelId` を含めることができます。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>テレメトリ、プロパティ、およびコマンドを実装する

[IoT プラグ アンド プレイ モデルのコンポーネントの概要](../articles/iot-pnp/concepts-modeling-guide.md)に関するページで説明されているように、デバイス ビルダーは、コンポーネントを使用してデバイスを記述するかどうかを決定する必要があります。 コンポーネントを使用する場合、デバイスはこのセクションで説明されている規則に従う必要があります。

### <a name="telemetry"></a>テレメトリ

既定のコンポーネントには、特別なプロパティは必要ありません。

入れ子になったコンポーネントを使用する場合、デバイスでは、メッセージ プロパティにコンポーネント名を設定する必要があります。

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>読み取り専用プロパティ

既定のコンポーネントからのプロパティの報告には、特別なコンストラクトは必要ありません。

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

入れ子になったコンポーネントを使用する場合、コンポーネントの名前内にプロパティを作成する必要があります。

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>書き込み可能なプロパティ

これらのプロパティは、デバイスから設定するか、ソリューションから更新することができます。 ソリューションからプロパティを更新すると、クライアントでは `Client` または `ModuleClient` でコールバックとして通知を受け取ります。 IoT プラグ アンド プレイ規則に従うために、デバイスからサービスに対して、プロパティが正常に受信されたことを通知する必要があります。

#### <a name="report-a-writable-property"></a>書き込み可能なプロパティを報告する

デバイスから書き込み可能なプロパティが報告された場合は、規則に定義されている `ack` 値を含める必要があります。

既定のコンポーネントから書き込み可能なプロパティを報告するには、次のようにします。

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

入れ子になったコンポーネントから書き込み可能なプロパティを報告するには、ツインにマーカーを含める必要があります。

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>必要なプロパティの更新をサブスクライブする

サービスでは、接続されたデバイスで通知をトリガーする目的のプロパティを更新できます。 この通知には、更新を識別するバージョン番号など、更新された目的のプロパティが含まれます。 デバイスでは、報告されたプロパティと同じ `ack` メッセージで応答する必要があります。

既定のコンポーネントでは、1 つのプロパティを参照し、受け取ったバージョンで報告される `ack` を作成します。

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
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
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
```

デバイス ツインでは、目的のセクションと報告されるセクションにプロパティが表示されます。

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

入れ子になったコンポーネントでは、コンポーネント名でラップされた目的のプロパティを受け取り、`ack` で報告されたプロパティを報告する必要があります。

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

コンポーネントのデバイス ツインには、次のように目的のセクションと報告されたセクションを示します。

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>コマンド

既定のコンポーネントは、サービスから呼び出されたときに、コマンド名を受け取ります。

入れ子になったコンポーネントは、コンポーネント名と `*` 区切り記号が先頭に付けられたコマンド名を受け取ります。

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>要求と応答のペイロード

コマンドでは、型を使用して、要求と応答のペイロードを定義します。 デバイスでは、受け取った入力パラメーターを逆シリアル化し、応答をシリアル化する必要があります。 ペイロードで定義された複合型を使用してコマンドを実装する例を次に示します。

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

次のコード スニペットは、デバイスでこのコマンド定義がどのように実装されるかを示しています。これには、シリアル化と逆シリアル化を有効にするために使用される型が含まれます。

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。
