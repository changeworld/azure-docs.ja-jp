---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f4eb312aff200389f59a3e342305b8eda98f213e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582780"
---
## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> モジュールと IoT Edge には、`IoTHubDeviceClient` ではなく `IoTHubModuleClient` を使用します。

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

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>読み取り専用プロパティ

既定のコンポーネントからのプロパティの報告には、特別なコンストラクトは必要ありません。

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

入れ子になったコンポーネントを使用する場合、次のコンポーネントの名前内にプロパティを作成する必要があります。

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

これらのプロパティは、デバイスから設定するか、ソリューションから更新することができます。 ソリューションからプロパティを更新すると、クライアントでは `IoTHubDeviceClient` または `IoTHubModuleClient` でコールバックとして通知を受け取ります。 IoT プラグ アンド プレイ規則に従うために、デバイスからサービスに対して、プロパティが正常に受信されたことを通知する必要があります。

#### <a name="report-a-writable-property"></a>書き込み可能なプロパティを報告する

デバイスから書き込み可能なプロパティが報告された場合は、規則に定義されている `ack` 値を含める必要があります。

既定のコンポーネントから書き込み可能なプロパティを報告するには、次のようにします。

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
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

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
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

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。
