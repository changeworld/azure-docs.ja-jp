---
title: IoT DevKit AZ3166 を Azure IoT Hub に接続する
description: このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/29/2021
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 4478d915f854a6272bf4d61b08ce692c310bb2c3
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109655100"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 を Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 このキットには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープンソース ボード パッケージ、および充実した[サンプル ギャラリー](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-learn"></a>学習内容

* IoT ハブを作成し、MXChip IoT DevKit のデバイスを登録する方法。
* IoT DevKit を Wi-Fi に接続して、IoT ハブの接続文字列を構成する方法。
* IoT ハブに DevKit センサーのテレメトリ データを送信する方法。
* 開発環境を準備し、IoT DevKit 用のアプリケーションを開発する方法。

DevKit をお持ちでない場合は、 [DevKit シミュレーター](https://azure-samples.github.io/iot-devkit-web-simulator/)を使用するか、[DevKit を購入](https://aka.ms/iot-devkit-purchase)してください。

DevKit のすべてのチュートリアルのソース コードは、[コード サンプル ギャラリー](/samples/browse/?term=mxchip)に関するページにあります。

## <a name="what-you-need"></a>必要なもの

- MXChip IoT DevKit ボードとマイクロ USB ケーブル。 [こちら](https://aka.ms/iot-devkit-purchase)から今すぐ入手できます。
- Windows 10、macOS 10.10 以上、または Ubuntu 18.04 以上が実行されているコンピューター。
- 有効な Azure サブスクリプション [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにします。
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
 
## <a name="prepare-the-development-environment"></a>開発環境の準備

次の手順に従って、DevKit の開発環境を準備します。

#### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Visual Studio Code と Azure IoT Tools 拡張機能パッケージをインストールする

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software) をインストールします。 Arduino コードをコンパイルしたりアップロードしたりするために必要なツールチェーンが備わっています。
    * **Windows**:Windows インストーラー バージョンを使用してください。 App Store からはインストールしないでください。
    * **macOS**:抽出した **Arduino.app** を `/Applications` フォルダーにドラッグ アンド ドロップします。
    * **Ubuntu**:`$HOME/Downloads/arduino-1.8.8` などのフォルダーにファイルを解凍します

2. 強力な IntelliSense、コード補完、デバッグ サポート、および豊富な拡張機能などを備えたクロス プラットフォームのソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) を、マーケットプレースからインストールできます。

3. VS Code を起動し、拡張機能マーケットプレースから **Arduino** を探してインストールします。 この拡張機能は、Arduino プラットフォームでの開発用に強化されたエクスペリエンスを提供します。

    ![Arduino のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 拡張機能マーケットプレースから [Azure IoT Tools](https://aka.ms/azure-iot-tools) を探してインストールします。

    ![拡張機能マーケットプレースでの Azure IoT Tools を示すスクリーンショット。](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    または、この URL をコピーしてブラウザー ウィンドウに貼り付けます: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Azure IoT Tools 拡張パックには、[Azure IoT Device Workbench](https://aka.ms/iot-workbench) が含まれています。このワークベンチは、さまざまな IoT DevKit デバイスの開発とデバッグに使用されます。 [Azure IoT Hub 拡張機能](https://aka.ms/iot-toolkit)は Azure IoT Tools 拡張パックにも含まれていますが、Azure IoT Hubs を管理し、それとやりとりする目的で使用されます。

5. Arduino 設定で VS Code を構成します。

    Visual Studio Code で、 **[ファイル] > [基本設定] > [設定]** (macOS では、 **[Code] > [基本設定] > [設定]** ) の順にクリックします。 次に、  *[設定]* ページの右上隅にある **[設定を開く (JSON)]** アイコンをクリックします。

    ![Azure IoT Tools のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Arduino のインストールのための正しいパスは VS Code で構成する必要があります。 プラットフォームと Arduino IDE をインストールしたディレクトリ パスに応じて、次の行を追加して Arduino を構成してください。 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        下の **{username}** プレースホルダーはユーザー名に置き換えてください。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.13",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. `F1` をクリックしてコマンド パレットを開き、**Arduino: ボード マネージャー** を入力して選択します。 **AZ3166** を検索し、最新バージョンをインストールします。

    ![DevKit SDK のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

#### <a name="install-st-link-drivers"></a>ST-Link のドライバーのインストール

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) は、IoT DevKit が開発マシンとの通信に使用する USB インターフェイスです。 コンパイル済みのデバイス コードを DevKit にフラッシュするには、これを Windows 上にインストールする必要があります。 OS ごとの手順に従って、対象デバイスに対するマシンのアクセスを許可してください。

* **Windows**:[STMicroelectronics Web サイト](https://www.st.com/en/development-tools/stsw-link009.html)から USB ドライバーをダウンロードしてインストールします。
* **macOS**:macOS の場合、ドライバーは必要ありません。
* **Ubuntu**:ターミナルからコマンドを実行し、グループの変更を反映するために一度サインアウトしてからサインインします。

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

これで必要な開発環境の準備と構成がすべて完了しました。

## <a name="prepare-azure-resources"></a>Azure リソースを準備する

この記事では、IoT Hub を作成し、ハブを使用するようにデバイスを登録する必要があります。 次のサブセクションでは、Azure CLI を使用してリソースを作成する方法について説明します。  

Azure IoT Tools の拡張機能を使用して、IoT Hub を作成し、Visual Studio Code 内にデバイスを登録することもできます。 VS Code 内にハブとデバイスを作成する方法の詳細については、[VS Code 用の Azure IoT Tools の使用](iot-hub-create-use-iot-toolkit.md)に関する記事を参照してください。

#### <a name="create-an-iot-hub"></a>IoT Hub の作成

まだ IoT Hub を作成していない場合は、「[Azure CLI を使用して IoT Hub を作成する](iot-hub-create-using-cli.md)」の手順に従います。

#### <a name="register-a-device"></a>デバイスの登録

デバイスを接続するには、あらかじめ IoT ハブの IoT DevKit に登録しておく必要があります。 新しいデバイスを登録するには、次の Azure Cloud Shell 用の手順に従ってください。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **AZ3166Device**: 登録するデバイスの名前。 この記事の例では、ご覧のように **AZ3166Device** を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新する必要があります。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id AZ3166Device
    ```

   > [!NOTE]
   > `device-identity` の実行中にエラーが発生する場合は、[Azure IoT Extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) インストールします。
   > 次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Extension により、IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) に固有のコマンドが Azure CLI に追加されます。
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id AZ3166Device --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=AZ3166Device;SharedAccessKey={YourSharedAccessKey}`

    この値は後で使用します。


## <a name="prepare-your-hardware"></a>ハードウェアの準備

次のハードウェアをコンピューターに接続します。

* DevKit ボード
* マイクロ USB ケーブル

![必要なハードウェア](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit をコンピューターに接続するには、次の手順を実行します。

1. USB ケーブルの端部をコンピューターに接続します。

2. マイクロ USB の端部を DevKit に接続します。

3. 電源の緑色の LED で、接続されていることを確認できます。

   ![ハードウェアの接続](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

#### <a name="update-firmware"></a>ファームウェアを更新する

DevKit Get Started ファームウェア (`devkit-getstarted-*.*.*.bin`) は、IoT ハブ上のデバイス固有のエンドポイントに接続し、温度と湿度のテレメトリを送信します。 

1. IoT DevKit 用の最新バージョンの [GetStarted ファームウェア (devkit-getstarted- *.* .*.bin)](https://github.com/microsoft/devkit-sdk/releases/) をダウンロードします。 この更新の時点では、最新のファイル名は **devkit-getstarted-2.0.0.bin** です。

1. IoT DevKit が USB 経由でコンピューターに接続していることを確認します。 エクスプローラーを開くと、**AZ3166** という名前の USB 大容量記憶装置があります。

    ![エクスプローラーを開く](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. ダウンロードしたファームウェアをドラッグして大容量記憶装置にドロップすると、自動的にフラッシュされます。

    ![ファームウェアをコピーする](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit で、**B** ボタンを押したままにしておきます。 **リセット** ボタンを押して離します。 その後、ボタン **B** を離します。DevKit が AP モードになります。 確認するには、DevKit のサービス セット識別子 (SSID) と、構成ポータルの IP アドレスが画面に表示されます。

    ![リセット ボタン、B ボタン、および SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP モードの設定](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 別の Wi-Fi 対応デバイス (コンピューターまたは携帯電話) の Web ブラウザーを使って、前のステップで表示されている IoT DevKit SSID に接続します。 パスワードの入力を求められたら、空白のままにします。

    ![SSID に接続する](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. ブラウザーで **192.168.0.1** を開きます。 IoT DevKit を接続する Wi-Fi を選択し、Wi-Fi のパスワードを入力して、前に記録しておいたデバイスの接続文字列を貼り付けます。 **[Configure Device]** \(デバイスの構成\) をクリックします。

    ![構成 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit では、2.4 GHz のネットワークのみがサポートされます。 詳細については、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) を参照してください。

1. 結果ページを表示すると、Wi-Fi の情報とデバイスの接続文字列が IoT DevKit に保存されます。

    ![構成の結果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。

1. IoT DevKit は数秒で再起動します。 DevKit の画面に、DevKit の IP アドレスに続いて、温度と湿度の値および Azure IoT Hub に送信されたメッセージ数を含むテレメトリ データが表示されます。

    ![Wi-Fi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![データの送信](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure に送信されたテレメトリ データを確認するには、Azure Cloud Shell で次のコマンドを実行します。

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    このコマンドは、IoT Hub に送信されたデバイスからクラウド (D2C) へのメッセージを監視します

## <a name="build-your-first-project"></a>初めてのプロジェクトを作成する

#### <a name="open-sample-code-from-sample-gallery"></a>サンプル ギャラリーからサンプル コードを開く

IoT DevKit には豊富なサンプルのギャラリーが含まれており、それを使って、さまざまな Azure サービスへの DevKit の接続を学習できます。

1. IoT DevKit がお使いのコンピューターに接続されて **いない** ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Open Examples...]** \(Azure IoT Device Workbench: 例を開く...\)を入力して選択します。次に、 **[MXChip IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[Get Started]\(はじめに\)** を探して **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、既定のパスを選択してサンプル コードをダウンロードします。

    ![サンプルを開く](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)


#### <a name="review-the-code"></a>コードの確認

`GetStarted.ino` は Arduino のメイン スケッチ ファイルです。

![D2C メッセージ](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

デバイス テレメトリが Azure IoT Hub に送信されるしくみを見るには、同じフォルダーで `utility.cpp` ファイルを開きます。 [API リファレンス](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)を見ると、IoT DevKit でセンサーや周辺機器を使用する方法がわかります。

使用されている `DevKitMQTTClient` は **iothub_client** のラッパーであり、Azure IoT Hub とやりとりします。iothub_client の詳細は「[Microsoft Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)」 (C 向けの Microsoft Azure IoT SDK およびライブラリ) にあります。


#### <a name="configure-and-compile-device-code"></a>デバイス コードを構成し、コンパイルする

1. 右下のステータス バーで、選択したボードとして **MXCHIP AZ3166** が表示されていること、またシリアル ポートと **STMicroelectronics** が使用されていることを確認します。

    ![ボードと COM の選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Configure Device Settings...]** \(Azure IoT Workbench: デバイス設定の構成\) を入力して選択し、 **[Config Device Connection String]** \(デバイス接続文字列の構成\) を選択します。 次に、IoT デバイスの接続文字列を貼り付けます。  

1. DevKit で **ボタン A** を押しながら、**リセット** ボタンを押して離した後、**ボタン A** を離します。DevKit が構成モードに移行し、接続文字列が保存されます。

    ![接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1` をもう一度クリックし、 **[Azure IoT Device Workbench: Upload Device Code]\(デバイス コードのアップロード\)** を入力して選択します。 これにより、コンパイルと DevKit へのコードのアップロードが開始されます。

    ![Arduino のアップロード](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit が再起動され、コードの実行が開始されます。 DevKit は、ハブへのテレメトリの送信を開始します。

> [!NOTE]
> エラーや中断が生じた場合は、再度コマンドを実行することにより、いつでも復旧できます。


## <a name="use-the-serial-monitor"></a>シリアル モニターの使用

VS Code のシリアル モニターは、コードから送信されたログ情報を表示するのに役立ちます。 このログ情報は、`LogInfo()` API を使用して生成されます。  これはデバッグに便利です。 

1. ステータス バーの電源プラグ アイコンをクリックして、シリアル モニターを開きます。

    ![シリアル モニター](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

2. 次の結果が表示された場合、サンプル アプリケーションは正常に実行されてます。

    * IoT Hub に送信されたメッセージがシリアル モニターに表示されます。
    * MXChip IoT DevKit の LED が点滅している。
    
    ![シリアル モニターの出力](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)
    
    > [!NOTE]
    > テスト中に、LED が点滅せず、Azure portal にデバイスから受信したデータが表示されないにもかかわらず、デバイスの OLED 画面には "**実行中**" と表示されるエラーが発生することがあります。この問題を解決するには、Azure portal で IoT ハブのデバイスに移動し、デバイスにメッセージを送信します。 VS Code のシリアル モニターで次の応答が表示される場合は、デバイスからの直接通信がルーター レベルでブロックされている可能性があります。 接続中のデバイスに構成されているファイアウォールとルーターのルールを確認してください。 また、送信ポート 1833 が開いていることを確認してください。
    > 
    > ERROR: mqtt_client.c (ln 454):Error: failure opening connection to endpoint  
    > INFO: >>>Connection status: disconnected  
    > ERROR: tlsio_mbedtls.c (ln 604):Underlying IO open failed  
    > ERROR: mqtt_client.c (ln 1042):Error: io_open failed  
    > ERROR: iothubtransport_mqtt_common.c (ln 2283): failure connecting to address atcsliothub.azure-devices.net.  
    > INFO: >>>Re-connect.  
    > INFO:IoThub Version:1.3.6  

3. DevKit でボタン **A** を押しながら、**リセット** ボタンを押して離した後、ボタン **A** を離します。DevKit はテレメトリの送信を停止し、構成モードに移行します。 構成モードの完全なコマンド一覧は、VS Code のシリアル モニターの出力ウィンドウに表示されます。

    ```output
    ************************************************
    ** MXChip - Microsoft IoT Developer Kit **
    ************************************************
    Configuration console:
     - help: Help document.
     - version: System version.
     - exit: Exit and reboot.
     - scan: Scan Wi-Fi AP.
     - set_wifissid: Set Wi-Fi SSID.
     - set_wifipwd: Set Wi-Fi password.
     - set_az_iothub: Set IoT Hub device connection string.
     - set_dps_uds: Set DPS Unique Device Secret (UDS) for X.509 certificates..
     - set_az_iotdps: Set DPS Symmetric Key. Format: "DPSEndpoint=global.azure-devices-provisioning.net;IdScope=XXX;DeviceId=XXX;SymmetricKey=XXX".
     - enable_secure: Enable secure channel between AZ3166 and secure chip.
     ```

    このモードでは、使用する IoT Hub デバイスの接続文字列の変更などのコマンドがサポートされています。 シリアル モニターでテキスト コマンドを送信するには、`F1` を押して **[Arduino: Send Text to Serial Port]** \(Arduino: シリアル ポートにテキストを送信\) を選択します。

4. DevKit で、**リセット** ボタンを押して離します。 これによりデバイスが再起動し、テレメトリの送信を再開できるようになります。
    
## <a name="use-vs-code-to-view-hub-telemetry"></a>VS Code を使用してハブのテレメトリを表示する

「[ハードウェアの準備](#prepare-your-hardware)」セクションの最後で、Azure CLI を使用して IoT Hub のデバイスからクラウド (D2C) へのメッセージを監視しました。 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して、VS Code のデバイスからクラウド (D2C) へのメッセージを監視することもできます。

1. [Azure portal](https://portal.azure.com/) にサインインし、作成した IoT Hub を検索します。

    ![Azure portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **[共有アクセス ポリシー]** ウィンドウで **iothubowner** ポリシーをクリックし、IoT Hub の接続文字列をコピーします。

    ![Azure IoT Hub 接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code で、`F1` をクリックし、 **[Azure IoT Hub:IoT Hub 接続文字列を設定する]** を入力して選択します。 IoT Hub の接続文字列をフィールドに貼り付けます。

    ![Azure IoT Hub 接続文字列の設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 左側の **[AZURE IOT HUB]** ウィンドウを展開し、作成したデバイス名を右クリックして、 **[Start Monitoring Built-in Event Endpoint]** \(組み込みイベント エンドポイントの監視の開始\) を選択します。

    ![D2C メッセージの監視](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

    組み込みエンドポイントの入力を求めるメッセージが表示される場合は、Azure portal の IoT Hub の **[組み込みエンドポイント]** をクリックして表示される **[イベント ハブ互換エンドポイント]** を使用します。 

1. **[出力]** ウィンドウで、IoT Hub への受信 D2C メッセージを確認できます。 

    **Azure IoT Hub** を選択して、出力が正しくフィルター処理されていることを確認します。 このフィルター処理を使用して、ハブの監視とシリアル モニターの出力を切り替えることができます。

    ![IoT Hub への受信 D2C メッセージを示すスクリーンショット。](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)


## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) で解決方法を確認するか、[Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) からお問い合わせください。 このページにコメントを残してフィードバックをお寄せいただくこともできます。

## <a name="next-steps"></a>次のステップ

MXChip IoT DevKit を IoT ハブに接続し、キャプチャしたセンサー データを IoT ハブに送信しました。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
