---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: cd7d4d3653dc775a949da229e4005d297f7810aa
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126101"
---
## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](../articles/iot-central/core/quick-deploy-iot-central.md)に関するページをご覧ください。 アプリケーションは、2020 年 7 月 14 日以降に作成されている必要があります。
* [Python](https://www.python.org/) バージョン 3.7 以降がインストールされた開発用マシン。 コマンドラインで `python --version` を実行して、お使いのバージョンを確認できます。 Python は、さまざまなオペレーティング システムで使用できます。 このチュートリアルの手順では、Windows コマンド プロンプトで **python** コマンドを実行していることを前提としています。
* サンプル コードが格納された [Microsoft Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) GitHub リポジトリのローカル コピー。 このリンク ([ZIP のダウンロード](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip)) を使用してリポジトリのコピーをダウンロードしてください。 次に、ローカル コンピューター上の適切な場所にファイルを解凍します。

## <a name="review-the-code"></a>コードの確認

先ほどダウンロードした Microsoft Azure IoT SDK for Python のコピーにある *azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py* ファイルをテキスト エディターで開きます。

このサンプルを実行して IoT Central に接続すると、Device Provisioning Service (DPS) を使用してデバイスが登録され、接続文字列が生成されます。 このサンプルでは、必要な DPS 接続情報がコマンドライン環境から取得されます。

`main` 関数は、次のことを行います。

* DPS を使用してデバイスをプロビジョニングします。 プロビジョニング情報には、モデル ID が含まれます。
* `Device_client` オブジェクトを作成し、接続が開かれる前に `dtmi:com:example:Thermostat;1` モデル ID を設定します。
* `maxTempSinceLastReboot` プロパティを IoT Central に送信します。
* `getMaxMinReport` コマンドのリスナーを作成します。
* 書き込み可能なプロパティの更新をリッスンするプロパティ リスナーを作成します。
* 10 秒ごとに温度テレメトリを送信するループを開始します。

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

`provision_device` 関数は、DPS を使用してデバイスをプロビジョニングし、IoT Central に登録します。 デバイスのモデル ID は、この関数によってプロビジョニング ペイロードに追加されます。

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener` 関数は、コマンドの要求を処理します。デバイスが `getMaxMinReport` コマンドを受け取ったときに `max_min_handler` 関数を実行し、`create_max_min_report_response` 関数を実行して応答を生成します。

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener` は、書き込み可能なプロパティ (`targetTemperature` など) の更新を処理し、JSON 応答を生成します。

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

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

`send_telemetry_from_thermostat` 関数は、テレメトリ メッセージ IoT Central に送信します。

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>接続情報の取得

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>コードの実行

サンプル アプリケーションを実行するには、コマンドライン環境を開き、*simple_thermostat.py* サンプル ファイルがある *azure-iot-sdk-python/azure-iot-device/samples/pnp* フォルダーに移動します。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

必要なパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

サンプルを実行します。

```cmd/sh
python simple_thermostat.py
```

次の出力は、デバイスが IoT Central に登録して接続するようすを示しています。 `maxTempSinceLastReboot` プロパティを送信した後、テレメトリの送信が開始されます。

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
