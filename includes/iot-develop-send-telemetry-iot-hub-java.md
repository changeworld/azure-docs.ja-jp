---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 1922efb97feda19154bd965f6d383daba4830338
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712871"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとデバイスを作成します。 次に、Azure IoT device SDK サンプルを使用して、シミュレートされた温度コントローラーを実行し、それをハブに安全に接続して、テレメトリを送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- Java SE Development Kit 8 以降がインストールされた開発マシン。 複数のプラットフォーム用の Java 8 (LTS) JDK は、「[OpenJDK の Zulu ビルドのダウンロード](https://www.azul.com/downloads/zulu-community/)」からダウンロードできます。 インストーラーで、 **[Add to Path]\(パスに追加\)** オプションを選択します。
- [Apache Maven 3](https://maven.apache.org/download.cgi)。 ダウンロードをローカル フォルダーに抽出した後、Maven */bin* フォルダーへの完全なパスを Windows PATH 変数に追加します。
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](../articles/cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、「[Azure CLI をインストールする]( /cli/azure/install-azure-cli)」をご覧ください。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、Java SDK を使用して、シミュレートされたデバイスから対象の IoT ハブにメッセージを送信します。

### <a name="configure-your-environment"></a>環境を構成する
1. コンソールを開き、Azure IoT Java device SDK をインストールし、コード サンプルをビルドして実行します。
    > [!NOTE]
    > これで、開いたばかりのコンソール ウィンドウと、CLI コマンドを入力するために以前に使用した Cloud Shell または CLI コンソールの、2 つのコンソール ウィンドウが開いていることになります。
1. シミュレートされたデバイスを Azure IoT に接続できるよう、次の環境変数を設定します。
    * `IOTHUB_DEVICE_CONNECTION_STRING` という環境変数を設定します。 変数の値には、前のセクションで保存したデバイス接続文字列を使用します。
    * `IOTHUB_DEVICE_SECURITY_TYPE` という環境変数を設定します。 変数には、リテラル文字列値 `connectionString` を使用します。

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Windows CMD では、各変数の文字列値を囲む引用符は入力しません。

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="build-the-sample"></a>サンプルをビルドする
1. ご利用のコンソールで、Azure IoT Java device SDK を、自分のローカル コンピューターに複製します。
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```
1. SDK のルート フォルダーに移動し、次のコマンドを実行して、SDK をビルドし、サンプルを更新します。
    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```
    この操作は数分かかります。

### <a name="run-the-code"></a>コードの実行
1. CLI アプリで、[az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされた IoT デバイスでのイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```
1. Java コンソールで、samples ディレクトリに移動します。
    ```console
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```
1. Java コンソールで、次のコード サンプルを実行します。 このサンプルは、サーモスタット センサーを備えたシミュレートされた温度コントローラーを作成します。
    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../articles/iot-develop/overview-iot-plug-and-play.md)」を参照してください。

    シミュレートされたデバイスは、IoT ハブに接続した後、アプリケーション内で作成されたデバイス インスタンスに接続して、テレメトリの送信を開始します。 最初のプロビジョニングの詳細が完了すると、コンソールによって温度コントローラーのテレメトリの出力が開始されます。
    
    ```output
    2021-05-13 22:11:05.639 DEBUG TemperatureController:518 - Telemetry - Response from IoT Hub: message Id=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx, status=OK_EMPTY
    2021-05-13 22:11:10.229 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.229 INFO  IotHubTransport:1473 - Sending message ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.231 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 22.8░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    2021-05-13 22:11:10.234 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.236 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 35.3░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```