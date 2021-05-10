---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: d878c7abf025b5c66790a96f9f921f669dcdf1ef
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491117"
---
## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](../articles/iot-central/core/quick-deploy-iot-central.md)に関するページをご覧ください。 アプリケーションは、2020 年 7 月 14 日以降に作成されている必要があります。
* [Python](https://www.python.org/) バージョン 3.7 以降がインストールされた開発用マシン。 コマンドラインで `python --version` を実行して、お使いのバージョンを確認できます。 Python は、さまざまなオペレーティング システムで使用できます。 このチュートリアルの手順では、Windows コマンド プロンプトで **python** コマンドを実行していることを前提としています。
* サンプル コードが格納された [Microsoft Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) GitHub リポジトリのローカル コピー。 このリンク ([Download ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip)) を使用してリポジトリのコピーをダウンロードしてください。 次に、ローカル コンピューター上の適切な場所にファイルを解凍します。

## <a name="review-the-code"></a>コードの確認

先ほどダウンロードした Microsoft Azure IoT SDK for Python のコピーにある *azure-iot-sdk-python/azure-iot-device/samples/pnp/temp_controller_with_thermostats.py* ファイルをテキスト エディターで開きます。

このサンプルを実行して IoT Central に接続すると、Device Provisioning Service (DPS) を使用してデバイスが登録され、接続文字列が生成されます。 このサンプルでは、必要な DPS 接続情報がコマンドライン環境から取得されます。

`main` 関数は、次のことを行います。

* DPS を使用してデバイスをプロビジョニングします。 プロビジョニング情報には、モデル ID が含まれます。 IoT Central では、モデル ID を使用して、このデバイスのデバイス テンプレートを識別または生成します。 詳細については、「[デバイス テンプレートへのデバイスの関連付け](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)」をご覧ください。
* `Device_client` オブジェクトを作成し、接続が開かれる前に `dtmi:com:example:TemperatureController;2` モデル ID を設定します。
* 初期プロパティ値を IoT Central に送信します。 これは `pnp_helper` を使用して修正プログラムを作成します。
* `getMaxMinReport` および `reboot` コマンドのリスナーを作成します。 各サーモスタット コンポーネントには、独自の `getMaxMinReport` コマンドがあります。
* 書き込み可能なプロパティの更新をリッスンするプロパティ リスナーを作成します。
* 2 つのサーモスタット コンポーネントからは温度テレメトリを、既定のコンポーネントからはワーキング セット テレメトリを 8 秒ごとに送信するループを開始します。

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
            print("Device was assigned")
            print(registration_result.registration_state.assigned_hub)
            print(registration_result.registration_state.device_id)
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

    ################################################
    # Update readable properties from various components

    properties_root = pnp_helper.create_reported_properties(serialNumber=serial_number)
    properties_thermostat1 = pnp_helper.create_reported_properties(
        thermostat_1_component_name, maxTempSinceLastReboot=98.34
    )
    properties_thermostat2 = pnp_helper.create_reported_properties(
        thermostat_2_component_name, maxTempSinceLastReboot=48.92
    )
    properties_device_info = pnp_helper.create_reported_properties(
        device_information_component_name,
        swVersion="5.5",
        manufacturer="Contoso Device Corporation",
        model="Contoso 4762B-turbo",
        osName="Mac Os",
        processorArchitecture="x86-64",
        processorManufacturer="Intel",
        totalStorage=1024,
        totalMemory=32,
    )

    property_updates = asyncio.gather(
        device_client.patch_twin_reported_properties(properties_root),
        device_client.patch_twin_reported_properties(properties_thermostat1),
        device_client.patch_twin_reported_properties(properties_thermostat2),
        device_client.patch_twin_reported_properties(properties_device_info),
    )

    ################################################
    # Get all the listeners running
    print("Listening for command requests and property updates")

    global THERMOSTAT_1
    global THERMOSTAT_2
    THERMOSTAT_1 = Thermostat(thermostat_1_component_name, 10)
    THERMOSTAT_2 = Thermostat(thermostat_2_component_name, 10)

    listeners = asyncio.gather(
        execute_command_listener(
            device_client, method_name="reboot", user_command_handler=reboot_handler
        ),
        execute_command_listener(
            device_client,
            thermostat_1_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_command_listener(
            device_client,
            thermostat_2_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    ################################################
    # Function to send telemetry every 8 seconds

    async def send_telemetry():
        print("Sending telemetry from various components")

        while True:
            curr_temp_ext = random.randrange(10, 50)
            THERMOSTAT_1.record(curr_temp_ext)

            temperature_msg1 = {"temperature": curr_temp_ext}
            await send_telemetry_from_temp_controller(
                device_client, temperature_msg1, thermostat_1_component_name
            )

            curr_temp_int = random.randrange(10, 50)  # Current temperature in Celsius
            THERMOSTAT_2.record(curr_temp_int)

            temperature_msg2 = {"temperature": curr_temp_int}

            await send_telemetry_from_temp_controller(
                device_client, temperature_msg2, thermostat_2_component_name
            )

            workingset_msg3 = {"workingSet": random.randrange(1, 100)}
            await send_telemetry_from_temp_controller(device_client, workingset_msg3)

    send_telemetry_task = asyncio.ensure_future(send_telemetry())

    # ...
```

`provision_device` 関数は、DPS を使用してデバイスをプロビジョニングし、IoT Central に登録します。 この関数により、デバイスのモデル ID ([デバイスをデバイス テンプレートに関連付ける](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)ために IoT Central が使用) がプロビジョニング ペイロードに追加されます。

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

`execute_command_listener` 関数はコマンド要求を処理して、デバイスがサーモスタット コンポーネントの `getMaxMinReport` コマンドを受信したときに `max_min_handler` 関数を実行し、デバイスが `reboot` コマンドを受信したときに `reboot_handler` 関数を実行します。 これは、`pnp_helper` モジュールを使用して応答を作成します。

```python
async def execute_command_listener(
    device_client,
    component_name=None,
    method_name=None,
    user_command_handler=None,
    create_user_response_handler=None,
):
    while True:
        if component_name and method_name:
            command_name = component_name + "*" + method_name
        elif method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        values = command_request.payload
        print(values)

        if user_command_handler:
            await user_command_handler(values)
        else:
            print("No handler provided to execute")

        (response_status, response_payload) = pnp_helper.create_response_payload_with_status(
            command_request, method_name, create_user_response=create_user_response_handler
        )

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener` は、サーモスタット コンポーネントの書き込み可能なプロパティ (`targetTemperature` など) の更新を処理し、JSON 応答を生成します。 これは、`pnp_helper` モジュールを使用して応答を作成します。

```python
async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        print(patch)
        properties_dict = pnp_helper.create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

`send_telemetry_from_temp_controller` 関数は、サーモスタット コンポーネントから IoT Central にテレメトリ メッセージを送信します。 これは、`pnp_helper` モジュールを使用してメッセージを作成します。

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = pnp_helper.create_telemetry(telemetry_msg, component_name)
    await device_client.send_message(msg)
    print("Sent message")
    print(msg)
    await asyncio.sleep(5)
```

## <a name="get-connection-information"></a>接続情報の取得

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>コードの実行

サンプル アプリケーションを実行するには、コマンドライン環境を開き、*temp_controller_with_thermostats.py* サンプル ファイルがある *azure-iot-sdk-python/azure-iot-device/samples/pnp* フォルダーに移動します。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

必要なパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

サンプルを実行します。

```cmd/sh
python temp_controller_with_thermostats.py
```

次の出力は、デバイスが IoT Central に登録して接続するようすを示しています。 サンプルは、2 つのサーモスタット コンポーネントから `maxTempSinceLastReboot` プロパティを送信した後、テレメトリの送信を開始します。

```cmd/sh
Device was assigned
iotc-60a.....azure-devices.net
sample-device-01
Updating pnp properties for root interface
{'serialNumber': 'alohomora'}
Updating pnp properties for thermostat1
{'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
Updating pnp properties for thermostat2
{'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
Updating pnp properties for deviceInformation
{'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
Listening for command requests and property updates
Press Q to quit
Sending telemetry from various components
Sent message
{"temperature": 27}
Sent message
{"temperature": 17}
Sent message
{"workingSet": 13}
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。

```cmd/sh
{'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
the data in the desired properties patch was: {'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
Values received are :-
{'targetTemperature': 67, '__t': 'c'}
Sent message

...

Command request received with payload
2021-03-31T05:00:00.000Z
Will return the max, min and average temperature from the specified time 2021-03-31T05:00:00.000Z to the current time
Done generating
{"avgTemp": 4.0, "endTime": "2021-03-31T12:29:48.322427", "maxTemp": 18, "minTemp": null, "startTime": "2021-03-31T12:28:28.322381"}
```
