<properties
    pageTitle="Gateway SDK を使用したデバイスのシミュレート |Microsoft Azure"
    description="Windows で Azure IoT Hub Gateway SDK を使用してシミュレートされたデバイスからテレメトリを送信する方法を示す、Azure IoT Hub Gateway SDK のチュートリアル。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-beta-send-devicetocloud-messages-with-a-simulated-device-using-windows"></a>IoT Gateway SDK (ベータ) - Windows を使用してシミュレートされたデバイスでデバイスからクラウドへのメッセージを送信する

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

作業を開始する前に、以下を行う必要があります。

- Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]します。
- Azure サブスクリプションで [IoT Hub を作成][lnk-create-hub]します。このチュートリアルを実行するには、Hub の名前が必要です。 Azure サブスクリプションがまだない場合は、[無料アカウント][lnk-free-trial]を取得できます。
- 2 つのデバイスを IoT Hub に追加し、デバイスの ID とデバイス キーをメモしておきます。 [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、前の手順で作成した IoT Hub にデバイスを追加し、キーを取得します。

サンプルをビルドするには、次の手順に従います。

1. **開発者コマンド プロンプト for VS2015** コマンド プロンプトを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドして、テストを実行します。 Visual Studio ソリューションは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **build** フォルダーにあります。

サンプルを実行するには:

テキスト エディターで、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** ファイルを開きます。 このファイルでは、サンプル ゲートウェイの各モジュールを構成します。

- **IoTHub** モジュールは、IoT Hub に接続します。 データを IoT Hub に送信するようにモジュールを構成する必要があります。 具体的には、**IoTHubName** 値を実際の IoT Hub の名前に設定し、**IoTHubSuffix** 値を **azure-devices.net** に設定します。 **Transport** の値を "HTTP"、"AMQP"、または "MQTT" のいずれかに設定します。 現在、すべてのデバイス メッセージで 1 つの TCP 接続を共有するのは "HTTP" のみです。 値を "AMQP" または "MQTT" に設定すると、ゲートウェイは各デバイスで IoT Hub に対する TCP 接続を別個に維持します。
- **mapping** モジュールは、シミュレートされたデバイスの MAC アドレスを IoT Hub のデバイス ID にマップします。 **deviceId** 値が IoT Hub に追加した 2 つのデバイスの ID と一致し、**deviceKey** 値に 2 つのデバイスのキーが含まれていることを確認します。
- **BLE1** モジュールと **BLE2** モジュールは、シミュレートされたデバイスです。 これらのモジュールの MAC アドレスが **mapping** モジュールの MAC アドレスとどのように一致しているかに注意してください。
- **Logger** モジュールは、ゲートウェイのアクティビティをファイルに記録します。
- 次に示す **module path** の値は、Gateway SDK リポジトリを **C:** ドライブのルートに複製していることを前提としています。 リポジトリを別の場所にダウンロードした場合は、 **module path** の値を適宜調整する必要があります。
- JSON ファイルの下部にある **links** 配列は、**BLE1** モジュールと **BLE2** モジュールを **mapping** モジュールに、**mapping** モジュールを **IoTHub** モジュールに接続します。 また、すべてのメッセージが **Logger** モジュールによってログに記録されます。

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

構成ファイルに加えた変更を保存します。

サンプルを実行するには:

1. コマンド プロンプトで、 **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
2. 次のコマンドを実行します。
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、IoT Hub がゲートウェイから受信するメッセージを監視できます。


## <a name="next-steps"></a>次のステップ

Gateway SDK に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

- [Gateway SDK で実際のデバイスから D2C メッセージを送信][lnk-physical-device]
- [Azure IoT Gateway SDK][lnk-gateway-sdk]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!---HONumber=Oct16_HO2-->


