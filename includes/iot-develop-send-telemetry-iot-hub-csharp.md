---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 5a72409880953570163728d1e8e943ebdbeb1ba0
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518297"
---
## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Visual Studio (Community、Professional、または Enterprise) 2019](https://visualstudio.microsoft.com/downloads/)。
- [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリのローカル コピー。 リポジトリのコピーをダウンロードして展開します: [ZIP をダウンロード](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)します。
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](/azure/cloud-shell/quickstart)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 このクイックスタートには、Azure CLI バージョン 2.0.76 以降が必要です。 バージョンを確認するには、`az --version` を使用します。 [Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページの手順に従って、Azure CLI をインストールまたはアップグレードした後、実行して、ログインします。 初回使用時にインストールを求められたら、Azure CLI 拡張機能をインストールします。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、ローカル環境を構成し、シミュレートされた温度コントローラーを作成するサンプルを実行します。

Visual Studio でサンプル アプリケーションを実行するには:

1. Azure IoT Samples for C# を展開したフォルダーで、*azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* ソリューション ファイルを Visual Studio で開きます。 

1. **ソリューション エクスプローラー** で、**PnpDeviceSamples > TemperatureController** プロジェクト ファイルを選択して右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

1. **TemperatureController** プロジェクトを右クリックし、 **[プロパティ]** を選択します。 **[デバッグ]** タブを選択し、次の環境変数をプロジェクトに追加します。

    | Name | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 以前に保存した接続文字列。 |

1. 更新された **TemperatureController** プロジェクト ファイルを保存します。

1. CLI アプリで、[az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされた IoT デバイスでのイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Visual Studio で、Ctrl キーを押しながら F5 キーを押して、サンプルを実行します。

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 36.7
    ```