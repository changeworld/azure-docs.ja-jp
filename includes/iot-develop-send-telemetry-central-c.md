---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/06/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 9b383086eb9a53f061f445b26fe2bcd1242524ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801422"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 最初に、デバイスをホストするための Azure IoT Central アプリケーションを作成します。 次に、Azure IoT device SDK サンプルを使用して、シミュレートされた温度コントローラーを実行し、それを IoT Central に安全に接続して、テレメトリを送信します。

## <a name="prerequisites"></a>前提条件
- このクイックスタートは、Linux または Windows で実行できます。 シェル コマンドでは標準の Linux パス区切り記号 `/` を使用します。 Windows を使用する場合は、これらの区切り記号を Windows パス区切り記号 `\` に置き換えてください。

お使いのオペレーティング システムの残りの前提条件をインストールします。

### <a name="linux"></a>Linux
このチュートリアルの手順は、Ubuntu Linux 18.04 を使用してテストされました。

このクイックスタートを Linux で完了するには、ご利用のローカルの Linux 環境に以下のソフトウェアをインストールする必要があります。

`apt-get` コマンドを使用して、**GCC**、**Git**、**cmake**、および必要な依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
このクイックスタートを Windows で完了するには、Visual Studio 2019 をインストールし、C および C++ 開発に必要なコンポーネントを追加します。

1. 新しいユーザーの場合は、[Visual Studio (Community、Professional、または Enterprise) 2019](https://visualstudio.microsoft.com/downloads/) をインストールします。 インストールするエディションをダウンロードし、インストーラーを起動します。
    > [!NOTE]
    > 既存の Visual Studio 2019 ユーザーの場合は、Windows の **[スタート]** を選択し、「*Visual Studio インストーラー*」と入力して、インストーラーを開始します。
1. インストーラーの **[ワークロード]** タブで、 **[Desktop Development with C++]\(C++ によるデスクトップ開発\)** ワークロードを選択します。
1. インストーラーの **[個別のコンポーネント]** タブで、 **[Git for Windows]** を選択します。
1. インストールを実行します。

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、ローカル環境を構成し、Azure IoT C device SDK をインストールし、シミュレートされた温度コントローラーを作成するサンプルを実行します。

### <a name="configure-your-environment"></a>環境を構成する

1. コンソールを開き、Azure IoT C device SDK をインストールし、コード サンプルを実行します。 Windows の場合は、 **[スタート]** を選択し、「*VS 2019 用開発者コマンド プロンプト*」と入力して、コンソールを開きます。 Linux の場合は、Bash を開きます。 

1. コンソールに適したコマンドを使用して、次の環境変数を設定します。 シミュレートされたデバイスは、これらの値を使用して IoT Central に接続します。 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY`、`IOTHUB_DEVICE_DPS_DEVICE_ID` には、前に保存したデバイス接続の値を使用してください。

    **CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Windows CMD では、変数の値を囲む引用符は入力しません。

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK とサンプルをインストールする

1. Azure IoT C device SDK を、お使いのローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```

1. SDK のルート フォルダーに移動し、次のコマンドを実行して依存関係を更新します。
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    この操作は、数分かかります。

1. SDK とサンプルをビルドするには、次のコマンドを実行します。

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```

### <a name="run-the-code"></a>コードの実行

1. コンソールに適したコマンドを使用して、サンプル コードを実行します。

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、アプリケーション内で作成されたデバイス インスタンスに接続して、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
    Info: Initiating DPS client to retrieve IoT Hub connection information
    -> 17:03:08 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: xxxxxxxxxxxxxxx/registrations/my-sdk-device/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
    <- 17:03:09 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
    -> 17:03:10 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
    <- 17:03:11 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
    Info: Provisioning callback indicates success.  iothubUri=iotc-xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net, deviceId=my-sdk-device
    -> 17:03:27 DISCONNECT
    Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
    Info: Successfully created device client.  Hit Control-C to exit program
    
    Info: Sending serialNumber property to IoTHub
    Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
    Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
    Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
    Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
    Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
    Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
    Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
    Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
    ```