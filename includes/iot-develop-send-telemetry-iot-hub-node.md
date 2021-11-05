---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: d96e331727c1c4d306746c35565f759fcd12a4c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860505"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 Azure CLI と IoT エクスプローラーを使用して、Azure IoT ハブとデバイスを作成します。 次に Azure IoT device SDK サンプルを使用して、温度コントローラーを実行し、それをハブに安全に接続し、テレメトリを送信します。 温度コントローラーのサンプル アプリケーションはローカル コンピューター上で動作し、センサー データをシミュレートして IoT Hub に送信します。

## <a name="prerequisites"></a>前提条件
このクイックスタートは、Windows、Linux、Raspberry Pi で実行できます。 これは、次の OS およびデバイス バージョンでテストされています。

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi 3 Model B+ で実行されている Raspberry Pi OS バージョン 10 (Raspian)

Raspberry Pi に関する説明がある場合を除き、開発マシンに次の前提条件をインストールします。

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- [Node.js](https://nodejs.org) バージョン 10 以降。 ノードのバージョンを確認するには、`node --version` を実行します。
- [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT を監視および管理するための GUI ベースのクロスプラットフォーム ユーティリティ。 開発プラットフォームとして Raspberry Pi を使用している場合は、別のコンピューターに IoT エクスプローラーをインストールすることをお勧めします。 IoT エクスプローラーをインストールしない場合は、Azure CLI を使用して同じ手順を実行できます。 
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](../articles/cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、「[Azure CLI をインストールする]( /cli/azure/install-azure-cli)」をご覧ください。 開発プラットフォームとして Raspberry Pi を使用している場合は、Azure Cloud Shell を使用するか、または別のコンピューターに Azure CLI をインストールすることをお勧めします。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する
このセクションでは、Node.js SDK を使用して、デバイスから IoT hub にメッセージを送信します。 2 つのサーモスタット センサーを備えた温度コントローラーを実装するサンプルを実行します。

1. Windows CMD、PowerShell、または Bash などの新しいコンソールを開きます。 以下の手順では、このコンソールを使用して Node.js SDK をインストールし、Node.js のサンプル コードを操作します。

    > [!NOTE]
    > Azure CLI のローカル インストールを使用している場合は、次の 2 つのコンソール ウィンドウが開きます。 このセクションのコマンドは、CLI で使用していたものではなく、必ずこの開いたコンソールに入力してください。

1. [Azure IoT Node.js SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)をローカル コンピューターにクローンします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. sample ディレクトリに移動します。

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\javascript
    ```

    **Linux または Raspberry Pi OS**
    ```console
    cd azure-iot-sdk-node/device/samples/javascript
    ```

1. Azure IoT Node.js SDK および必要な依存関係をインストールします。

    ```console
    npm install
    ```

    このコマンドを実行すると、*package.json* ファイルに指定されている適切な依存関係が device/samples ディレクトリにインストールされます。

1. 次の環境変数両方を設定し、Azure IoT にデバイスを接続できるようにします。
    * `IOTHUB_DEVICE_CONNECTION_STRING` という環境変数を設定します。 変数の値には、前のセクションで保存したデバイス接続文字列を使用します。
    * `IOTHUB_DEVICE_SECURITY_TYPE` という環境変数を設定します。 変数には、リテラル文字列値 `connectionString` を使用します。

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Windows CMD では、各変数の文字列値を囲む引用符は入力しません。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. 次のサンプル コードを実行します。

    ```console
    node pnp_temperature_controller.js
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../articles/iot-develop/overview-iot-plug-and-play.md)」を参照してください。

このサンプルを実行すると、登録したデバイスとして IoT ハブに安全に接続され、テレメトリ メッセージの送信が開始されます。 サンプル出力がコンソールに表示されます。

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT エクスプローラーでは、デバイスのテレメトリを表示できます。 必要に応じて、Azure CLI を使用してテレメトリを表示できます。

Azure IoT エクスプローラーでテレメトリを表示するには:

1. IoT エクスプローラーの IoT ハブから、 **[View devices in this hub]\(このハブのデバイスを表示\)** を選択し、リストからデバイスを選択します。 
1. デバイスの左側のメニューで、 **[テレメトリ]** を選択します。
1. **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを表示します。

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-node/iot-explorer-device-telemetry.png" alt-text="IoT エクスプローラーのデバイス テレメトリのスクリーンショット":::

1. **[停止]** を選択して、イベントの受信を終了します。

個々のデバイス コンポーネントによって送信されたテレメトリを読み取るには、IoT エクスプローラーのプラグ アンド プレイ機能を使用できます。 たとえば、このクイックスタートの温度コントローラーには、thermostat1 と thermostat2 の 2 つのサーモスタットがあります。 thermostat1 によって報告された温度を確認するには: 

1. IoT エクスプローラーのデバイスで、左側のメニューから **[IoT プラグ アンド プレイ コンポーネント]** を選択します。 次に、コンポーネントの一覧から **[thermostat1]** を選択します。

1. **[thermostat1]** コンポーネント ペインで、上部のメニューから **[テレメトリ]** を選択します。

1. **[テレメトリ]** ペインで、前と同じ手順に従います。 **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。

Azure CLI を使用してデバイス テレメトリを表示するには:

1. [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、デバイスから IoT ハブに送信するイベントをモニターします。 Azure IoT で以前に作成した、デバイスと IoT ハブの名前を使用します。

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 接続の詳細とテレメトリの出力をコンソールに表示します。

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 70.5897683228018
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 52.87582619316418
    ```
    
