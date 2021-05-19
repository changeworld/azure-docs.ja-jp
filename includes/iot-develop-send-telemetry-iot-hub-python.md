---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 3e5dadcd61246e68cb90ed6d115e7d10bebda92f
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518294"
---
## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- [Python](https://www.python.org/downloads/) バージョン 3.7 以降。 Python のバージョンを確認するには、`python --version` を実行します。
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](/azure/cloud-shell/quickstart)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 このクイックスタートには、Azure CLI バージョン 2.0.76 以降が必要です。 バージョンを確認するには、`az --version` を使用します。 [Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページの手順に従って、Azure CLI をインストールまたはアップグレードした後、実行して、ログインします。 初回使用時にインストールを求められたら、Azure CLI 拡張機能をインストールします。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、Python SDK を使用して、シミュレートされたデバイスから対象の IoT ハブにメッセージを送信します。

1. 新しいコンソール ウィンドウを開きます。 このコンソールを使用して Python SDK をインストールし、Python のサンプル コードを操作します。 これで、開いたばかりのコンソール ウィンドウと、CLI コマンドを入力するために以前に使用した Cloud Shell または CLI コンソールの、2 つのコンソール ウィンドウが開いていることになります。

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

1. CLI アプリで、[az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされた IoT デバイスでのイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Python コンソールで、次のサンプル ファイルのコードを実行します。 このサンプルは、サーモスタット センサーを備えたシミュレートされた温度コントローラーを作成します。

    ```console
    python temp_controller_with_thermostats.py
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../articles/iot-pnp/overview-iot-plug-and-play.md)」を参照してください。

 Python コードがデバイスから IoT ハブにメッセージを送信すると、イベントを監視している CLI アプリにそのメッセージが表示されます。

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: thermostat1
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 29

event:
  component: thermostat2
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 48
```

デバイスが安全に接続され、Azure IoT Hub にテレメトリを送信するようになりました。