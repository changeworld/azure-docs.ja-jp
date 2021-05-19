---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 984849cddceb2476bd9d0870da47a33770c4a36a
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776865"
---
## <a name="prerequisites"></a>前提条件
- [Python](https://www.python.org/downloads/) バージョン 3.7 以降。 Python のバージョンを確認するには、`python --version` を実行します。
- [Git](https://git-scm.com/downloads).
- このクイックスタートは、Linux または Windows で実行できます。 シェル コマンドでは標準の Linux パス区切り記号 `/` を使用します。 Windows を使用する場合は、これらの区切り記号を Windows パス区切り記号 `\` に置き換えてください。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、ローカル環境を構成し、Azure IoT Python device SDK をインストールし、シミュレートされた温度コントローラーを作成するサンプルを実行します。

### <a name="configure-your-environment"></a>環境を構成する

1. Windows CMD、PowerShell、または Bash を使用してコンソールを開きます。

1. コンソールに適したコマンドを使用して、次の環境変数を設定します。 シミュレートされたデバイスは、これらの値を使用して IoT Central に接続します。 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY`、`IOTHUB_DEVICE_DPS_DEVICE_ID` には、前に保存したデバイス接続の値を使用してください。

    **Windows CMD**

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

    **Bash (Linux または Windows)**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK とサンプルをインストールする

1. Azure IoT Python device SDK を、お使いのローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. samples ディレクトリに移動します。
    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Azure IoT Python SDK をインストールします。
    ```console
    pip3 install azure-iot-device
    ```

### <a name="run-the-code"></a>コードの実行

1. コンソールで、SDK の次のコード サンプルを実行します。 このサンプルは、サーモスタット センサーを備えたシミュレートされた温度コントローラーを作成します。
    ```console
    python temp_controller_with_thermostats.py
    ```

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、アプリケーション内で作成されたデバイス インスタンスに接続して、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
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
    {"temperature": 33}
    ```