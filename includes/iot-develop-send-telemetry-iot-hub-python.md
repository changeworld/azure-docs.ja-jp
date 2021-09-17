---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: ef3c7af3150cd492be4d56d51bf31ecb989f45a2
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122635796"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとデバイスを作成します。 次に、Azure IoT device SDK サンプルを使用して、シミュレートされた温度コントローラーを実行し、それをハブに安全に接続して、テレメトリを送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- [Python](https://www.python.org/downloads/) バージョン 3.7 以降。 Python のバージョンを確認するには、`python --version` を実行します。
- [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT を監視および管理するためのクロスプラットフォーム ユーティリティ 
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](../articles/cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、「[Azure CLI をインストールする]( /cli/azure/install-azure-cli)」をご覧ください。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、Python SDK を使用して、シミュレートされたデバイスから対象の IoT ハブにメッセージを送信します。 2 つのサーモスタット センサーを備えた温度コントローラーを実装するサンプルを実行します。

1. 新しいコンソール ウィンドウを開きます。 このコンソールを使用して Python SDK をインストールし、Python のサンプル コードを操作します。

    > [!NOTE]
    > Azure CLI のローカル インストールを使用している場合、2 つのコンソール ウィンドウが開くことがあります。 このセクションのコマンドは、CLI で使用していたものではなく、必ずこの開いたコンソールに入力してください。

1. Python コンソールで、[Azure IoT Python SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)を、お使いのローカル コンピューターに複製します。

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
1. シミュレートされたデバイスを Azure IoT に接続できるよう、次の環境変数を設定します。
    * `IOTHUB_DEVICE_CONNECTION_STRING` という環境変数を設定します。 変数の値には、前のセクションで保存したデバイス接続文字列を使用します。
    * `IOTHUB_DEVICE_SECURITY_TYPE` という環境変数を設定します。 変数には、リテラル文字列値 `connectionString` を使用します。

    **Windows (cmd)**

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

    **Bash (Linux または Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. Python コンソールで、次のサンプル ファイルのコードを実行します。

    ```console
    python temp_controller_with_thermostats.py
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT プラグ アンド プレイの利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../articles/iot-develop/overview-iot-plug-and-play.md)」を参照してください。

このサンプルを実行すると、登録したデバイスとして IoT ハブに安全に接続され、テレメトリ メッセージの送信が開始されます。 サンプル出力がコンソールに表示されます。

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT エクスプローラーでは、デバイスのテレメトリを表示できます。 必要に応じて、Azure CLI を使用してテレメトリを表示できます。

Azure IoT エクスプローラーでテレメトリを表示するには:

1. IoT エクスプローラーの IoT ハブから、 **[View devices in this hub]\(このハブのデバイスを表示\)** を選択し、一覧からデバイスを選択します。 
1. デバイスの左側のメニューで、 **[テレメトリ]** を選択します。
1. **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを表示します。

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-python/iot-explorer-device-telemetry.png" alt-text="IoT エクスプローラーのデバイス テレメトリのスクリーンショット":::

1. **[停止]** を選択して、イベントの受信を終了します。

個々のデバイス コンポーネントによって送信されたテレメトリを読み取るには、IoT エクスプローラーのプラグ アンド プレイ機能を使用できます。 たとえば、このクイックスタートの温度コントローラーには、thermostat1 と thermostat2 の 2 つのサーモスタットがあります。 thermostat1 によって報告された気温を確認するには: 

1. IoT エクスプローラーのデバイスで、左側のメニューから **[IoT プラグ アンド プレイ コンポーネント]** を選択します。 次に、コンポーネントの一覧から **[thermostat1]** を選択します。

1. **[thermostat1]** コンポーネント ペインで、上部のメニューから **[テレメトリ]** を選択します。

1. **[テレメトリ]** ペインで、前と同じ手順に従います。 **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。

Azure CLI を使用してデバイス テレメトリを表示するには:

1. お使いの CLI アプリで、[az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされたデバイスから IoT ハブに送信されるイベントを監視します。 Azure IoT で以前に作成した、デバイスと IoT ハブの名前を使用します。

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 接続の詳細とテレメトリの出力をコンソールで確認します。

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 29
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 48
    ```
    