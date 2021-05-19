---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 9b1460819a496a141260aede32c519e2e2271c95
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776602"
---
## <a name="prerequisites"></a>前提条件
- [Visual Studio (Community、Professional、または Enterprise) 2019](https://visualstudio.microsoft.com/downloads/)。
- [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリのローカル コピー。 リポジトリのコピーをダウンロードして展開します: [ZIP をダウンロード](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)します。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、ローカル環境を構成し、シミュレートされた温度コントローラーを作成するサンプルを実行します。

Visual Studio でサンプル アプリケーションを実行するには:

1. Azure IoT Samples for C# を展開したフォルダーで、*azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* ソリューション ファイルを Visual Studio で開きます。 

1. **ソリューション エクスプローラー** で、**PnpDeviceSamples > TemperatureController** プロジェクト ファイルを選択して右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

1. **TemperatureController** プロジェクトを右クリックし、 **[プロパティ]** を選択します。 **[デバッグ]** タブを選択し、次の環境変数をプロジェクトに追加します。

    | Name | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | あらかじめ書き留めておいた ID スコープ値。 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | あらかじめ書き留めておいた、生成されたデバイス キーの値。 |

1. 更新された **TemperatureController** プロジェクト ファイルを保存します。

1. Ctrl キーを押しながら F5 キーを押してサンプルを実行します。

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
        [05/04/2021 11:53:50]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Press Control+C to quit the sample.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set up the device client.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Initializing via DPS
        [05/04/2021 11:53:56]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for 'reboot' command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Connection status change registered - status=Connected, reason=Connection_Ok.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for "getMaxMinReport" command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler to receive 'targetTemperature' updates.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component = 'deviceInformation', properties update is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - { "serialNumber": "SR-123456" } is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat1", { "temperature": 44.9 } in °C.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 44.9 } in °C is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat2", { "temperature": 40.8 } in °C.
    ```