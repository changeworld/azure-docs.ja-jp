---
title: Azure IoT Hub への MXCHIP AZ3166 の接続のクイックスタート
description: Azure RTOS 組み込みソフトウェアを使用して、MXCHIP AZ3166 デバイスを Azure IoT Hub に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/09/2021
ms.openlocfilehash: 36e4a46cbe71b39550c1a6c066670be6008ab1c2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259470"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-hub"></a>クイックスタート: MXCHIP AZ3166 Devkit を IoT Hub に接続する

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)<br>
**完了までの合計時間**: 30 分

[![コードを参照](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

このクイックスタートでは、Azure RTOS を使用して、MXCHIP AZ3166 IoT DevKit (これ以降、MXCHIP DevKit と呼びます) を Azure IoT に接続します。 

また、IoT エクスプローラーと IoT プラグ アンド プレイを使用して MXCHIP DevKit を管理します。 IoT プラグ アンド プレイは、アプリケーションがプログラムでデバイスの機能を照会して操作できるようにするオープン デバイス モデルを提供します。 デバイスは、このモデルを使用して、機能を IoT プラグ アンド プレイ対応アプリケーションにブロードキャストします。 このモデルを使用することで、デバイスの追加、構成、管理のタスクを効率化および強化できます。 詳細については、[IoT プラグ アンド プレイのドキュメント](../iot-develop/index.yml)を参照してください。

次のタスクを実行します。

* C で MXCHIP DevKit をプログラミングするための、一連の組み込み開発ツールをインストールする
* イメージをビルドして MXCHIP DevKit にフラッシュする
* Azure CLI を使用して、MXCHIP DevKit が安全に接続できる Azure IoT ハブを作成し、管理する
* Azure IoT エクスプローラーを使用して、IoT ハブへのデバイスの登録、デバイスのプロパティの表示、デバイス テレメトリの表示、デバイスでのダイレクト コマンドの呼び出しを行う

## <a name="prerequisites"></a>前提条件

* Windows 10 を実行している PC
* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
* リポジトリを複製するための [Git](https://git-scm.com/downloads)
* Azure CLI。 このクイックスタートで Azure CLI コマンドを実行するには、2 つのオプションがあります。
    * ブラウザーで CLI コマンドを実行する対話型シェルである Azure Cloud Shell を使用します。 何もインストールする必要がないため、このオプションをお勧めします。 Cloud Shell を初めて使用する場合は、[Azure portal](https://portal.azure.com) にサインインします。 [Cloud Shell のクイックスタート](../cloud-shell/quickstart.md)の手順に従って、**Cloud Shell を起動** し、**Bash 環境を選択** します。
    * 必要に応じて、お使いのローカル コンピューターで Azure CLI を実行します。 Azure CLI が既にインストールされている場合は、`az upgrade` を実行して、CLI と拡張機能を最新バージョンにアップグレードします。 Azure CLI のインストール方法については、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関する記事を参照してください。

* [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT を監視および管理するためのクロスプラットフォーム ユーティリティ 
* ハードウェア

    * [MXCHIP AZ3166 IoT DevKit](https://www.seeedstudio.com/AZ3166-IOT-Developer-Kit.html) (MXCHIP DevKit)
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
> * [Termite](https://www.compuphase.com/software_termite.htm): 接続されたデバイス リソースのシリアル ポートの出力を監視

ツールをインストールするには:

1. エクスプローラーで、リポジトリ内の次のパスに移動し、*get-toolchain.bat* という名前の設定スクリプトを実行します。

    *getting-started\tools\get-toolchain.bat*

1. インストールが完了したら、新しいコンソール ウィンドウを開き、設定スクリプトによって行われた構成の変更を確認します。 このコンソールを使用して、クイックスタートの残りのプログラミング タスクを実行します。 Windows CMD、PowerShell、または Git Bash for Windows を使用できます。
1. 次のコードを実行して、CMake バージョン3.14 以降がインストールされていることを確認します。

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>クラウド コンポーネントを作成する

### <a name="create-an-iot-hub"></a>IoT Hub の作成

Azure CLI を使用して、デバイスのイベントとメッセージングを処理する IoT ハブを作成できます。

IoT ハブを作成するには:

1. CLI アプリを起動します。 このクイックスタートの以降の部分で CLI コマンドを実行するには、コマンド構文をコピーして CLI アプリに貼り付け、変数の値を編集して Enter キーを押します。
    - Cloud Shell を使用する場合は、[Cloud Shell](https://shell.azure.com/bash) のこのリンクを右クリックし、新しいタブで開くオプションを選択します。
    - Azure CLI をローカルで使用している場合は、CLI コンソール アプリを起動し、Azure CLI にサインインします。

1. [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add&preserve-view=true) を実行して、*azure-iot* 拡張機能をインストールするか、最新バージョンにアップグレードします。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. [az group create](/cli/azure/group#az-group-create) コマンドを実行してリソース グループを作成します。 次のコマンドでは、*MyResourceGroup* という名前のリソース グループが *centralus* リージョンに作成されます。

    > [!NOTE] 
    > 必要に応じて、別の `location` を設定することもできます。 利用可能な場所を確認するには、[az account list-locations](/cli/azure/account#az-account-list-locations) を実行します。

    ```azurecli
    az group create --name MyResourceGroup --location centralus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドを実行して、IoT ハブを作成します。 IoT ハブの作成には数分かかることがあります。

    *YourIotHubName*: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。 IoT ハブ名は Azure でグローバルに一意である必要があります。 このプレースホルダーは、このクイックスタートの残りの部分で、一意の IoT ハブ名を表すために使用されます。

    `--sku F1` パラメーターにより、Free レベルの IoT ハブが作成されます。 Free レベルのハブは機能セットが限られており、概念実証アプリケーションに使用されます。 IoT Hub のレベル、機能、価格の詳細については、「[Azure IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub)」を参照してください。 

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName} --sku F1 --partition-count 2
    ```

1. IoT ハブが作成されたら、コンソールに JSON 出力を表示し、後の手順で使用するために `hostName` 値をコピーします。 `hostName` 値は次の例のようになります。

    `{Your IoT hub name}.azure-devices.net`

### <a name="configure-iot-explorer"></a>IoT エクスプローラーの構成

このクイックスタートの残りの部分では、IoT エクスプローラーを使用して、IoT ハブへのデバイスの登録、デバイスのプロパティとテレメトリの表示、デバイスへのコマンドの送信を行います。 このセクションでは、先ほど作成した IoT ハブに接続し、パブリック モデル リポジトリからプラグ アンド プレイ モデルを読み取るように IoT エクスプローラーを構成します。 

IoT ハブへの接続を追加するには:

1. CLI アプリで [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) コマンドを実行して、IoT ハブの接続文字列を取得します。

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 引用符で囲まずに接続文字列をコピーします。
1. Azure IoT エクスプローラーで、左側のメニューの **[IoT Hub]** を選択し、 **[+ 接続の追加]** を選択します。
1. 接続文字列を **[接続文字列]** ボックスに貼り付けます。
1. **[保存]** を選択します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-connection.png" alt-text="IoT エクスプローラーでの接続の追加のスクリーンショット":::

1. 接続が成功すると、IoT エクスプローラーが **[デバイス]** ビューに切り替わります。

パブリック モデル リポジトリを追加するには:

1. IoT エクスプローラーで、 **[ホーム]** を選択してホーム ビューに戻ります。
1. 左側のメニューで、 **[IoT プラグ アンド プレイの設定]** を選択し、 **[+ 追加]** を選択して、ドロップダウン メニューから **[パブリック リポジトリ]** を選択します。
1. `https://devicemodels.azure.com` に、パブリック モデル リポジトリのエントリが表示されます。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-public-repository.png" alt-text="IoT エクスプローラーでのパブリック モデル リポジトリの追加のスクリーンショット":::

1. **[保存]** を選択します。

### <a name="register-a-device"></a>デバイスの登録

このセクションでは、新しいデバイス インスタンスを作成し、作成した IoT ハブに登録します。 新しく登録したデバイスの接続情報を使用して、後のセクションで物理デバイスを安全に接続します。

デバイスを登録するには:

1. IoT エクスプローラーのホーム ビューで、 **[IoT Hub]** を選択します。
1. 前に追加した接続が表示されます。 接続プロパティの下にある **[View devices in this hub]\(このハブのデバイスを表示\)** を選択します。
1. **[+ 新規]** を選択し、デバイスのデバイス ID (例: *mydevice*) を入力します。 他のプロパティはすべてそのままにしておきます。
1. **[作成]** を選択します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-created.png" alt-text="Azure IoT エクスプローラーのデバイス ID のスクリーンショット":::

1. コピー ボタンを使用して、 **[デバイス ID]** フィールドと **[主キー]** フィールドをコピーし、メモしておきます。

次のセクションに進む前に、次の値をコピーしたことを確認してください。

* `hostName`
* `deviceId`
* `primaryKey`

## <a name="prepare-the-device"></a>デバイスの準備

MXCHIP DevKit を Azure に接続するには、Wi-Fi と Azure IoT の設定の構成ファイルを変更し、イメージをリビルドして、イメージをデバイスにフラッシュします。

### <a name="add-configuration"></a>構成を追加する

1. テキスト エディターで次のファイルを開きます。

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. 次に示すように、ファイルの先頭付近にある次の行をコメントアウトします。

    ```c
    // #define ENABLE_DPS
    ```

1. Wi-Fi 定数をローカル環境の次の値に設定します。

    |定数名|値|
    |-------------|-----|
    |`WIFI_SSID` |{*自分の Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*自分の Wi-Fi パスワード*}|
    |`WIFI_MODE` |{*ファイル内の列挙された Wi-Fi モード値のいずれか*}|

1. Azure IoT デバイス情報の定数を、Azure リソースの作成後に保存した値に設定します。

    |定数名|値|
    |-------------|-----|
    |`IOT_HUB_HOSTNAME` |{*自分の IoT ハブの hostName 値*}|
    |`IOT_DPS_REGISTRATION_ID` |{*自分のデバイス ID 値*}|
    |`IOT_DEVICE_SAS_KEY` |{*自分のプライマリ キー値*}|

1. ファイルを保存して閉じます。

### <a name="build-the-image"></a>イメージをビルドする

1. コンソールまたはエクスプローラーで、次のパスのスクリプト *rebuild.bat* を実行してイメージをビルドします。

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

2. ビルドが完了したら、次のパスにバイナリ ファイルが作成されたことを確認します。

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>イメージをフラッシュする

1. MXCHIP DevKit で、**リセット** ボタンとマイクロ USB ポートを見つけます。 これらのコンポーネントは、次の手順で使用します。 次の図で両方が強調表示されています。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/mxchip-iot-devkit.png" alt-text="MXCHIP DevKit ボードの主要コンポーネントを見つける":::

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
    > Termite を Devkit に接続できない場合は、[ST-LINK ドライバー](https://www.st.com/en/development-tools/stsw-link009.html)をインストールして、もう一度やり直してください。 追加の手順については、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。
1. **[設定]** を選択します。
1. **[Serial port settings]\(シリアルポートの設定\)** ダイアログで、次の設定を確認し、必要に応じて更新します。
    * **Baud rate\(ボー レート\)** : 115,200
    * **Port\(ポート\)** : MXCHIP DevKit が接続されているポート。 ドロップダウンに複数のポート オプションがある場合は、使用する適切なポートを見つけることができます。 Windows の **デバイス マネージャー** を開き、 **[ポート]** を表示して、使用するポートを特定します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/termite-settings.png" alt-text="Termite アプリのシリアル ポート設定のスクリーンショット":::

1. [OK] を選択します。
1. デバイス上の **リセット** ボタンを押します。 このボタンは、デバイス上にラベルが付いていて、マイクロ USB コネクタの近くにあります。
1. **Termite** アプリで、次のチェックポイント値を確認して、デバイスが初期化され、Azure IoT に接続されていることを確認します。

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 157.245.166.169
        SNTP time update: Jun 8, 2021 18:16:50.807 UTC
    SUCCESS: SNTP initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Termite は、次の手順でデバイスの出力を監視するために開いたままにしておきます。

## <a name="view-device-properties"></a>デバイスのプロパティを表示する

Azure IoT エクスプローラーを使用して、デバイスのプロパティを表示および管理できます。 このセクションとそれ以降のセクションでは、IoT エクスプローラーに表示されるプラグ アンド プレイ機能を使用して、MXCHIP DevKit を管理および操作します。 これらの機能は、パブリック モデル リポジトリで MXCHIP DevKit 用に公開されているデバイス モデルに依存します。 このクイックスタートの前の方で、このリポジトリでデバイス モデルを検索するように IoT エクスプローラーを構成しました。 多くの場合、IoT エクスプローラーのデバイス ペインの左側のメニューから同じアクションを選択することで、プラグ アンド プレイを使用しなくても同じアクションを実行できます。ただし、プラグ アンド プレイを使用すると、エクスペリエンスが強化されることが多々あります。 これは、IoT エクスプローラーでは、プラグ アンド プレイ デバイスで指定されたデバイス モデルを読み取り、そのデバイスに固有の情報を提示できるためです。  

IoT エクスプローラーでデバイスの IoT プラグ アンド プレイ コンポーネントにアクセスするには:

1. IoT エクスプローラーのホーム ビューで **[IoT Hub]** を選択し、 **[View devices in this hub]\(このハブのデバイスを表示\)** を選択します。
1. デバイスを選択します。
1. **[IoT プラグ アンド プレイ コンポーネント]** を選択します。
1. **[既定のコンポーネント]** を選択します。 IoT エクスプローラーに、デバイスに実装されている IoT プラグ アンド プレイ コンポーネントが表示されます。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-default-component-view.png" alt-text="IoT エクスプローラーに表示された、MXCHIP DevKit の既定のコンポーネントのスクリーンショット":::

1. **[インターフェイス]** タブで、デバイス モデルの **[説明]** の JSON コンテンツを確認します。 JSON には、デバイス モデルの各 IoT プラグ アンド プレイ コンポーネントの構成の詳細が含まれています。

    IoT エクスプローラーの各タブは、デバイス モデルの IoT プラグ アンド プレイ コンポーネントの 1 つに対応しています。

    | タブ | Type | 名前 | 説明 |
    |---|---|---|---|
    | **Interface** | インターフェイス | `MXCHIP Getting Started Guide` | MXCHIP DevKit のモデル例 |
    | **プロパティ (読み取り専用)** | プロパティ | -- | 現在、このモデルには読み取り専用のプロパティはありません |
    | **プロパティ (書き込み可能)** | プロパティ | `telemetryInterval` | デバイスがテレメトリを送信する間隔 |
    | **コマンド** | コマンド | `setLedState` | LED のオン/オフを切り替えます |
    | **テレメトリ** | 製品利用統計情報 | `temperature` | 摂氏の温度 |

Azure IoT エクスプローラーを使用してデバイスのプロパティを表示するには:

1. **[プロパティ (読み取り専用)]** タブを選択します。現在、デバイス モデルによって公開されている読み取り専用のプロパティはありません。
1. **[プロパティ (書き込み可能)]** タブを選択します。テレメトリが送信される間隔が表示されます。
1. `telemetryInterval` を *5* に変更し、 **[Update desired value]\(必要な値の更新\)** を選択します。 これにより、デバイスでは、この間隔を使用してテレメトリが送信されるようになります。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-set-telemetry-interval.png" alt-text="IoT エクスプローラーでの MXCHIP DevKit のテレメトリの間隔の設定を示すスクリーンショット":::

1. IoT エクスプローラーは通知で応答します。 Termite で更新を監視することもできます。
1. テレメトリの間隔を 10 に戻します。
 
Azure CLI を使用してデバイスのプロパティを表示するには:

1. [az iot hub device-identity show](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show) コマンドを実行します。

    ```azurecli
    az iot hub device-identity show --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. コンソール出力でデバイスのプロパティを調べます。

## <a name="view-telemetry"></a>利用統計情報データを表示する

Azure IoT エクスプローラーを使用すると、デバイスからクラウドへのテレメトリのフローを表示できます。 必要に応じて、Azure CLI を使用して同じタスクを実行することもできます。

Azure IoT エクスプローラーでテレメトリを表示するには:

1. IoT エクスプローラーで、デバイスの **[IoT プラグ アンド プレイ コンポーネント]** (既定のコンポーネント) ペインから、 **[テレメトリ]** タブを選択します。 **[Use built-in event hub]\(組み込みのイベント ハブを使用する\)** が *[はい]* に設定されていることを確認します。
1. **[スタート]** を選択します。
1. デバイスからクラウドにメッセージが送信されるときのテレメトリを表示します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-telemetry.png" alt-text="IoT エクスプローラーのデバイス テレメトリのスクリーンショット":::

    > [!NOTE]
    > Termite アプリを使用して、デバイスからテレメトリを監視することもできます。

1. **[Show modeled events]\(モデル化されたイベントを表示する\)** チェック ボックスをオンにして、デバイス モデルで指定されたデータ形式でイベントを表示します。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-show-modeled-events.png" alt-text="IoT エクスプローラーのモデル化されたテレメトリ イベントのスクリーンショット":::

1. **[停止]** を選択して、イベントの受信を終了します。

Azure CLI を使用してデバイス テレメトリを表示するには:

1. [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) コマンドを実行します。 Azure IoT で以前に作成した、デバイスと IoT ハブの名前を使用します。

    ```azurecli
    az iot hub monitor-events --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. コンソールに JSON 出力を表示します。

    ```json
    {
        "event": {
            "origin": "mydevice",
            "module": "",
            "interface": "dtmi:azurertos:devkit:gsgmxchip;1",
            "component": "",
            "payload": "{\"humidity\":41.21,\"temperature\":31.37,\"pressure\":1005.18}"
        }
    }
    ```

1. Ctrl + C キーを押して監視を終了します。


## <a name="call-a-direct-method-on-the-device"></a>デバイス上のダイレクト メソッドを呼び出す

Azure IoT エクスプローラーを使用して、デバイスに実装したダイレクト メソッドを呼び出すこともできます。 ダイレクト メソッドには名前があり、必要に応じて、JSON ペイロード、構成可能な接続、メソッドのタイムアウトを設定できます。 このセクションでは、LED のオン/オフを切り替えるメソッドを呼び出します。 必要に応じて、Azure CLI を使用して同じタスクを実行することもできます。

Azure IoT エクスプローラーでメソッドを呼び出すには:

1. IoT エクスプローラーで、デバイスの **[IoT プラグ アンド プレイ コンポーネント]** (既定のコンポーネント) ペインから **[コマンド]** タブを選択します。
1. **setLedState** コマンドで、 **[state]** を **[true]** に設定します。
1. **[コマンドの送信]** を選択します。 IoT エクスプローラーに通知が表示され、デバイスの黄色のユーザー LED ライトがオンになります。

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-invoke-method.png" alt-text="IoT エクスプローラーでの setLedState メソッドの呼び出しのスクリーンショット":::

1. **[state]** を **[false]** に設定し、 **[コマンドの送信]** を選択します。 黄色のユーザー LED がオフになります。
1. 必要に応じて、Termite で出力を表示してメソッドの状態を監視することもできます。

Azure CLI を使用してメソッドを呼び出すには:

1. [az iot hub invoke-device-method](/cli/azure/iot/hub#az_iot_hub_invoke_device_method) コマンドを実行し、メソッド名とペイロードを指定します。 このメソッドでは、`method-payload` を `true` に設定すると LED がオンになり、`false` に設定するとオフになります。

    ```azurecli
    az iot hub invoke-device-method --device-id mydevice --method-name setLedState --method-payload true --hub-name {YourIoTHubName}
    ```

    CLI コンソールに、デバイスでのメソッド呼び出しの状態が表示されます。`204` は成功を示しています。

    ```json
    {
        "payload": {},
        "status": 200
    }    
    ```

1. デバイスを調べて LED の状態を確認します。

1. Termite ターミナルを表示して、出力メッセージを確認します。

    ```output
    Receive direct method: setLedState
        Payload: true
    LED is turned ON
    Device twin property sent: {"ledState":true}
    ```

## <a name="troubleshoot-and-debug"></a>トラブルシューティングとデバッグ

デバイス コードのビルド、デバイスのフラッシュ、または接続で問題が発生した場合は、[トラブルシューティング](troubleshoot-embedded-device-quickstarts.md)に関する記事を参照してください。

アプリケーションのデバッグについては、「[Visual Studio Code を使用したデバッグ](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成した Azure リソースが不要になった場合は、Azure CLI を使用して、リソース グループとそのすべてのリソースを削除できます。

> [!IMPORTANT] 
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。

名前でリソース グループを削除するには、以下の手順を実行します。

1. [az group delete](/cli/azure/group#az-group-delete) コマンドを実行します。 これにより、作成したリソース グループ、IoT Hub、デバイスの登録が削除されます。

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```

1. [az group list](/cli/azure/group#az-group-list) コマンドを実行して、リソース グループが削除されていることを確認します。  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure RTOS サンプル コードを含むカスタム イメージをビルドし、そのイメージを MXCHIP DevKit デバイスにフラッシュしました。 また、Azure CLI または IoT エクスプローラーを使用して、Azure リソースを作成し、MXCHIP DevKit を安全に Azure に接続して、テレメトリの表示とメッセージの送信を行いました。

次のステップとして、次の記事を参照し、IoT デバイス SDK を使用してデバイスを Azure IoT に接続する方法の詳細を確認します。 

> [!div class="nextstepaction"]
> [MXCHIP AZ3166 Devkit を IoT Central に接続する](quickstart-devkit-mxchip-az3166.md)
> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Central に接続する](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [シミュレートされたデバイスを IoT Hub に接続する](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS には、通信をセキュリティで保護し、基になる MCU/MPU ハードウェア保護メカニズムを使用してコードとデータの分離を作成するためのコンポーネントを含む OEM があります。 ただし、各 OEM は最終的に、そのデバイスが進化するセキュリティ要件を確実に満たすようにする役目を負っています。
