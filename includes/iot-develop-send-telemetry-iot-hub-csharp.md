---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: a1c4367e735f02a82c599002c294343145f9cda5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744309"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとデバイスを作成します。 次に、Azure IoT device SDK サンプルを使用して、シミュレートされた温度コントローラーを実行し、それをハブに安全に接続して、テレメトリを送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Visual Studio (Community、Professional、または Enterprise) 2019](https://visualstudio.microsoft.com/downloads/)。
- [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリのローカル コピー。 リポジトリのコピーをダウンロードして展開します: [ZIP をダウンロード](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)します。
- [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT を監視および管理するためのクロスプラットフォーム ユーティリティ 
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](../articles/cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、「[Azure CLI をインストールする]( /cli/azure/install-azure-cli)」をご覧ください。

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、C# SDK を使用して、シミュレートされたデバイスから対象の IoT ハブにメッセージを送信します。 2 つのサーモスタット センサーを備えた温度コントローラーを実装するサンプルを実行します。

Visual Studio でサンプル アプリケーションを実行するには:

1. Azure IoT Samples for C# を展開したフォルダーで、*azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* ソリューション ファイルを Visual Studio で開きます。 

1. **ソリューション エクスプローラー** で、**PnpDeviceSamples > TemperatureController** プロジェクト ファイルを選択して右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

1. **TemperatureController** プロジェクトを右クリックし、 **[プロパティ]** を選択します。 **[デバッグ]** タブを選択し、次の環境変数をプロジェクトに追加します。

    | Name | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 以前に保存した接続文字列。 |

1. 更新された **TemperatureController** プロジェクト ファイルを保存します。

1. Visual Studio で、Ctrl キーを押しながら F5 キーを押して、サンプルを実行します。

コンソール ウィンドウが開きます。 このサンプルを実行すると、登録したデバイスとして IoT ハブに安全に接続され、テレメトリ メッセージの送信が開始されます。 サンプル出力がコンソールに表示されます。

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT エクスプローラーでは、デバイスのテレメトリを表示できます。 必要に応じて、Azure CLI を使用してテレメトリを表示できます。

Azure IoT エクスプローラーでテレメトリを表示するには:

1. IoT エクスプローラーの IoT ハブから、 **[View devices in this hub]\(このハブのデバイスを表示\)** を選択し、リストからデバイスを選択します。 
1. デバイスの左側のメニューで、 **[テレメトリ]** を選択します。
1. **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを表示します。

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-csharp/iot-explorer-device-telemetry.png" alt-text="IoT エクスプローラーのデバイス テレメトリのスクリーンショット":::

1. **[停止]** を選択して、イベントの受信を終了します。

個々のデバイス コンポーネントによって送信されたテレメトリを読み取るには、IoT エクスプローラーのプラグ アンド プレイ機能を使用できます。 たとえば、このクイックスタートの温度コントローラーには、thermostat1 と thermostat2 の 2 つのサーモスタットがあります。 thermostat1 によって報告された温度を確認するには: 

1. IoT エクスプローラーのデバイスで、左側のメニューから **[IoT プラグ アンド プレイ コンポーネント]** を選択します。 次に、コンポーネントの一覧から **[thermostat1]** を選択します。

1. **[thermostat1]** コンポーネント ペインで、上部のメニューから **[テレメトリ]** を選択します。

1. **[テレメトリ]** ペインで、前と同じ手順に従います。 **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認してから、 **[開始]** を選択します。

Azure CLI を使用してデバイス テレメトリを表示するには:

1. お使いの CLI アプリで [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされたデバイスから IoT ハブに送信されるイベントを監視します。 Azure IoT で以前に作成した、デバイスと IoT ハブの名前を使用します。

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
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 36.7
    ```

1. Ctrl + C キーを押して監視を終了します。
