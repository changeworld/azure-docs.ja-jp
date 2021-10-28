---
title: Azure IoT Central への Renesas RX65N Cloud Kit の接続のクイックスタート
description: Azure RTOS 組み込みソフトウェアを使用して、Renesas RX65N Cloud kit デバイスを Azure IoT に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: 73463d2474bae2ca0c6788a0332d3f797f2fb002
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226283"
---
# <a name="quickstart-connect-a-renesas-rx65n-cloud-kit-to-iot-central"></a>クイックスタート: Renesas RX65N Cloud Kit を IoT Central に接続する

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)<br>
**完了までの合計時間**: 30 分

[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RX65N_Cloud_Kit)

このクイックスタートでは、Azure RTOS を使用して、Renesas RX65N Cloud Kit (以降、Renesas RX65N) を Azure IoT に接続します。

次のタスクを実行します。

* C で Renesas RX65N をプログラミングするための、一連の組み込み開発ツールをインストールする
* イメージをビルドして Renesas RX65N にフラッシュする
* Azure IoT Central を使用して、クラウド コンポーネントを作成し、プロパティを表示し、デバイスのテレメトリを表示し、ダイレクト コマンドを呼び出す

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC
* リポジトリを複製するための [Git](https://git-scm.com/downloads)
* ハードウェア

    * [Renesas RX65N Cloud Kit](https://www.renesas.com/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-cloud-kit-renesas-rx65n-cloud-kit) (Renesas RX65N)
    * USB 2.0 A オス - ミニ USB オス ケーブル x 2
    * WiFi 2.4 GHz

## <a name="prepare-the-development-environment"></a>開発環境の準備

開発環境を設定するには、まず、このクイックスタートに必要なすべてのアセットを含む GitHub リポジトリをクローンします。 次に、一連のプログラミング ツールをインストールします。

### <a name="clone-the-repo-for-the-quickstart"></a>クイックスタート用のリポジトリのクローン

次のリポジトリを複製して、すべてのサンプル デバイス コード、設定スクリプト、オフライン バージョンのドキュメントをダウンロードします。 別のクイックスタートでこのリポジトリを既にクローンしている場合は、もう一度行う必要はありません。

リポジトリを複製するには、次のコマンドを実行します。

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>ツールのインストール

複製されたリポジトリには、必要なツールをインストールして構成する設定スクリプトが含まれています。 別の組み込みデバイスのクイックスタートでこれらのツールをインストールしてある場合は、もう一度行う必要はありません。

> [!NOTE]
> 設定スクリプトでは、次のツールがインストールされます。
> * [CMake](https://cmake.org): ビルド
> * [RX GCC](http://gcc-renesas.com/downloads/get.php?f=rx/8.3.0.202004-gnurx/gcc-8.3.0.202004-GNURX-ELF.exe): コンパイル
> * [Termite](https://www.compuphase.com/software_termite.htm): 接続されたデバイスのシリアル ポートの出力を監視する

ツールをインストールするには:

1. エクスプローラーで、リポジトリ内の次のパスに移動し、*get-toolchain-rx.bat* という名前の設定スクリプトを実行します。

    *getting-started\tools\get-toolchain-rx.bat*

1. RX コンパイラを Windows のパスに追加します。

   *%USERPROFILE%\AppData\Roaming\GCC for Renesas RX 8.3.0.202004-GNURX-ELF\rx-elf\rx-elf\bin*

1. インストールが完了したら、新しいコンソール ウィンドウを開き、設定スクリプトによって行われた構成の変更を確認します。 このコンソールを使用して、クイックスタートの残りのプログラミング タスクを実行します。 Windows CMD、PowerShell、または Git Bash for Windows を使用できます。
1. 次のコマンドを実行して、CMake バージョン 3.14 以降がインストールされており、RX コンパイラのパスが正しく設定されていることを確認します。

    ```shell
    cmake --version
    rx-elf-gcc --version
    ```
残りのツールをインストールするには:

* [Renesas Flash Programmer](https://www.renesas.com/software-tool/renesas-flash-programmer-programming-gui) をインストールします。 Renesas Flash Programmer には、Renesas E2 Lite を介して Renesas RX65N をフラッシュするために必要なドライバーとツールが含まれています。

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>デバイスの準備

Renesas RX65N を Azure に接続するには、Wi-Fi と Azure IoT の設定の構成ファイルを変更し、イメージをリビルドして、イメージをデバイスにフラッシュします。

### <a name="add-configuration"></a>構成を追加する

1. テキスト エディターで次のファイルを開きます。

    *getting-started\Renesas\RX65N_Cloud_Kit\app\azure_config.h*

1. Wi-Fi 定数をローカル環境の次の値に設定します。

    |定数名|値|
    |-------------|-----|
    |`WIFI_SSID` |{*自分の Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*自分の Wi-Fi パスワード*}|
    |`WIFI_MODE` |{*ファイル内の列挙された Wi-Fi モード値のいずれか*}|

1. Azure IoT デバイス情報の定数を、Azure リソースの作成後に保存した値に設定します。

    |定数名|値|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*自分の ID スコープ値*}|
    |`IOT_DPS_REGISTRATION_ID` |{*自分のデバイス ID 値*}|
    |`IOT_DEVICE_SAS_KEY` |{*自分のプライマリ キー値*}|

1. ファイルを保存して閉じます。

### <a name="build-the-image"></a>イメージをビルドする

1. コンソールまたはエクスプローラーで、次のパスのスクリプト *rebuild.bat* を実行してイメージをビルドします。

    *getting-started\Renesas\RX65N_Cloud_Kit\tools\rebuild.bat*

2. ビルドが完了したら、次のパスにバイナリ ファイルが作成されたことを確認します。

    *getting-started\Renesas\RX65N_Cloud_Kit\build\app\rx65n_azure_iot.hex*

### <a name="connect-the-device"></a>デバイスを接続する

> [!NOTE]
> Renesas RX65N の設定と概要の詳細については、[Renesas RX65N Cloud Kit クイック スタート](https://www.renesas.com/document/man/quick-start-guide-renesas-rx65n-cloud-kit)に関するページを参照してください。

1. 次の図を参考にして、次の手順を実行します。
    
    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/renesas-rx65n.jpg" alt-text="Renesas RX65N のボードで、reset、USB、E1/E2Lite を見つける":::

1. ボードから **EJ2** のリンクを取り外して、E2 Lite デバッガーを有効にします。 リンクは、**USER SW** ボタンの下にあります。
    > [!WARNING] 
    > このリンクを取り外さないと、デバイスをフラッシュできなくなります。

1. **WiFi モジュール** を **Cloud Option Board** に接続します

1. 最初のミニ USB ケーブルを使用して、Renesas RX65N の **USB シリアル** をコンピューターに接続します。

1. 2 つ目のミニ USB ケーブルを使用して、Renesas RX65N の **USB E2 Lite** をコンピューターに接続します。

### <a name="flash-the-image"></a>イメージをフラッシュする

1. [スタート] メニューから *Renesas Flash Programmer* アプリケーションを起動します。

2. *[File]\(ファイル\)* メニューの *[New Project...]\(新しいプロジェクト...\)* を選択し、次の設定を入力します。
    * **[Microcontroller]\(マイクロコントローラー\)** : RX65x
    * **[Project Name]\(プロジェクト名\)** : RX65N
    * **[Tool]\(ツール\)** : E2 emulator Lite
    * **[Interface]\(インターフェイス\)** : FINE

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-new.png" alt-text="Renesas Flash Programmer、新しいプロジェクトのスクリーンショット":::

3. *[Tool Details]\(ツールの詳細\)* ボタンを選択し、 *[Reset Settings]\(設定のリセット\)* タブに移動します。

4. *[Reset Pin as Hi-Z]\(Pin を Hi-Z としてリセット\)* を選択して *[OK]* をクリックします。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-reset.png" alt-text="Renesas Flash Programmer、設定のリセットのスクリーンショット":::

5. *[Connect]\(接続\)* ボタンを押し、メッセージが表示されたら *[Auto Authentication]\(自動認証\)* チェックボックスをオンにして、 *[OK]* を押します。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/rfp-auth.png" alt-text="Renesas Flash Programmer、認証のスクリーンショット":::

6. *[Browse...]\(参照...\)* ボタンを選択し、前のセクションで作成した *rx65n_azure_iot.hex* ファイルを見つけます。

7. *[Start]\(開始)* を押して、フラッシュを開始します。 このプロセスには 10 秒ほどかかります。

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認できます。
> [!TIP]
> フラッシュ後、デバイスを初期化または接続するときに問題が発生した場合は、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。

1. **Termite** を開始します。
1. **[設定]** を選択します。
1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port (ポート)** : Renesas RX65N が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. [OK] を選択します。
1. デバイス上の **リセット** ボタンを押します。
1. **Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.21
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.76.76
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.79.214.107
        SNTP time update: May 21, 2021 20:24:10.76 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgrx65ncloud;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

## <a name="verify-the-device-status"></a>デバイスの状態を確認する

IoT Central ポータルでデバイスの状態を表示するには:
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[デバイスの状態]** が **[プロビジョニング済み]** に更新されていることを確認します。
1. **[デバイス テンプレート]** が **[RX65N Cloud Kit ファースト ステップ ガイド]** に更新されていることを確認します。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-view-status.png" alt-text="IoT Central のデバイスの状態のスクリーンショット":::

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT Central を使用すると、デバイスからクラウドへのテレメトリのフローを表示できます。

IoT Central ポータルでテレメトリを表示するには:

1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. デバイスの一覧からデバイスを選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを **[概要]** タブで表示します。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-telemetry.png" alt-text="IoT Central のデバイス テレメトリのスクリーンショット":::

    > [!NOTE]
    > Termite アプリを使用して、デバイスからテレメトリを監視することもできます。

## <a name="call-a-direct-method-on-the-device"></a>デバイス上のダイレクト メソッドを呼び出す

また、IoT Central を使用して、デバイスに実装したダイレクト メソッドを呼び出すこともできます。 ダイレクト メソッドには名前があり、必要に応じて、JSON ペイロード、構成可能な接続、メソッドのタイムアウトを設定できます。 このセクションでは、LED のオンとオフを切り替えることができるメソッドを呼び出します。

IoT Central ポータルでメソッドを呼び出すには:

1. [デバイス] ページで、 **[コマンド]** タブを選択します。
1. **[状態]** ドロップダウンで **[True]** を選択し、 **[実行]** を選択します。  LED ライトがオンになります。

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-invoke-method.png" alt-text="デバイスでダイレクト メソッドを呼び出している IoT Central のスクリーンショット":::

1. **[状態]** ドロップダウンで **[False]** を選択し、 **[実行]** を選択します。 LED ライトがオフになります。

## <a name="view-device-information"></a>デバイス情報を表示する

IoT Central からデバイス情報を表示できます。

[デバイス] ページから **[バージョン情報]** タブを選択します。

:::image type="content" source="media/quickstart-devkit-renesas-rx65n-cloud-kit/iot-central-device-about.png" alt-text="IoT Central のデバイス情報のスクリーンショット":::

## <a name="troubleshoot"></a>トラブルシューティング

デバイス コードのビルド、デバイスのフラッシュ、または接続で問題が発生した場合は、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成した Azure リソースが不要になったら、IoT Central ポータルからそれらを削除できます。

Azure IoT Central のサンプル アプリケーション全体とそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure RTOS サンプル コードを含むカスタム イメージをビルドし、そのイメージを Renesas RX65N デバイスにフラッシュしました。 また、IoT Central ポータルを使用して、Azure リソースを作成し、Renesas RX65N を安全に Azure に接続し、テレメトリを表示し、メッセージを送信しました。

次のステップとして、IoT デバイス SDK を使用してデバイスを Azure IoT に接続する方法の詳細について次の記事を確認してください。 

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Central に接続する](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Hub に接続する](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS には、通信をセキュリティで保護し、基になる MCU/MPU ハードウェア保護メカニズムを使用してコードとデータの分離を作成するためのコンポーネントを含む OEM があります。 ただし、各 OEM は最終的に、そのデバイスが進化するセキュリティ要件を確実に満たすようにする役目を負っています。

