---
title: Azure IoT Central への MXCHIP AZ3166 の接続のクイックスタート
description: Azure RTOS 組み込みソフトウェアを使用して、MXCHIP AZ3166 デバイスを Azure IoT に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448604"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>クイックスタート: MXCHIP AZ3166 Devkit を IoT Central に接続する

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)<br>
**完了までの合計時間**: 30 分

[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

このチュートリアルでは、Azure RTOS を使用して、MXCHIP AZ3166 IoT DevKit (以降、MXCHIP DevKit) を Azure IoT に接続します。 この記事は、[Azure IoT 組み込みデバイスの開発の概要](quickstart-device-development.md)のシリーズの一部です。 このシリーズでは、デバイス開発者に Azure RTOS を紹介し、複数のデバイス評価キットを Azure IoT に接続する方法を示します。

次のタスクを実行します。

* C で MXCHIP DevKit をプログラミングするための、一連の組み込み開発ツールをインストールする
* イメージをビルドして MXCHIP DevKit にフラッシュする
* Azure IoT Central を使用して、クラウド コンポーネントを作成し、プロパティを表示し、デバイスのテレメトリを表示し、ダイレクト コマンドを呼び出す

## <a name="prerequisites"></a>前提条件

* Microsoft Windows 10 で実行されている PC
* リポジトリを複製するための [Git](https://git-scm.com/downloads)
* ハードウェア

    > * [MXCHIP AZ3166 IoT DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2.4 GHz
    > * USB 2.0 A male to Micro USB male ケーブル

## <a name="prepare-the-development-environment"></a>開発環境の準備

開発環境を設定するには、まず、チュートリアルで必要なすべての資産を含む GitHub リポジトリを複製します。 次に、一連のプログラミング ツールをインストールします。

### <a name="clone-the-repo-for-the-tutorial"></a>チュートリアル用のリポジトリを複製する

次のリポジトリを複製して、すべてのサンプル デバイス コード、設定スクリプト、オフライン バージョンのドキュメントをダウンロードします。 このリポジトリを別のチュートリアルで複製している場合は、もう一度実行する必要はありません。

リポジトリを複製するには、次のコマンドを実行します。

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>ツールのインストール

複製されたリポジトリには、必要なツールをインストールして構成する設定スクリプトが含まれています。 ファースト ステップ ガイドの別のチュートリアルでこれらのツールをインストールしている場合は、もう一度実行する必要はありません。

> [!NOTE]
> 設定スクリプトでは、次のツールがインストールされます。
> * [CMake](https://cmake.org): ビルド
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): コンパイル
> * [Termite](https://www.compuphase.com/software_termite.htm): 接続されたデバイスのシリアル ポートの出力を監視する

ツールをインストールするには:

1. エクスプローラーで、リポジトリ内の次のパスに移動し、*get-toolchain.bat* という名前の設定スクリプトを実行します。

    > *getting-started\tools\get-toolchain.bat*

1. インストールが完了したら、新しいコンソール ウィンドウを開き、設定スクリプトによって行われた構成の変更を確認します。 このコンソールを使用して、チュートリアルの残りのプログラミング タスクを実行します。 Windows CMD、PowerShell、または Git Bash for Windows を使用できます。
1. 次のコードを実行して、CMake バージョン3.14 以降がインストールされていることを確認します。

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>クラウド コンポーネントを作成する

### <a name="create-the-iot-central-application"></a>Azure IoT Central アプリケーションを作成する

デバイスを Azure IoT に接続する方法は複数あります。 このセクションでは、Azure IoT Central を使用してデバイスを接続する方法について説明します。 IoT Central は、IoT ソリューションの作成と管理のコストと複雑さを軽減する、IoT アプリケーション プラットフォームです。

新しいアプリケーションを作成するには:
1. [Azure IoT Central ポータル](https://apps.azureiotcentral.com/)で、サイド ナビゲーション メニューの **[マイ アプリ]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[カスタム アプリ]** を選択します。
1. アプリケーション名と URL を追加します。
1. 7 日間の試用版をアクティブ化するには、 **[無料]** 価格プランを選択します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Azure IoT Central でカスタム アプリを作成する":::

1. **［作成］** を選択します

    IoT Central によってアプリケーションがプロビジョニングされると、自動的に新しいアプリケーション ダッシュボードにリダイレクトされます。

    > [!NOTE]
    > 既存の IoT Central アプリケーションがある場合は、新しいアプリケーションを作成するのではなく、それを使用してこの記事の手順を実行できます。

### <a name="create-a-new-device"></a>新しいデバイスを作成する

このセクションでは、IoT Central アプリケーション ダッシュボードを使用して、新しいデバイスを作成します。 新しく作成したデバイスの接続情報を使用して、後のセクションで物理デバイスを安全に接続します。

デバイスを作成するには:
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[+ 新規]** を選択して **[新しいデバイスを作成する]** ウィンドウを開きます。
1. [デバイス テンプレート] は **[Unassigned]\(未割り当て\)** のままにしてください。
1. 目的のデバイス名とデバイス ID を入力します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Azure IoT Central でデバイスを作成する":::

1. **[作成]** ボタンを選択します。
1. 新しく作成したデバイスが **[すべてのデバイス]** 一覧に表示されます。  デバイス名を選択すると詳細が表示されます。
1. 右上のメニュー バーで **[接続]** を選択して、次のセクションでデバイスの構成に使用する接続情報を表示します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="デバイス接続の詳細を表示する":::

1. **[接続]** ダイアログに表示される次の接続文字列パラメーターの接続値を確認します。 これらの値は、次の手順で構成ファイルに追加します。

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>デバイスの準備

MXCHIP DevKit を Azure に接続するには、Wi-Fi と Azure IoT の設定の構成ファイルを変更し、イメージをリビルドして、イメージをデバイスにフラッシュします。

### <a name="add-configuration"></a>構成を追加する

1. テキスト エディターで次のファイルを開きます。

    > *getting-started\MXChip\AZ3166\app\azure_config.h*

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

コンソールまたはエクスプローラーで、次のパスのスクリプト *rebuild.bat* を実行してイメージをビルドします。

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

ビルドが完了したら、次のパスにバイナリ ファイルが作成されたことを確認します。

> *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>イメージをフラッシュする

1. MXCHIP DevKit で、**リセット** ボタンとマイクロ USB ポートを見つけます。 これらのコンポーネントは、次の手順で使用します。 次の図で両方が強調表示されています。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="MXCHIP DevKit ボードの主要コンポーネントを見つける":::

1. マイクロ USB ケーブルを MXCHIP DevKit のマイクロ USB ポートに接続し、それをコンピューターに接続します。
1. エクスプローラーで、前のセクションで作成したバイナリ ファイルを見つけます。
1. バイナリ ファイル *mxchip_azure_iot.bin* をコピーします。
1. エクスプローラーで、コンピューターに接続されている MXCHIP DevKit デバイスを見つけます。 このデバイスは、ドライブ ラベル **AZ3166** でシステム上にドライブとして表示されます。
1. バイナリ ファイルを MXCHIP Devkit のルート フォルダーに貼り付けます。 自動的に点滅が始まり、数秒後に完了します。

    > [!NOTE]
    > 点滅のプロセス中、MXCHIP DevKit の緑色の LED が切り替わります。

### <a name="confirm-device-connection-details"></a>デバイス接続の詳細を確認する

**Termite** アプリを使用して、通信を監視し、デバイスが正しく設定されていることを確認できます。

1. **Termite** を開始します。
    > [!TIP]
    > Termite を Devkit に接続できない場合は、[ST-LINK ドライバー](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip)をインストールして、もう一度やり直してください。 追加の手順については、[トラブルシューティング](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)に関する記事を参照してください。
1. **[設定]** を選択します。
1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port\(ポート\)** : MXCHIP DevKit が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Termite アプリで設定を確認する":::

1. [OK] を選択します。
1. デバイス上の **リセット** ボタンを押します。 このボタンは、デバイス上にラベルが付いていて、マイクロ USB コネクタの近くにあります。
1. **Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

## <a name="verify-the-device-status"></a>デバイスの状態を確認する

IoT Central ポータルでデバイスの状態を表示するには:
1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. **[デバイスの状態]** が **[プロビジョニング済み]** に更新されていることを確認します。
1. **[デバイス テンプレート]** が **[ファースト ステップ ガイド]** に更新されていることを確認します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="IoT Central でデバイスの状態を表示する":::

## <a name="view-telemetry"></a>利用統計情報データを表示する

IoT Central を使用すると、デバイスからクラウドへのテレメトリのフローを表示できます。

IoT Central ポータルでテレメトリを表示するには:

1. アプリケーション ダッシュボードで、サイド ナビゲーション メニューの **[デバイス]** を選択します。
1. デバイスの一覧からデバイスを選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを **[概要]** タブで表示します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="デバイス テレメトリを IoT Central で表示する":::

    > [!NOTE]
    > Termite アプリを使用して、デバイスからテレメトリを監視することもできます。

## <a name="call-a-direct-method-on-the-device"></a>デバイス上のダイレクト メソッドを呼び出す

また、IoT Central を使用して、デバイスに実装したダイレクト メソッドを呼び出すこともできます。 ダイレクト メソッドには名前があり、必要に応じて、JSON ペイロード、構成可能な接続、メソッドのタイムアウトを設定できます。 このセクションでは、LED のオンとオフを切り替えることができるメソッドを呼び出します。

IoT Central ポータルでメソッドを呼び出すには:

1. [デバイス] ページで、 **[コマンド]** タブを選択します。
1. **[状態]** を選択し、 **[実行]** を選択します。  LED ライトがオンになります。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="デバイスのダイレクト メソッドを呼び出す":::

1. **[状態]** の選択を解除し、 **[実行]** を選択します。 LED ライトがオフになります。

## <a name="view-device-information"></a>デバイス情報を表示する

IoT Central からデバイス情報を表示できます。

[デバイス] ページから **[バージョン情報]** タブを選択します。

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="IoT Central でデバイスに関する情報を表示する":::

## <a name="debugging"></a>デバッグ

アプリケーションのデバッグについては、「[Visual Studio Code を使用したデバッグ](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した IoT Central リソースが不要になったら、IoT Central ポータルからそれらを削除することができます。 このファースト ステップ ガイドの別のチュートリアルに進む場合、必要に応じて、既に作成したリソースを保持し、再利用することもできます。

Azure IoT Central のサンプル アプリケーション全体とそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure RTOS サンプル コードを含むカスタム イメージをビルドし、そのイメージを MXCHIP DevKit デバイスにフラッシュしました。 また、IoT Central ポータルを使用して、Azure リソースを作成し、MXCHIP DevKit を安全に Azure に接続し、テレメトリを表示し、メッセージを送信しました。

* デバイス開発者にお勧めする次の手順は、[Azure IoT 組み込みデバイス開発の概要](quickstart-device-development.md)のシリーズの他のチュートリアルを参照することです。
* このガイドの手順に従った後、デバイスを初期化または接続するときに問題が発生した場合は、[トラブルシューティング](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)に関する記事を参照してください。
* このチュートリアルのサンプル コードで Azure RTOS コンポーネントを使用する方法の詳細については、「[ファースト ステップ ガイドで Azure RTOS を使用する](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md)」を参照してください。

    > [!IMPORTANT]
    > Azure RTOS には、通信をセキュリティで保護し、基になる MCU/MPU ハードウェア保護メカニズムを使用してコードとデータの分離を作成するためのコンポーネントを含む OEM があります。 ただし、各 OEM は最終的に、そのデバイスが進化するセキュリティ要件を確実に満たすようにする役目を負っています。

