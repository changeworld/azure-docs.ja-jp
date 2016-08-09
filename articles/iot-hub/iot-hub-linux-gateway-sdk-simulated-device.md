<properties
	pageTitle="Gateway SDK を使用したデバイスのシミュレート |Microsoft Azure"
	description="Linux で Azure IoT Hub Gateway SDK を使用してシミュレートされたデバイスからテレメトリを送信する方法を示す、Azure IoT Hub Gateway SDK のチュートリアル。"
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
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# IoT ゲートウェイ SDK (ベータ) – Linux を使用してシミュレートされたデバイスから D2C メッセージを送信する

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## サンプルのビルドと実行

作業を開始する前に、以下を行う必要があります。

- Linux で SDK を使用するための[開発環境を設定][lnk-setupdevbox]します。
- Azure サブスクリプションで [IoT Hub を作成][lnk-create-hub]します。このチュートリアルを実行するには、Hub の名前が必要です。Azure サブスクリプションがまだない場合は、[無料アカウント][lnk-free-trial]を取得できます。
- 2 つのデバイスを IoT Hub に追加し、デバイスの ID とデバイス キーをメモしておきます。[デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、前の手順で作成した IoT Hub にデバイスを追加し、キーを取得します。

サンプルをビルドするには:

1. シェルを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.sh** スクリプトを実行します。このスクリプトでは、**cmake** ユーティリティを使用して、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに **build** という名前のフォルダーを作成し、メイクファイルを生成します。スクリプトは、次にソリューションをビルドし、テストを実行します。

> [AZURE.NOTE]  **build.sh** スクリプトを実行するたびに **build** フォルダーが削除され、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに再作成されます。

サンプルを実行するには:

テキスト エディターで、**azure-iot-gateway-sdk** リポジトリのローカル コピーにある **samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_lin.json** ファイルを開きます。このファイルでは、サンプル ゲートウェイの各モジュールを構成します。

- **IoTHub** モジュールは、IoT Hub に接続します。データを IoT Hub に送信するようにモジュールを構成する必要があります。具体的には、**IoTHubName** 値を実際の IoT Hub の名前に設定し、**IoTHubSuffix** の値を **azure-devices.net** に設定します。
- **mapping** モジュールは、シミュレートされたデバイスの MAC アドレスを IoT Hub のデバイス ID にマップします。**deviceId** 値が IoT Hub に追加した 2 つのデバイスの ID と一致し、**deviceKey** 値に 2 つのデバイスのキーが含まれていることを確認します。
- **BLE1** モジュールと **BLE2** モジュールは、シミュレートされたデバイスです。これらのモジュールの MAC アドレスが **mapping** モジュールの MAC アドレスとどのように一致しているかに注意してください。
- **Logger** モジュールは、ゲートウェイのアクティビティをファイルに記録します。
- 次に示す **module path** の値は、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルートからサンプルを実行することを前提としています。

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothubhttp/libiothubhttp_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentitymap_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}

```

構成ファイルに加えた変更を保存します。

サンプルを実行するには:

1. シェル内で **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
2. 次のコマンドを実行します。

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```

3. [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、IoT Hub がゲートウェイから受信するメッセージを監視できます。

## 次のステップ

Gateway SDK に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

- [Gateway SDK で実際のデバイスから device-to-cloud メッセージを送信][lnk-physical-device]
- [ゲートウェイ デバイスの管理][lnk-manage-devices]
- [Azure IoT Gateway SDK][lnk-gateway-sdk]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]
- [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md
[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->