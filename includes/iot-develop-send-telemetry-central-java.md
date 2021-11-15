---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6e3a7b7b37e02f6f351caf985fd997d7daafd70c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861328"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 最初に、デバイスをホストするための Azure IoT Central アプリケーションを作成します。 次に Azure IoT device SDK サンプルを使用して、温度コントローラーを作成し、それを IoT Central に安全に接続し、テレメトリを送信します。 温度コントローラーのサンプル アプリケーションはローカル コンピューター上で動作し、センサー データをシミュレートして IoT Central に送信します。

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Windows、Linux、Raspberry Pi で実行できます。 これは、次の OS およびデバイス バージョンでテストされています。

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi 3 Model B+ で実行されている Raspberry Pi OS バージョン 10 (buster)

次に示す前提条件を開発マシンにインストールします。

- [Git](https://git-scm.com/downloads).

お使いのオペレーティング システムの残りの前提条件をインストールします。

### <a name="windows"></a>Windows

Windows でこのクイックスタートを完了するには、次のソフトウェアをインストールします。

- Java SE Development Kit 8 以降。 複数のプラットフォーム用の Java 8 (LTS) JDK は、「[OpenJDK の Zulu ビルドのダウンロード](https://www.azul.com/downloads/zulu-community/)」からダウンロードできます。 インストーラーで、 **[Add to Path]\(パスに追加\)** オプションを選択します。

- [Apache Maven 3](https://maven.apache.org/download.cgi)。 ダウンロードをローカル フォルダーに抽出した後、Maven */bin* フォルダーへの完全なパスを Windows `PATH` 環境変数に追加します。

### <a name="linux-or-raspberry-pi-os"></a>Linux または Raspberry Pi OS

このクイックスタートを Linux または Raspberry Pi OS で完了するには、次のソフトウェアをインストールします。

> [!NOTE]
> このセクションの手順は、Linux Ubuntu/Debian ディストリビューションに基づいています。 (Raspberry Pi OS は Debian に基づいています)。別の Linux ディストリビューションを使用している場合は、それに応じて手順を変更する必要があります。

- OpenJDK (Open Java Development Kit) 8 以降。 `java -version` コマンドを使用して、システムにインストールされている Java のバージョンを確認できます。 Java Runtime (JRE) だけでなく、JDK がインストールされていることを確認します。

    1. システムの OpenJDK をインストールするには、次のコマンドを入力します。

        システムの OpenJDK の既定のバージョン (記事の執筆時点では、Ubuntu 20.04 および Raspberry Pi OS 10 用 OpenJDK 11) をインストールする場合は、次のようにします。

        ```bash
        sudo apt update
        sudo apt install default-jdk
        ```

        または、インストールする JDK のバージョンを指定することもできます。 次に例を示します。

        ```bash
        sudo apt update
        sudo apt install openjdk-8-jdk
        ```

    1. システムに複数のバージョンの Java がインストールされている場合は、次のコマンドを使用して、Java と Java コンパイラの既定 (自動) バージョンを構成できます。

        ```bash
        update-java-alternatives --list          #list the Java versions installed
        sudo update-alternatives --config java   #set the default Java version
        sudo  update-alternatives --config javac #set the default Java compiler version
        ```

    1. JDK インストールのパスを指すように `JAVA_HOME` 環境変数を設定する必要があります。 (これは通常、 **/usr/lib/jvm** ディレクトリ内のバージョン管理されたサブディレクトリです)。

        ```bash
        export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
        ```

        > [!IMPORTANT]
        > このコマンドは、 現在のシェル環境で `JAVA_HOME` 変数を設定します。 新しいシェルを開いたときにすぐに使用できるように、`~/.bashrc` または `/etc/profile` ファイルにコマンドを追加することをお勧めします。

    1. Java JDK (および JRE) のバージョンがインストールされていること、Java コンパイラのバージョンが JDK のバージョンと一致していること、および `JAVA_HOME` 環境変数が適切に設定されていることを確認します。

        ```bash
        java -version
        javac -version
        echo $JAVA_HOME
        ```

- Apache Maven 3。 `mvn --version` コマンドを使用して、システムにインストールされている Maven のバージョンを確認できます。

    1. 次のコマンドを入力して、Maven をインストールします。

        ```bash
        sudo apt-get update
        sudo apt-get install maven
        ```

    1. 次のコマンドを実行して、インストールを確認します。

        ```bash
        mvn --version
        ```

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

このセクションでは、ローカル環境を設定し、Azure IoT Java device SDK をインストールし、温度コントローラーを作成するサンプルを実行します。

### <a name="configure-your-environment"></a>環境を構成する

1. Windows CMD または Bash などのコンソールを開きます。

    **Linux および Raspberry Pi OS**

    JAVA_HOME (`echo $JAVA_HOME`) 環境変数が設定されていることを確認します。 SDK とサンプルを正常にビルドするには、この環境変数を設定する必要があります。 JAVA_HOME の設定の詳細については、[Linux または Raspberry Pi の前提条件](#linux-or-raspberry-pi-os)に関するセクションを参照してください。

1. コンソールに適したコマンドを使用して、次の環境変数を設定します。 このデバイスでは、これらの値を使用して IoT Central に接続します。 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY`、`IOTHUB_DEVICE_DPS_DEVICE_ID` には、前に保存したデバイス接続の値を使用してください。

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

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="build-and-run-the-code"></a>コードのビルドと実行

1. Azure IoT Java device SDK を、お使いのローカル コンピューターにクローンします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. SDK のルート フォルダーに移動し、次のコマンドを実行して、SDK をビルドし、サンプルを更新します。

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    この操作は数分かかります。

1. sample ディレクトリに移動します。

    **Windows**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Linux または Raspberry Pi OS**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. コンソールで、SDK の次のコード サンプルを実行します。 このサンプルでは、サーモスタット センサーを備えた温度コントローラーを作成します。

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    デバイスを IoT Central アプリケーションに接続すると、そのアプリケーションで作成したデバイス インスタンスにデバイスが接続され、テレメトリの送信を開始します。 最初のプロビジョニングの詳細が完了すると、コンソールで温度コントローラーのテレメトリの出力が開始されます。

    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```