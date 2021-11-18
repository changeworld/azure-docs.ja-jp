---
title: Azure IoT Central への Microchip ATSAME54-XPro の接続のクイックスタート
description: Azure RTOS 組み込みソフトウェアを使用して、Microchip ATSAME54-XPro デバイスを Azure IoT に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 10/18/2021
zone_pivot_groups: iot-develop-toolset
ms.openlocfilehash: f5bae84a80ed76b2a000ac2cdda134ce923a9aa9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708155"
---
# <a name="quickstart-connect-a-microchip-atsame54-xpro-evaluation-kit-to-iot-central"></a>クイックスタート: Microchip ATSAME54-XPro Evaluation kit を IoT Central に接続する

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)<br>
**完了までの合計時間**: 45 分

:::zone pivot="iot-toolset-cmake"
[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO)
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/samples/)
:::zone-end

このクイックスタートでは、Azure RTOS を使用して、Microchip ATSAME54-XPro (以降、Microchip E54) を Azure IoT に接続します。

次のタスクを実行します。

* C で Microchip E54 をプログラミングするための、一連の組み込み開発ツールをインストールする
* イメージをビルドして Microchip E54 にフラッシュする
* Azure IoT Central を使用して、クラウド コンポーネントを作成し、プロパティを表示し、デバイスのテレメトリを表示し、ダイレクト コマンドを呼び出す

:::zone pivot="iot-toolset-cmake"

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC
* リポジトリを複製するための [Git](https://git-scm.com/downloads)
* ハードウェア

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0 A male to Micro USB male ケーブル
  * ワイヤード (有線) イーサネット アクセス
  * イーサネット ケーブル
  * 省略可能: [Weather Click](https://www.mikroe.com/weather-click) センサー。 このセンサーをデバイスに追加して、気象条件を監視できます。 このセンサーをお持ちでない場合でも、このクイックスタートを実行できます。
  * 省略可能: [mikroBUS Xplained Pro](https://www.microchip.com/Developmenttools/ProductDetails/ATMBUSADAPTER-XPRO) アダプター。 このアダプターを使用して、Weather Click センサーを Microchip E54 に接続します。 センサーとこのアダプターをお持ちでない場合でも、このクイックスタートを実行できます。

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
>
> * [CMake](https://cmake.org): ビルド
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): コンパイル
> * [Termite](https://www.compuphase.com/software_termite.htm): 接続されたデバイスのシリアル ポートの出力を監視する

ツールをインストールするには:

1. エクスプローラーで、リポジトリ内の次のパスに移動し、***get-toolchain.bat*** という名前の設定スクリプトを実行します。

    *getting-started\tools\get-toolchain.bat*

1. インストールが完了したら、新しいコンソール ウィンドウを開き、設定スクリプトによって行われた構成の変更を確認します。 このコンソールを使用して、クイックスタートの残りのプログラミング タスクを実行します。 Windows CMD、PowerShell、または Git Bash for Windows を使用できます。
1. 次のコードを実行して、CMake バージョン3.14 以降がインストールされていることを確認します。

    ```shell
    cmake --version
    ```

1. [AVR&reg; と SAM デバイス用の Microchip Studio](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices#) をインストールします。 Microchip Studio は、Microchip E54 をプログラムおよびフラッシュするためのツールを含むデバイスの開発環境です。 このチュートリアルでは、Microchip Studio のみを使用して Microchip E54 をフラッシュします。 インストールには数分かかります。コンポーネントのインストールを承認するように求めるメッセージが数回表示されます。

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>デバイスの準備

Microchip E54 を Azure に接続するには、Azure IoT の設定の構成ファイルを変更し、イメージをリビルドして、イメージをデバイスにフラッシュします。

### <a name="add-configuration"></a>構成を追加する

1. テキスト エディターで次のファイルを開きます。

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Azure IoT デバイス情報の定数を、Azure リソースの作成後に保存した値に設定します。

    |定数名|値|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*自分の ID スコープ値*} |
    | `IOT_DPS_REGISTRATION_ID` | {*自分のデバイス ID 値*} |
    | `IOT_DEVICE_SAS_KEY` | {*自分のプライマリ キー値*} |

1. ファイルを保存して閉じます。

### <a name="connect-the-device"></a>デバイスを接続する

1. Microchip E54 で、**リセット** ボタン、**イーサネット** ポート、**Debug USB (デバッグ USB)** というラベルの付いたマイクロ USB ポートを見つけます。 次の図で各コンポーネントが強調表示されています。

    ![Microchip E54 評価キット ボードで主要なコンポーネントを見つける](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. マイクロ USB ケーブルを Microchip E54 の **Debug USB (デバッグ USB)** ポートに接続し、それからコンピューターに接続します。

    > [!NOTE]
    > 必要に応じて、Microchip E54 の設定と概要の詳細について、「[SAM E54 Xplained Pro User's Guide (SAM E54 Xplained Pro ユーザーズ ガイド)](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf)」を参照してください。

1. イーサネット ケーブルを使用して Microchip E54 をイーサネット ポートに接続します。

### <a name="optional-install-a-weather-sensor"></a>省略可能: 気象センサーをインストールする

Weather Click センサーと mikroBUS Xplained Pro アダプターがある場合は、このセクションの手順に従います。それ以外の場合は、[イメージのビルド](#build-the-image)に進みます。 センサーをお持ちでない場合でも、このクイックスタートを実行できます。 実際のセンサーが存在しない場合、デバイスのサンプル コードを実行するとシミュレートされたデータが返されます。

1. Weather Click センサーと mikroBUS Xplained Pro アダプターがある場合は、次の写真で示されているように Microchip E54 にインストールします。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/sam-e54-sensor.png" alt-text="Microchip ES4 に Weather Click センサーと mikroBUS Xplained Pro アダプターをインストールする":::

1. 前に編集した構成ファイルを再度開きます。

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. ヘッダー ファイルの次のコードのように、定数 `__SENSOR_BME280__` の値を **1** に設定します。 この値を設定すると、デバイスは Weather Click センサーの実際のセンサー データを使用できます。

    `#define __SENSOR_BME280__ 1`

1. ファイルを保存して閉じます。

### <a name="build-the-image"></a>イメージをビルドする

1. コンソールまたはエクスプローラーで、次のパスのスクリプト ***rebuild.bat*** を実行してイメージをビルドします。

    *getting-started\Microchip\ATSAME54-XPRO\tools\rebuild.bat*

1. ビルドが完了したら、次のパスにバイナリ ファイルが作成されたことを確認します。

    *getting-started\Microchip\ATSAME54-XPRO\build\app\atsame54_azure_iot.bin*

### <a name="flash-the-image"></a>イメージをフラッシュする

1. **Windows [スタート] > [Microchip Studio Command Prompt]\(Microchip Studio コマンド プロンプト\)** コンソールを開き、ビルドした Microchip E54 バイナリ ファイルのフォルダーに移動します。

    *getting-started\Microchip\ATSAME54-XPRO\build\app*

1. *atprogram* ユーティリティを使用して、バイナリ イメージで Microchip E54 をフラッシュします。

    ```shell
    atprogram --tool edbg --interface SWD --device ATSAME54P20A program --chiperase --file atsame54_azure_iot.bin --verify
    ```

    > [!NOTE]
    > Microchip E54 で Atmel-ICE ツールと atprogram ツールを使用する方法の詳細については、「[Using Atmel-ICE for AVR Programming In Mass Production (大量生産における AVR プログラミングのための Atmel-ICE の使用)](http://ww1.microchip.com/downloads/en/AppNotes/00002466A.pdf)」を参照してください。

    フラッシュ プロセスが完了すると、コンソールにプログラミングが成功したことが表示されます。

    ```output
    Firmware check OK
    Programming and verification completed successfully.
    ```

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認できます。

1. **Termite** を開始します。

    > [!TIP]
    > フラッシュ後にデバイスを初期化したり、接続したりできない場合、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md) で追加の手順を参照してください。

1. **[設定]** を選択します。

1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port (ポート)** : Microchip E54 が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。
    * **Flow control\(フロー制御\)** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. **[OK]** を選択します。

1. デバイス上の **リセット** ボタンを押します。 このボタンは、デバイス上にラベルが付いていて、マイクロ USB コネクタの近くにあります。

1. **Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.21
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.55.58.103
        SNTP time update: Jun 5, 2021 20:2:46.32 UTC 
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

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC

* ハードウェア

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0 A male to Micro USB male ケーブル
  * ワイヤード (有線) イーサネット アクセス
  * イーサネット ケーブル

* [Termite](https://www.compuphase.com/software_termite.htm)。 当該 Web ページの「**Downloads and license**」で、"complete setup" を選択します。 Termite は、デバイスの出力を監視するために使用する RS232 ターミナルです。

* IAR Embedded Workbench for ARM (EW for ARM)。 [IAR EW for ARM の 14 日間の無料試用版](https://www.iar.com/products/architectures/arm/iar-embedded-workbench-for-arm/)をダウンロードしてインストールできます。

* [Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/rel_6.1_pnp_beta/Azure_RTOS_6.1_PnP_ATSAME54-XPRO_IAR_Sample_2021_03_18.zip) ファイルをダウンロードし、作業ディレクトリに抽出します。 ビルド時にコンパイラ エラーが発生しないように、短いパスのディレクトリを選択します。

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>デバイスの準備

Microchip E54 を Azure に接続するには、Microchip E54 をご使用のコンピューターに接続し、Azure IoT の設定の構成ファイルを変更し、イメージをビルドして、イメージをデバイスにフラッシュします。

### <a name="connect-the-device"></a>デバイスを接続する

1. Microchip E54 で、**リセット** ボタン、**イーサネット** ポート、**Debug USB (デバッグ USB)** というラベルの付いたマイクロ USB ポートを見つけます。 次の図で各コンポーネントが強調表示されています。

    ![Microchip E54 評価キット ボードで主要なコンポーネントを見つける](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. マイクロ USB ケーブルを Microchip E54 の **Debug USB (デバッグ USB)** ポートに接続し、それからコンピューターに接続します。

    > [!NOTE]
    > 必要に応じて、Microchip E54 の設定と概要の詳細について、「[SAM E54 Xplained Pro User's Guide (SAM E54 Xplained Pro ユーザーズ ガイド)](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf)」を参照してください。

1. イーサネット ケーブルを使用して Microchip E54 をイーサネット ポートに接続します。

### <a name="configure-termite"></a>Termite を構成する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認します。 このセクションでは、デバイスのシリアル ポートを監視するように **Termite** を構成します。

1. **Termite** を開始します。

1. **[設定]** を選択します。

1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port (ポート)** : Microchip E54 が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。
    * **Flow control\(フロー制御\)** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. **[OK]** を選択します。

Termite は Microchip E54 から出力を受け取る準備ができました。

### <a name="configure-build-flash-and-run-the-image"></a>イメージの構成、ビルド、フラッシュ、実行

1. ご使用のコンピューターで **IAR EW for ARM** アプリを開きます。

1. **[File]\(ファイル\) > [Open workspace]\(ワークスペースを開く\)** を選択し、zip ファイルを抽出した作業フォルダーから **same54Xpro\iar** フォルダーに移動し、**_azure_rtos.eww_** EWARM ワークスペースを開きます。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-iar.png" alt-text="IAR ワークスペースを開く":::

1. 左側の **[Workspace]\(ワークスペース\)** ウィンドウから **sample_azure_iot_embedded_sdk_pnp** プロジェクトを右クリックして、 **[Set as active]\(アクティブに設定\)** を選択します。

1. サンプルを展開し、**Sample** フォルダーを展開して sample_config.h ファイルを開きます。

1. ファイルの先頭付近にある `#define ENABLE_DPS_SAMPLE` ディレクティブをコメント解除します。

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Azure IoT デバイス情報の定数を、Azure リソースの作成後に保存した値に設定します。 `ENDPOINT` 定数は、Azure Device Provisioning Service (DPS) のグローバル エンドポイントに設定されます。

    |定数名|値|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*自分の ID スコープ値*} |
    | `REGISTRATION_ID` | {*自分のデバイス ID 値*} |
    | `DEVICE_SYMMETRIC_KEY` | {*自分のプライマリ キー値*} |

    > [!NOTE]
    > `ENDPOINT`、`ID_SCOPE`、`REGISTRATION_ID` の値は `#ifndef ENABLE_DPS_SAMPLE` ステートメントに設定されています。 値を `#else` ステートメントに設定するようにしてください。これらは `ENABLE_DPS_SAMPLE` 値が定義されたときに使用されます。

1. ファイルを保存します。

1. **[Project]\(プロジェクト\) > [Batch Build]\(一括ビルド\)** を選択します。 次に、 **[build_all]** と **[Make]** を選択してすべてのプロジェクトをビルドします。 **[Build]\(ビルド\)** ウィンドウにビルド出力が表示されます。 すべてのサンプル プロジェクトのコンパイルとリンクが正常に完了したことを確認します。

1. ツール バーの緑色の **[Download and Debug]\(ダウンロードとデバッグ\)** ボタンを押し、プログラムをダウンロードします。

1. イメージのダウンロードが完了したら、 **[Go]\(実行\)** を選択してサンプルを実行します。

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

:::zone-end
:::zone pivot="iot-toolset-mplab"

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC

* ハードウェア

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0 A male to Micro USB male ケーブル
  * ワイヤード (有線) イーサネット アクセス
  * イーサネット ケーブル

* [Termite](https://www.compuphase.com/software_termite.htm)。 当該 Web ページの「**Downloads and license**」で、"complete setup" を選択します。 Termite は、デバイスの出力を監視するために使用する RS232 ターミナルです。

* [MPLAB X IDE 5.35](https://www.microchip.com/mplab/mplab-x-ide)。

* [MPLAB XC32/32++ Compiler 2.4.0 以降](https://www.microchip.com/mplab/compilers)。

* [Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/rel_6.1_pnp_beta/Azure_RTOS_6.1_PnP_ATSAME54-XPRO_MPLab_Sample_2021_03_18.zip) ファイルをダウンロードし、作業ディレクトリに解凍します。 ビルド時にコンパイラ エラーが発生しないように、短いパスのディレクトリを選択します。

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>デバイスの準備

Microchip E54 を Azure に接続するには、Microchip E54 をご使用のコンピューターに接続し、Azure IoT の設定の構成ファイルを変更し、イメージをビルドして、イメージをデバイスにフラッシュします。

### <a name="connect-the-device"></a>デバイスを接続する

1. Microchip E54 で、**リセット** ボタン、**イーサネット** ポート、**Debug USB (デバッグ USB)** というラベルの付いたマイクロ USB ポートを見つけます。 次の図で各コンポーネントが強調表示されています。

    ![Microchip E54 評価キット ボードで主要なコンポーネントを見つける](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. マイクロ USB ケーブルを Microchip E54 の **Debug USB (デバッグ USB)** ポートに接続し、それからコンピューターに接続します。

    > [!NOTE]
    > 必要に応じて、Microchip E54 の設定と概要の詳細について、「[SAM E54 Xplained Pro User's Guide (SAM E54 Xplained Pro ユーザーズ ガイド)](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf)」を参照してください。

1. イーサネット ケーブルを使用して Microchip E54 をイーサネット ポートに接続します。

### <a name="configure-termite"></a>Termite を構成する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認します。 このセクションでは、デバイスのシリアル ポートを監視するように **Termite** を構成します。

1. **Termite** を開始します。

1. **[設定]** を選択します。

1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port (ポート)** : Microchip E54 が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。
    * **Flow control\(フロー制御\)** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. **[OK]** を選択します。

Termite は Microchip E54 から出力を受け取る準備ができました。

### <a name="configure-build-flash-and-run-the-image"></a>イメージの構成、ビルド、フラッシュ、実行

1. ご使用のコンピューターで **MPLAB X IDE** を開きます。

1. **[File]\(ファイル\) > [Open project]\(プロジェクトを開く\)** を選択します。 プロジェクトを開くダイアログで、zip ファイルを抽出した作業フォルダーから **same54Xpro\mplab** フォルダーに移動します。 すべてのプロジェクトを選択し (**common_hardware_code** および **docs** フォルダーは選択しない)、 **[Open Project]\(プロジェクトを開く\)** を選択します。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-mplab.png" alt-text="MPLab IDE でプロジェクトを開く":::

1. 左側の **[Projects]\(プロジェクト\)** ウィンドウから **sample_azure_iot_embedded_sdk_pnp** プロジェクトを右クリックして、 **[Set as Main Project]\(メイン プロジェクトに設定\)** を選択します。

1. **sample_azure_iot_embedded_sdk_pnp** プロジェクトを展開し、**Header Files** フォルダーを展開して sample_config.h ファイルを開きます。

1. ファイルの先頭付近にある `#define ENABLE_DPS_SAMPLE` ディレクティブをコメント解除します。

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Azure IoT デバイス情報の定数を、Azure リソースの作成後に保存した値に設定します。 `ENDPOINT` 定数は、Azure Device Provisioning Service (DPS) のグローバル エンドポイントに設定されます。

    |定数名|値|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*自分の ID スコープ値*} |
    | `REGISTRATION_ID` | {*自分のデバイス ID 値*} |
    | `DEVICE_SYMMETRIC_KEY` | {*自分のプライマリ キー値*} |

    > [!NOTE]
    > `ENDPOINT`、`ID_SCOPE`、`REGISTRATION_ID` の値は `#ifndef ENABLE_DPS_SAMPLE` ステートメントに設定されています。 値を `#else` ステートメントに設定するようにしてください。これらは `ENABLE_DPS_SAMPLE` 値が定義されたときに使用されます。

1. ファイルを保存します。

1. サンプルをビルドする前に、**sample_azure_iot_embedded_pnp** プロジェクトの依存するライブラリ **threadx**、**netxduo**、**same54_lib** をビルドする必要があります。 各ライブラリをビルドするには、 **[Projects]\(プロジェクト\)** ウィンドウでそのプロジェクトを右クリックして、 **[Build]\(ビルド\)** を選択します。 各ビルドが完了するまで待ってから、次のライブラリに進みます。

1. すべての前提条件ライブラリが正常にビルドされたら、**sample_azure_iot_embedded_pnp** プロジェクトを右クリックして、 **[Build]\(ビルド\)** を選択します。

1. 上部メニューから **[Debug]\(デバッグ\) > [Debug Main Project]\(メイン プロジェクトのデバッグ\)** を選択して、プログラムをダウンロードして開始します。

1. **[Tool not Found]\(ツールが見つかりません\)** というダイアログが表示されたら、 **[connect SAM E54 board]\(SAM E54 ボードに接続する\)** を選択して、 **[OK]** を選択します。

1. プログラムのダウンロードと実行の開始には数分かかる場合があります。 プログラムが正常にダウンロードされて実行されると、MPLAB X IDE の **[Output]\(出力\)** ウィンドウに次の状態が表示されます。

    ```output
    Programming complete
    
    Running
    ```

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

:::zone-end

## <a name="verify-the-device-status"></a>デバイスの状態を確認する

IoT Central ポータルでデバイスの状態を表示するには:

:::zone pivot="iot-toolset-cmake"
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[デバイスの状態]** が *[プロビジョニング済み]* に更新されていることを確認します。
1. **[デバイス テンプレート]** が *[ファースト ステップ ガイド]* に更新されていることを確認します。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status.png" alt-text="IoT Central のデバイスの状態のスクリーンショット":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[デバイスの状態]** が *[プロビジョニング済み]* に更新されていることを確認します。
1. **[デバイス テンプレート]** が *[Thermostat]\(サーモスタット\)* に更新されていることを確認します。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status-iar.png" alt-text="IoT Central のデバイスの状態のスクリーンショット":::
:::zone-end

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT Central を使用すると、デバイスからクラウドへのテレメトリのフローを表示できます。

IoT Central ポータルでテレメトリを表示するには:

1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. デバイスの一覧からデバイスを選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを **[概要]** タブで表示します。

    :::zone pivot="iot-toolset-cmake"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry.png" alt-text="IoT Central のデバイス テレメトリのスクリーンショット":::
    :::zone-end
    :::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry-iar.png" alt-text="IoT Central のデバイス テレメトリのスクリーンショット":::
    :::zone-end

    > [!NOTE]
    > Termite アプリを使用して、デバイスからテレメトリを監視することもできます。

## <a name="call-a-direct-method-on-the-device"></a>デバイス上のダイレクト メソッドを呼び出す

また、IoT Central を使用して、デバイスに実装したダイレクト メソッドを呼び出すこともできます。 ダイレクト メソッドには名前があり、必要に応じて、JSON ペイロード、構成可能な接続、メソッドのタイムアウトを設定できます。 このセクションでは、LED のオンとオフを切り替えることができるメソッドを呼び出します。

IoT Central ポータルでメソッドを呼び出すには:
:::zone pivot="iot-toolset-cmake"

1. [デバイス] ページで、 **[コマンド]** タブを選択します。

1. **[状態]** ドロップダウンで **[True]** を選択し、 **[実行]** を選択します。  LED ライトがオンになります。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method.png" alt-text="デバイスでダイレクト メソッドを呼び出している IoT Central のスクリーンショット":::

1. **[状態]** ドロップダウンで **[False]** を選択し、 **[実行]** を選択します。 LED ライトがオフになります。
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"

1. [デバイス] ページで、 **[コマンド]** タブを選択します。

1. **[次の日時以降]** フィールドで、日付の選択および時間セレクターを使用して時間を設定し、 **[実行]** を選択します。

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method-iar.png" alt-text="デバイスでダイレクト メソッドを呼び出している IoT Central のスクリーンショット":::

1. Termite でコマンド呼び出しを確認できます。

    ```output
    Receive method call: getMaxMinReport, with payload:"2021-10-14T17:45:00.000Z"
    ```

    > [!NOTE]
    > コマンド呼び出しと応答は、IoT Central のデバイス ページの **[生データ]** タブでも表示できます。
:::zone-end

## <a name="view-device-information"></a>デバイス情報を表示する

IoT Central からデバイス情報を表示できます。

[デバイス] ページから **[バージョン情報]** タブを選択します。

:::zone pivot="iot-toolset-cmake"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about.png" alt-text="IoT Central のデバイス情報のスクリーンショット":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about-iar.png" alt-text="IoT Central のデバイス情報のスクリーンショット":::
:::zone-end

## <a name="troubleshoot-and-debug"></a>トラブルシューティングとデバッグ

デバイス コードのビルド、デバイスのフラッシュ、または接続で問題が発生した場合は、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。

:::zone pivot="iot-toolset-cmake"
アプリケーションのデバッグについては、「[Visual Studio Code を使用したデバッグ](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)」を参照してください。
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"
アプリケーションのデバッグのヘルプについては、「**IAR EW for ARM**」の **[Help]\(ヘルプ\)** の選択項目を参照してください。  
:::zone-end
:::zone pivot="iot-toolset-mplab"
アプリケーションのデバッグのヘルプについては、「**MPLAB X IDE**」の **[Help]\(ヘルプ\)** の選択項目を参照してください。  
:::zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成した Azure リソースが不要になったら、IoT Central ポータルからそれらを削除できます。

Azure IoT Central のサンプル アプリケーション全体とそのデバイスおよびリソースをすべて削除するには:

1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure RTOS サンプル コードを含むカスタム イメージをビルドし、そのイメージを Microchip E54 デバイスにフラッシュしました。 また、IoT Central ポータルを使用して、Azure リソースを作成し、Microchip E54 を安全に Azure に接続し、テレメトリを表示し、メッセージを送信しました。

次のステップとして、IoT デバイス SDK を使用してデバイスを Azure IoT に接続する方法の詳細について次の記事を確認してください。 

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Central に接続する](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Hub に接続する](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS には、通信をセキュリティで保護し、基になる MCU/MPU ハードウェア保護メカニズムを使用してコードとデータの分離を作成するためのコンポーネントを含む OEM があります。 ただし、各 OEM は最終的に、そのデバイスが進化するセキュリティ要件を確実に満たすようにする役目を負っています。
