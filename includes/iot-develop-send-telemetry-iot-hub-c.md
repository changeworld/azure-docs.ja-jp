---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 03100422122a7a6843fcf774b3bc35190b9cd4b4
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712879"
---
[![コードを参照](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp/pnp_temperature_controller)

このクイックスタートでは、基本的な Azure IoT アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとデバイスを作成します。 次に、Azure IoT device SDK サンプルを使用して、シミュレートされた温度コントローラーを実行し、それをハブに安全に接続して、テレメトリを送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- [Git](https://git-scm.com/downloads).
- Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    - ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にログインします。 [Cloud Shell のクイックスタート](../articles/cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    - 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、「[Azure CLI をインストールする]( /cli/azure/install-azure-cli)」をご覧ください。

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
1. インストールを実行します。

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>シミュレートされたデバイスを実行する
このセクションでは、C SDK を使用して、シミュレートされたデバイスから対象の IoT ハブにメッセージを送信します。

### <a name="build-the-sample"></a>サンプルをビルドする
1. コンソールを開き、Azure IoT C device SDK をインストールし、コード サンプルを実行します。 Windows の場合は、 **[スタート]** を選択し、「*VS 2019 用開発者コマンド プロンプト*」と入力して、コンソールを開きます。 Linux の場合は、Bash を開きます。
    > [!NOTE]
    > これで、開いたばかりのコンソール ウィンドウと、CLI コマンドを入力するために以前に使用した Cloud Shell または CLI コンソールの、2 つのコンソール ウィンドウが開いていることになります。

1. C コンソールで、Azure IoT C device SDK を、お使いのローカル コンピューターに複製します。
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

### <a name="run-the-code"></a>コードの実行
1. CLI アプリで、[az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行して、シミュレートされた IoT デバイスでのイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```
1. コンソールに適したコマンドを使用して、サンプル コードを実行します。

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../articles/iot-develop/overview-iot-plug-and-play.md)」を参照してください。

    シミュレートされたデバイスは、IoT Central アプリケーションに接続した後、アプリケーション内で作成されたデバイス インスタンスに接続して、テレメトリの送信を開始します。 接続の詳細とテレメトリの出力がコンソールに表示されます。 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: ''
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: myDevice
      payload: '{"workingSet":1251}'
    
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: myDevice
      payload: '{"temperature":22.00}'
    ```