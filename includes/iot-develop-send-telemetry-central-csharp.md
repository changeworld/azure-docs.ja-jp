---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/08/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 1a61f369d0987caac2cc821e37c1e5e1ed7cda40
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861114"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 最初に、デバイスをホストするための Azure IoT Central アプリケーションを作成します。 次に Azure IoT device SDK サンプルを使用して、温度コントローラーを作成し、それを IoT Central に安全に接続し、テレメトリを送信します。 温度コントローラーのサンプル アプリケーションはローカル コンピューター上で動作し、センサー データをシミュレートして IoT Central に送信します。

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Windows、Linux、Raspberry Pi で実行できます。 これは、次の OS およびデバイス バージョンでテストされています。

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi 3 Model B+ で実行されている Raspberry Pi OS バージョン 10 (Raspian)

次に示す前提条件を開発マシンにインストールします。

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- .NET Core SDK 3.1。 ランタイムだけではなく、.NET SDK も必ずインストールしてください。 マシンにインストールされている .NET SDK とランタイムのバージョンを確認するには、`dotnet --info` を実行します。

  - Windows と Linux (Raspberry Pi を除く) の場合は、手順に従って、お使いのプラットフォームに [.NET Core SDK 3.1 をインストール](/dotnet/core/install/)します。
  - Raspberry Pi の場合は、手順に従って、[SDK を手動でインストール](/dotnet/core/install/linux-scripted-manual#manual-install)する必要があります。 これは、Debian では、.NET SDK のパッケージ マネージャーのインストールが x64 アーキテクチャでのみサポートされているためです。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する
このセクションでは、ローカル環境を構成し、Azure IoT C# のサンプルをインストールして、温度コントローラーを作成するサンプルを実行します。

### <a name="configure-your-environment"></a>環境を構成する

1. Windows CMD、PowerShell、または Bash などのコンソールを開きます。

1. コンソールに適したコマンドを使用して、次の環境変数を設定します。 このデバイスでは、これらの値を使用して IoT Central に接続します。 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY`、`IOTHUB_DEVICE_DPS_DEVICE_ID` には、前に保存したデバイス接続の値を使用してください。

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Windows CMD では、変数の値を囲む引用符は入力しません。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK とサンプルをインストールする

1. [C# (.NET) 用 Microsoft Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp)をローカル コンピューターにクローンします。

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. sample ディレクトリに移動します。

    **Windows**

    ```console
    cd azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController
    ```

    **Linux または Raspberry Pi OS**

    ```console
    cd azure-iot-samples-csharp/iot-hub/Samples/device/PnpDeviceSamples/TemperatureController
    ```

1. Azure IoT C# SDK および必要な依存関係をインストールします。

    ```console
    dotnet restore
    ```

    このコマンドを実行すると、*TemperatureController.csproj* ファイルに指定されている適切な依存関係がインストールされます。

### <a name="run-the-code"></a>コードの実行

1. コンソールで、コード サンプルを実行します。 このサンプルでは、サーモスタット センサーを備えた温度コントローラーを作成します。

    ```console
    dotnet run
    ```

    デバイスを IoT Central アプリケーションに接続すると、そのアプリケーションで作成したデバイス インスタンスにデバイスが接続され、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。

    ```output
    [10/09/2021 00:29:18]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Press Control+C to quit the sample.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set up the device client.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Initializing via DPS
    [10/09/2021 00:29:38]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for 'reboot' command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Connection status change registered - status=Connected, reason=Connection_Ok.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for "getMaxMinReport" command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler to receive 'targetTemperature' updates.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component = 'deviceInformation', properties update is complete.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - { "serialNumber": "SR-123456" } is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat1", { "temperature": 23.7 } in °C.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 23.7 } in °C is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat2", { "temperature": 25.8 } in °C.
    ```