---
title: Azure IoT Central にデバイス テレメトリを送信するクイックスタート (Python)
description: このクイックスタートでは、Python 用 Azure IoT Hub デバイス SDK を使用して、デバイスから IoT Central にテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 04/27/2021
ms.openlocfilehash: 2464d9d4e6a945620bdbf80728aa3e7df0e2776e
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226700"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>クイックスタート: デバイスから Azure IoT Central にテレメトリを送信する (Python)

**適用対象**: [デバイス アプリケーション開発者](about-iot-develop.md#device-application-development)<br>
**所要時間**: 12 分

このクイックスタートでは、基本的な IoT アプリケーション開発のワークフローについて説明します。 まず、Azure IoT Central でデバイスを管理するためのクラウド アプリケーションを作成します。 次に、Azure IoT Python SDK を使用して、シミュレートされたサーモスタット デバイスの作成、IoT Central への接続、テレメトリの送信を行います。

## <a name="prerequisites"></a>必須コンポーネント
- [Python 3.7](https://www.python.org/downloads/) 以降。 Python のバージョンを確認するには、`python --version` を実行します。 

[!INCLUDE [iot-develop-create-central-app-with-device](../../includes/iot-develop-create-central-app-with-device.md)]

## <a name="configure-a-simulated-device"></a>シミュレートされたデバイスを構成する
このセクションでは、Python SDK サンプルを使用して、シミュレートされたサーモスタット デバイスを構成します。

1. Windows CMD、PowerShell、Bash (Windows 用または Linux 用) のいずれかを使用してターミナルを開きます。 ターミナルを使用して Python SDK をインストールし、環境変数を更新して、Python コード サンプルを実行します。

1. [Azure IoT Python SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)をローカル コンピューターにコピーします。

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

1. シミュレートされたデバイスを IoT Central に接続できるよう、次の各環境変数を設定します。 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY`、`IOTHUB_DEVICE_DPS_DEVICE_ID` には、保存したデバイス接続の値を使用してください。

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

## <a name="send-telemetry"></a>テレメトリを送信する
システムの構成が済んだら、コードを実行する準備は完了です。 このコードでは、シミュレートされたサーモスタットを作成し、IoT Central アプリケーションとデバイス インスタンスに接続して、テレメトリを送信します。

1. ご利用のターミナルで、次のコード サンプルを実行します。 Python のサンプル コードは、必要に応じて Python IDE でも実行できます。
    ```console
    python temp_controller_with_thermostats.py
    ```

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、アプリケーション内で作成されたデバイス インスタンスに接続して、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
    c:\azure-iot-sdk-python\azure-iot-device\samples\pnp>python temp_controller_with_thermostats.py
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

1. IoT Central で **[デバイス]** を選択し、デバイス名をクリックして **[生データ]** タブを選択します。シミュレートされたデバイスからの生のテレメトリがこのビューに表示されます。 

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central デバイス テレメトリの生の出力":::
    
    デバイスが安全に接続され、Azure IoT にテレメトリを送信するようになりました。
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した IoT Central リソースが不要になった場合は、それらを削除してください。 引き続きこのガイドのドキュメントに従う場合は、必要に応じて、作成したアプリケーションを保持して他のサンプルに再利用することもできます。

Azure IoT Central のサンプル アプリケーションとそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure IoT Central を使用してアプリケーションとデバイスを作成した後、Azure IoT Python SDK を使用してシミュレートされたデバイスを作成し、テレメトリを送信しました。 また、IoT Central を使用してテレメトリを監視しました。

次のステップでは、デバイスをホストするためのソリューションとしての IoT Central について、また Azure SDK Python のコード サンプルについて詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [IoT Central アプリケーションを作成する](../iot-central/core/quick-deploy-iot-central.md)
> [!div class="nextstepaction"]
> [非同期デバイスのサンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)
> [!div class="nextstepaction"]
> [同期デバイスのサンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)
