---
title: "Azure IoT Edge を使用してデバイスをシミュレートする (Windows) | Microsoft Docs"
description: "Windows 上で Azure IoT Edge を使用して、Azure IoT Edge ゲートウェイを介して IoT Hub にテレメトリを送信するシミュレートされたデバイスを作成する方法。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 21cc2f3575a1e93ffd3b245371069f1498f6b63b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Azure IoT Edge を使用して、シミュレートされたデバイスに D2C メッセージを送信する (Windows)
[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行
作業を開始する前に、以下を行う必要があります。

* Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]します。
* Azure サブスクリプションで [IoT ハブを作成][lnk-create-hub]します。このチュートリアルを実行するには、ハブの名前が必要です。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* 2 つのデバイスを IoT Hub に追加し、デバイスの ID とデバイス キーをメモしておきます。 [デバイス エクスプローラー][lnk-device-explorer]または [iothub-explorer][lnk-iothub-explorer] ツールを使用して、前の手順で作成した IoT Hub にデバイスを追加し、キーを取得します。

サンプルをビルドするには、次の手順に従います。

1. **開発者コマンド プロンプト for VS 2015** または **開発者コマンド プロンプト for VS 2017** コマンド プロンプトを開きます。
2. **iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドします。 Visual Studio ソリューションは、**iot-edge** レポジトリのローカル コピーの **build** フォルダーにあります。 スクリプトで追加のパラメーターを指定すると、ユニット テストとエンド ツー エンド テストをビルドして実行できます。 パラメーターはそれぞれ、**--run-unittests** と **--run-e2e-tests** です。

サンプルを実行するには:

テキスト エディターで、**iot-edge** レポジトリのローカル コピーの **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** ファイルを開きます。 このファイルは、サンプル ゲートウェイの IoT Edge モジュールを構成します。

* **IoTHub** モジュールは、IoT Hub に接続します。 データを IoT Hub に送信するようにモジュールを構成します。 具体的には、**IoTHubName** 値を実際の IoT Hub の名前に設定し、**IoTHubSuffix** 値を **azure-devices.net** に設定します。 **Transport** の値を "HTTP"、"AMQP"、"MQTT" のいずれかに設定します。 現在、すべてのデバイス メッセージで 1 つの TCP 接続を共有するのは "HTTP" のみです。 値を "AMQP" または "MQTT" に設定すると、ゲートウェイは各デバイスで IoT Hub に対する TCP 接続を別個に維持します。
* **mapping** モジュールは、シミュレートされたデバイスの MAC アドレスを IoT Hub のデバイス ID にマップします。 **deviceId** 値が IoT Hub に追加した 2 つのデバイスの ID と一致し、**deviceKey** 値に 2 つのデバイスのキーが含まれていることを確認します。
* **BLE1** モジュールと **BLE2** モジュールは、シミュレートされたデバイスです。 モジュールの MAC アドレスが **mapping** モジュールの MAC アドレスとどのように一致しているかに注意してください。
* **Logger** モジュールは、ゲートウェイのアクティビティをファイルに記録します。
* 次の例の **module path** の値は、IoT Edge レポジトリを **C:** ドライブのルートに複製していることを前提としています。 リポジトリを別の場所にダウンロードした場合は、 **module path** の値を適宜調整する必要があります。
* JSON ファイルの下部にある **links** 配列は、**BLE1** モジュールと **BLE2** モジュールを **mapping** モジュールに、**mapping** モジュールを **IoTHub** モジュールに接続します。 また、すべてのメッセージが **Logger** モジュールによってログに記録されます。

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

構成ファイルに加えた変更を保存します。

サンプルを実行するには:

1. コマンド プロンプトで、**iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。
2. 次のコマンドを実行します。
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. [デバイス エクスプローラー][lnk-device-explorer]または [iothub-explorer][lnk-iothub-explorer] ツールを使用して、IoT Hub がゲートウェイから受信するメッセージを監視できます。

## <a name="next-steps"></a>次のステップ
IoT Edge に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

* [IoT Edge を使用して物理デバイスから D2C メッセージを送信する][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-iot-edge]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-iot-edge-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md
