---
title: Azure IoT Central への STMicroelectronics B-L475E-IOT01A または B-L4S5I-IOT01A の接続のクイックスタート
description: Azure RTOS 組み込みソフトウェアを使用して、STMicroelectronics B-L475E-IOT01A または B-L4S5I-IOT01A デバイスを Azure IoT に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 30e64f70f713540574545200dc188342b63e2f01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242163"
---
# <a name="quickstart-connect-an-stmicroelectronics-b-l475e-iot01a-or-b-l4s5i-iot01a-discovery-kit-to-iot-central"></a>クイックスタート: STMicroelectronics B-L475E-IOT01A または B-L4S5I-IOT01A Discovery kit を IoT Central に接続する

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)<br>
**完了までの合計時間**: 30 分

[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+)

このクイックスタートでは、Azure RTOS を使用して、STMicroelectronics [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) または [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) Discovery kit (以降、STM DevKit) を Azure IoT に接続します。

次のタスクを実行します。

* C で STM DevKit をプログラミングするための、一連の組み込み開発ツールをインストールする
* イメージをビルドして STM DevKit にフラッシュする
* Azure IoT Central を使用して、クラウド コンポーネントを作成し、プロパティを表示し、デバイスのテレメトリを表示し、ダイレクト コマンドを呼び出す

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC
* リポジトリを複製するための [Git](https://git-scm.com/downloads)
* ハードウェア

    * [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) または [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) (STM DevKit)
    * Wi-Fi 2.4 GHz
    * USB 2.0 A male to Micro USB male ケーブル

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
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): コンパイル
> * [Termite](https://www.compuphase.com/software_termite.htm): 接続されたデバイスのシリアル ポートの出力を監視する

ツールをインストールするには:

1. エクスプローラーで、リポジトリ内の次のパスに移動し、*get-toolchain.bat* という名前の設定スクリプトを実行します。

    *getting-started\tools\get-toolchain.bat*

1. インストールが完了したら、新しいコンソール ウィンドウを開き、設定スクリプトによって行われた構成の変更を確認します。 このコンソールを使用して、クイックスタートの残りのプログラミング タスクを実行します。 Windows CMD、PowerShell、または Git Bash for Windows を使用できます。
1. 次のコードを実行して、CMake バージョン3.14 以降がインストールされていることを確認します。

    ```shell
    cmake --version
    ```
[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>デバイスの準備

STM DevKit を Azure に接続するには、Wi-Fi と Azure IoT の設定の構成ファイルを変更し、イメージをリビルドして、イメージをデバイスにフラッシュします。

### <a name="add-configuration"></a>構成を追加する

1. テキスト エディターで次のファイルを開きます。

    *getting-started\STMicroelectronics\STM32L4_L4+\app\azure_config.h*

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

    *getting-started\STMicroelectronics\STM32L4_L4+\tools\rebuild.bat*

2. ビルドが完了したら、2 つのバイナリ ファイルが作成されたことを確認します。 STM Devkit ごとにバイナリ イメージがあります。 ビルドにより、イメージが次のパスに保存されます。

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l475_azure_iot.bin*

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l4S5_azure_iot.bin*

### <a name="flash-the-image"></a>イメージをフラッシュする

1. STM DevKit MCU で、**リセット** ボタン、**USB STLink** というラベルの付いたマイクロ USB ポート、ボードの部品番号を見つけます。 次の手順でこれらの項目を参照します。 次の図でこれらのすべてが強調表示されています。

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/stm-devkit-board.png" alt-text="STM DevKit ボードの主要コンポーネントを見つける":::

1. マイクロ USB ケーブルを STM DevKit の **USB STLINK** に接続し、それからコンピューターに接続します。

    > [!NOTE]
    > STM DevKit の詳細なセットアップ情報については、パッケージの手順を参照するか、[B-L475E-IOT01A のリソース](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html#resource)に関するページまたは [B-L4S5I-IOT01A のリソース](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html#resource)に関するページを参照してください。

1. エクスプローラーで、前のセクションで作成したバイナリ ファイルを見つけます。

1. 使用している STM Devkit の部品番号に対応するファイル名を持つバイナリ ファイルをコピーします。 たとえば、ボードの部品番号が **B-L475E-IOT01A1** の場合は、*stm32l475_azure_iot.bin* という名前のバイナリ ファイルをコピーします。

1. エクスプローラーで、コンピューターに接続されている STM DevKit を見つけます。 デバイスは、**DIS_L4IOT** というドライブ ラベルでシステム上のドライブとして表示されます。

1. バイナリ ファイルを STM Devkit のルート フォルダーに貼り付けます。 自動的に点滅が始まり、数秒後に完了します。

    > [!NOTE]
    > フラッシュ プロセス中、STM DevKit で LED が赤と緑の間で切り替わります。

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認できます。

1. **Termite** を開始します。
    > [!TIP]
    > Termite を Devkit に接続できない場合は、[ST-LINK ドライバー](https://www.st.com/en/development-tools/stsw-link009.html)をインストールして、もう一度やり直してください。 追加の手順については、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。
1. **[設定]** を選択します。
1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port (ポート)** : STM DevKit が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. [OK] を選択します。
1. デバイス上の **リセット** ボタンを押します。 ボタンは黒で、デバイス上にラベルが付いています。
1. **Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

    ```output
    Starting Azure thread

    Initializing WiFi
        Module: ISM43362-M3G-L44-SPI
        MAC address: C4:7F:51:8F:67:F6
        Firmware revision: C3.5.2.5.STM
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.22
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 21, 2021 22:42:8.394 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsg;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```
    > [!IMPORTANT]
    > DNS クライアントの初期化が失敗し、Wi-Fi ファームウェアが最新でないと通知される場合は、Wi-Fi モジュールのファームウェアを更新する必要があります。 [Inventek ISM 43362 Wi-Fi モジュール ファームウェア更新プログラム](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)をダウンロードしてインストールします。 次に、デバイスの **リセット** ボタンを押して接続を再確認し、このクイックスタートを続行します。


Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

## <a name="verify-the-device-status"></a>デバイスの状態を確認する

IoT Central ポータルでデバイスの状態を表示するには:
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[デバイスの状態]** が **[プロビジョニング済み]** に更新されていることを確認します。
1. **[デバイス テンプレート]** が **[ファースト ステップ ガイド]** に更新されていることを確認します。

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-view-status.png" alt-text="IoT Central のデバイスの状態のスクリーンショット":::

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT Central を使用すると、デバイスからクラウドへのテレメトリのフローを表示できます。

IoT Central ポータルでテレメトリを表示するには:

1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. デバイスの一覧からデバイスを選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを **[概要]** タブで表示します。

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-telemetry.png" alt-text="IoT Central のデバイス テレメトリのスクリーンショット":::

    > [!NOTE]
    > Termite アプリを使用して、デバイスからテレメトリを監視することもできます。

## <a name="call-a-direct-method-on-the-device"></a>デバイス上のダイレクト メソッドを呼び出す

また、IoT Central を使用して、デバイスに実装したダイレクト メソッドを呼び出すこともできます。 ダイレクト メソッドには名前があり、必要に応じて、JSON ペイロード、構成可能な接続、メソッドのタイムアウトを設定できます。 このセクションでは、LED のオンとオフを切り替えることができるメソッドを呼び出します。

IoT Central ポータルでメソッドを呼び出すには:

1. [デバイス] ページで、 **[コマンド]** タブを選択します。
1. **[状態]** ドロップダウンで **[True]** を選択し、 **[実行]** を選択します。  LED ライトがオンになります。

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-invoke-method.png" alt-text="デバイスでダイレクト メソッドを呼び出している IoT Central のスクリーンショット":::

1. **[状態]** ドロップダウンで **[False]** を選択し、 **[実行]** を選択します。 LED ライトがオフになります。

## <a name="view-device-information"></a>デバイス情報を表示する

IoT Central からデバイス情報を表示できます。

[デバイス] ページから **[バージョン情報]** タブを選択します。

:::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-about.png" alt-text="IoT Central のデバイス情報のスクリーンショット":::

## <a name="troubleshoot-and-debug"></a>トラブルシューティングとデバッグ

デバイス コードのビルド、デバイスのフラッシュ、または接続で問題が発生した場合は、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。

アプリケーションのデバッグについては、「[Visual Studio Code を使用したデバッグ](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成した Azure リソースが不要になったら、IoT Central ポータルからそれらを削除できます。

Azure IoT Central のサンプル アプリケーション全体とそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure RTOS サンプル コードを含むカスタム イメージをビルドし、そのイメージを STM DevKit デバイスにフラッシュしました。 また、IoT Central ポータルを使用して、Azure リソースを作成し、STM DevKit を安全に Azure に接続し、テレメトリを表示し、メッセージを送信しました。

次のステップとして、IoT デバイス SDK を使用してデバイスを Azure IoT に接続する方法の詳細について次の記事を確認してください。 

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Central に接続する](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Hub に接続する](quickstart-send-telemetry-iot-hub.md)


> [!IMPORTANT]
> Azure RTOS には、通信をセキュリティで保護し、基になる MCU/MPU ハードウェア保護メカニズムを使用してコードとデータの分離を作成するためのコンポーネントを含む OEM があります。 ただし、各 OEM は最終的に、そのデバイスが進化するセキュリティ要件を確実に満たすようにする役目を負っています。



