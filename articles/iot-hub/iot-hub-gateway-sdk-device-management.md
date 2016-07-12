<properties
	pageTitle="Gateway SDK を使用したデバイス管理 | Microsoft Azure"
	description="Gateway SDK を使用した場合にデバイス管理を実装する方法について説明する Azure IoT Hub Gateway SDK チュートリアル"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/15/2016"
     ms.author="dobett"/>


# IoT Gateway SDK (ベータ) – Gateway SDK を使用したデバイス管理

このチュートリアルでは、IoT Hub の[デバイス管理][lnk-device-management]機能を [Azure IoT Hub Gateway SDK][lnk-gateway-sdk] と共に使用する方法について説明します。ここでは、Intel Edison Compute Module を使用して、IoT Hub にテレメトリを送信するゲートウェイとシミュレートされたデバイスをホストします。IoT Hub のデバイス管理機能を使用して、Edison ボードでリモート ファームウェア更新を実行します。

このチュートリアルの内容:

- **アーキテクチャ**: デバイス管理サンプルのアーキテクチャに関する重要な情報。

- **ビルドおよび実行**: サンプルをビルドして実行するために必要な手順。

## アーキテクチャ

このチュートリアルでは、IoT Hub に接続されている IoT デバイス ゲートウェイとして機能するように Intel Edison ボードをプロビジョニングします。Edison ボードを IoT Hub で管理できるようにも構成します。次の図には、このチュートリアルで作成するソリューションの主要な要素を示しています。

![Gateway and device management architecture][img-architecture]

### デバイス

このソリューションには、IoT Hub に接続されている IoT デバイスが 3 つあります。

- Edison ボードは、**GW-device** という名前のデバイスです。このチュートリアルでは、IoT Hub のデバイス管理機能を使用して、この物理デバイスのファームウェアを更新します。

- 2 つのシミュレートされたデバイス (**GW-ble1-demo** と **GW-ble2-demo**)。これらのデバイスでは、ゲートウェイ経由で IoT Hub に接続し、温度テレメトリをハブに送信する Bluetooth Low Energy デバイスをシミュレートします。

### ゲートウェイ ソフトウェア

ゲートウェイ ソフトウェアは、Edison ボード上でサービスとして実行されます。2 つのシミュレートされたデバイスは、温度テレメトリを生成します。マッピング モジュールではこれらのシミュレートされたデバイスを IoT Hub に登録されているデバイスにマップし、HTTP モジュールでは IoT Hub エンドポイントとの通信を処理します。[IoT Gateway SDK のシミュレートされたデバイスからの D2C メッセージの送信][lnk-gateway-scenario]に関する記事では、このシナリオについて詳しく説明しています。

### デバイス管理クライアント

[デバイス管理クライアント][lnk-device-management]は、Edison ボード上でサービスとして実行されます。これにより、[ファームウェアの更新][lnk-dm-jobs]、再起動、構成の更新、デバイス プロパティの照会などのリモート ジョブを Edison ボードで実行できます。このチュートリアルでは、Edison ボードでファームウェアの更新を実行する要求をデバイス管理クライアントが受け取って処理します。

### IoT Hub

[Azure IoT Hub][lnk-iot-hub] は、何百万もの IoT デバイスとソリューション バックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。このチュートリアルでは、IoT Hub は次の処理を実行します。

- 3 つのデバイスがクラウドに接続できるようにする。
- シミュレートされたデバイスによって生成されたテレメトリをゲートウェイから受け取る。
- Edison ボードにファームウェア更新要求を送信できるようにする。
- ファームウェア更新ジョブの進行状況を監視する。

### デバイス管理サンプル UI

[デバイス管理サンプル UI][lnk-dm-sample-ui] は、インタラクティブな Web UI で IoT Hub のデバイス管理機能を使用できるようにするサンプル Web アプリケーションです。たとえば、サンプル UI を使用して、IoT Hub に接続されたデバイスを照会し、デバイスにファームウェア更新ジョブを送信できます。このチュートリアルでは、サンプル UI を使用して、Edison ボードにファームウェア更新ジョブを送信し、ジョブが完了するまで進行状況を監視します。

## ビルドと実行

このサンプルを実行するには、IoT Hub ゲートウェイ ソフトウェアとデバイス管理クライアントを含む Edison ボードのカスタム イメージをビルドする必要があります。

チュートリアルを開始する前に、Edison ボードをワイヤレス ネットワークに接続できることを確認する必要があります。Edison ボードを設定するには、それをホスト コンピューターに接続する必要があります。その後、ホスト コンピューターを使用して、作成したカスタム イメージで Edison ボードをフラッシュします。Intel では、次の各オペレーティング システム向けのガイドなど、さまざまなファースト ステップ ガイドを提供しています。

- 「[Get Started with the Intel Edison Development Board on Windows 64-bit (Windows 64 ビットで Intel Edison Development Board を使用する)][lnk-setup-win64]」
- 「[Get Started with the Intel Edison Development Board on Windows 32-bit (Windows 32 ビットで Intel Edison Development Board を使用する)][lnk-setup-win32]」
- 「[Getting Started with the Intel® Edison Board on Linux (Linux で Intel® Edison Board を使用する)][lnk-setup-linux]」

Edison ボードを設定して使用方法を理解するには、これらの "ファースト ステップ" 記事のすべての手順を完了する必要があります。ただし、以下は除きます。

- 最新のファームウェアをフラッシュする。このチュートリアルの一環としてファームウェアを更新するため、この時点でこの手順を完了する必要はありません。
- 最後の手順「Choose IDE (IDE の選択)」は、現在のチュートリアルには必要はありません。

Edison ボードを設定し、ホスト コンピューターに必要なドライバーをインストールしたら、シリアル ターミナルを使用して Edison ボードに接続できることを確認する必要があります。Intel の Web サイトの「[Setting up a serial terminal (シリアル ターミナルを設定する)][lnk-serial-connection]」ページには、Windows や Linux などのホスト オペレーティング システム向けの設定手順へのリンクが用意されています。

また、次のタスクも完了する必要があります。

- Azure サブスクリプションで [IoT Hub を作成する][lnk-create-hub]。このチュートリアルを完了するには、ハブの名前が必要です。Azure サブスクリプションがまだない場合は、[無料アカウント][lnk-free-trial]を取得できます。
- 3 つのデバイス (**GW-ble1-demo**、**GW-ble2-demo**、**GW-device**) を IoT Hub に追加し、その ID とデバイス キーをメモする。[Device Explorer または iothub-explorer][lnk-explorer-tools] ツールを使用して、前の手順で作成した IoT Hub にこれらのデバイスを追加し、キーを取得できます。これらのデバイスの 2 つ (**GW-ble1-demo** と **GW-ble2-demo**) をゲートウェイに接続しているシミュレートされた BLE デバイスとして使用し、Edison ゲートウェイ デバイスを識別するために 1 つのデバイス (**GW-device**) を IoT Hub から管理できるデバイス管理クライアントとして使用します。

### ビルド環境を準備し、カスタム イメージをビルドできることを確認する

Edison ボードのカスタム イメージを作成するには、Linux 環境が必要です。次の手順では、Ubuntu 14.04 を使用していると想定しています。これは、この記事の執筆時点で使用することをお勧めしているプラットフォームです。ホーム パーティションに少なくとも 40 GB の空き容量が必要です。

> [AZURE.NOTE] カスタム イメージをビルドするスクリプトは、4 CPU コアのコンピューターで実行した場合、最大で 6 時間かかります。より多くの CPU コアを持つより強力なコンピューターを使用すると、この時間を短縮できます。

このセクションの手順については、「[Intel Edison Board Support Package (Intel Edison ボード サポート パッケージ)][lnk-inteledison-bsp]」、「[Manually Building Yocto Images for the Intel Edison Board from Source (ソースから Intel Edison ボードの Yocto イメージを手動でビルドする)][lnk-hackgnar]」、「[Creating a Custom Linux Kernel for the Edison (release 2.1) (Edison (リリース 2.1) のカスタム Linux カーネルを作成する)][lnk-shawnhymel]」の各記事を参照しました。

1. Ubuntu 14.04 コンピューターにサインインし、ホーム フォルダーで次のコマンドを実行して、Edison ソース パッケージをダウンロードします。
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. 次のコマンドを使用して、ソース パッケージを圧縮解除してホーム フォルダーに **edison-src** フォルダーを作成し、新しい **edison-src** フォルダーに移動します。
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. 前提条件となるパッケージをインストールします。
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. **edison-src** フォルダーに新しいディレクトリを 2 つ作成します。
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. 次のスクリプトを実行して、ビルド環境をダウンロードします。CPU コアが 5 個以上ある場合は、**--parallel\_make** と **--bb\_number\_thread** スクリプト引数を、利用可能なコア数に設定します。
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Paho git リポジトリの場所を更新します。**~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** ファイルを編集し、URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` を `git://github.com/eclipse/paho.mqtt.c.git` に置き換えます。

7. 次のコマンドを使用して、ビルド環境を初期化します。
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. 次のコマンドを使用して、カスタム イメージをビルドします。このコマンドを初めて実行する場合は、4 CPU コアのコンピューターで最大 6 時間かかります。それ以降の、独自のカスタマイズを追加した後のリビルドは、格段に短時間で実行されます。
    
    ```
    bitbake edison-image
    ```

9. 次のコマンドを実行して、ビルドの最終処理を実行します。
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

これで、Edison ボードをフラッシュするために必要なファイルが **~/edison-src/out/linux64/build/toFlash/** フォルダーに作成されました。

### Gateway SDK をカスタム イメージに追加する

このセクションの手順では、Edison ボードが起動すると IoT ゲートウェイとして機能するように、Gateway SDK をカスタム イメージに追加する手順について説明します。このチュートリアルでは、ゲートウェイには、ゲートウェイが IoT Hub に転送する温度テレメトリを生成する 2 つの Bluetooth Low Energy (BLE) デバイスをシミュレートするモジュールが含まれています。

前のセクションで Edison イメージのビルドに使用したのと同じ Ubuntu 14.04 コンピューターで次の手順を完了します。

1. ホーム フォルダーに Gateway SDK を複製します。
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. IoT Hub に接続し、2 つのデバイスをシミュレートするようにゲートウェイを構成します。**~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** ファイルを編集し、**IoTHubName**、**IoTHubSuffix**、**deviceID**、**deviceKey** の各プレースホルダーを、IoT Hub を構成したときにメモしておいた値に置き換えます。前に作成したデバイス **GW-ble1-demo** と **GW-ble2-demo** を使用します。

3. 新しいレシピを格納するフォルダーを作成します。
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. **azure-iot-field-gateway-sdk.bb** という名前のレシピ ファイルを **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** フォルダーから、先ほど作成した **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** フォルダーにコピーします。ファイルを編集して、2 か所にある `<<userName>>` を現在のユーザー名に置き換えます。

5. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** を編集して、新しいレシピのエントリを追加します。ファイルの末尾に次の行を追加します。
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. これで、**bitbake** コマンドを実行して、新しいレシピだけをビルドし、変更をテストできるようになりました。
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Azure IoT Hub デバイス管理クライアントをカスタム イメージに追加する

このセクションの手順では、IoT Hub から Edison ゲートウェイ デバイスを管理できるように、IoT Hub デバイス管理クライアントをカスタム イメージに追加する手順について説明します。このチュートリアルでは、デバイス管理クライアントには、Edison ゲートウェイ デバイスのファームウェアを更新できるようにするサンプル コードが含まれています。

前のセクションで使用したのと同じ Ubuntu 14.04 コンピューターで次の手順を完了して、ゲートウェイを Edison イメージに追加します。

1. IoT Hub SDK リポジトリの **dmpreview** ブランチをホーム フォルダーに複製します。
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. 新しいレシピを格納する次のフォルダーを作成します。
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. **iotdm-edison-sample.bb** ファイルを **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** フォルダーから **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample** フォルダーにコピーします。

4. **iotdm\_edison\_sample.service** ファイルを **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** フォルダーから **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files** フォルダーにコピーします。

5. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** ファイルを編集して、新しいレシピのエントリを追加します。ファイルの末尾に次の行を追加します。
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

6. Gateway SDK とデバイス管理クライアントはいくつかのライブラリを共有しているため、**~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass** ファイルを編集する必要があります。このファイルの末尾に次の行を追加します。必ず `<your user>` を現在のユーザー名に置き換えます。
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

7. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** ファイルを編集し、ファイルの末尾に次の行を追加して、Edison ボードで WiFi が自動的に起動するように構成します。必ず `<your wifi ssid>` と `<your wifi password>` を WiFi ネットワークの適切な値に置き換えます。
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

8. これで、Gateway SDK とデバイス管理クライアントを含む Edison ボードのイメージをビルドできるようになりました。新しいレシピをビルドし、イメージに追加するだけなので、**bitbake** コマンドは前よりも格段に短時間で実行されます。
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

9. 次のコマンドを実行して、ビルドの最終処理を実行します。
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

これで、Edison ボードをフラッシュするために必要なファイルが **~/edison-src/out/linux64/build/toFlash/** フォルダーに作成されました。

### カスタム イメージで Intel Edison をフラッシュする

ここまでで、IoT Hub デバイス管理クライアントとゲートウェイ ソフトウェアの両方を含むカスタム イメージで Edison ボードをフラッシュできるようになりました。

カスタム イメージのビルドに使用した Ubuntu コンピューターの **toFlash** フォルダーのファイルを、USB ケーブルで Edison ボードに接続しているコンピューターにコピーします。

Windows コンピューターを使用して、USB ケーブルで Edison ボードに接続している場合、**flashall.bat** スクリプトを実行して、Edison をフラッシュします。Linux コンピューターを使用して、USB ケーブルで Edison ボードに接続している場合、**flashall.bat** スクリプトを実行して、Edison をフラッシュします。

フラッシュ プロセスが完了したら、ホスト コンピューターから[シリアル接続][lnk-serial-connection]を使用して、Edison に接続し、**root** としてサインインします。Edison ボードが WiFi ネットワークに接続されていることを確認する必要があります。

### サンプルの実行

**GW-device** デバイスとして IoT Hub に接続するように、Edison ボード上のデバイス管理クライアントを構成する必要があります。テキスト エディター (**vi** や **nano** など) を使用して、Edison の /home/root フォルダーに **.cs** という名前のファイルを作成します。このファイルには、**GW-device** の接続文字列のみを含める必要があります。前にこの接続文字列をメモしていない場合は、[Device Explorer または iothub-explorer][lnk-explorer-tools] ツールを使用して、IoT Hub デバイスのレジストリからこのデバイスの接続文字列を取得できます。

**.cs** ファイルを作成したら、次のコマンドを使用して、Edison ボードを再起動します。

```
reboot -h now
```

Edison が再起動したら、デバイス管理とゲートウェイの両方のサービスが開始され、状態が **OK** であることを確認します。

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

IoT ゲートウェイ サービスに関する問題のトラブルシューティングを行うには、Edison の **/deviceCloudUploadGatewaylog.log** ファイル内のエントリを確認します。また、次のコマンドを使用して、サービスの出力を表示することもできます。

```
systemctl status simulated_device_cloud_upload.service
```

IoT デバイス管理サービスに関する問題のトラブルシューティングを行うには、次のコマンドを使用して、すべての出力を表示します。

```
systemctl status iotdm_edison_sample.service
```

### ファームウェア更新ジョブを開始する

通常、IoT デバイス管理サービスによって要求された Edison のファームウェア更新では、URL からファームウェアを含む zip ファイルをダウンロードします。このチュートリアルを簡略化するために、zip ファイルを Edison ボードに手動でコピーし、更新の要求時に、http://** URL の**代わりに、file://** URL** を使用します。

ここでも、このチュートリアルを簡略化するために、まったく新しいイメージを使用する代わりに、同じファームウェア イメージを再適用します。このイメージが Edison ボードに適用されていることを確認できます。

カスタム イメージの作成に使用した Ubuntu コンピューターの **toFlash** フォルダーのすべてのファイルとサブフォルダーを含む、**edison.zip** という zip ファイルを作成します。**toFlash** フォルダーのファイルが zip ファイルのルートに含まれていることを確認します。SCP (Putty を使用する場合は PSCP) などのツールを使用して、**edison.zip** ファイルを Edison ボードの /home/root フォルダーにコピーします。

ファームウェア更新ジョブを送信し、進行状況を監視するために、Node.js [デバイス管理サンプル UI][lnk-dm-sample-ui] を使用します。このサンプルは、Windows または Linux で実行でき、[Node.js][lnk-nodejs] 6.1.0 以上が必要です。デスクトップ コンピューターでデバイス管理サンプル UI を取得、ビルド、実行するには、次の手順に従います。

1. **コマンド プロンプト**を開きます。

2. 「`node --version`」と入力して、Node.js 6.1.0 以上がインストールされていることを確認します。

3. 次のコマンドを実行して、Azure IoT デバイス管理 UI の GitHub リポジトリを複製します。

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. 複製した Azure IoT デバイス管理 UI リポジトリのコピーのルート フォルダーで、次のコマンドを実行して依存パッケージを取得します。

    ```
    npm install
    ```

5. npm インストール コマンドが完了したら、次のコマンドを実行してコードをビルドします。

    ```
    npm run build
    ```

6. テキスト エディターで、複製したフォルダーのルートにある user-config.json ファイルを開きます。テキスト "&lt;YOUR CONNECTION STRING HERE&gt;" を IoT Hub の接続文字列に置き換えます。Azure [ポータル][lnk-azure-portal]を使用して、この接続文字列を検索することができます。

7. コマンド プロンプトで次のコマンドを実行して、デバイス管理 UX アプリを起動します。

    ```
    npm run start
    ```

8. コマンド プロンプトに "Services have started" と表示されたら、Web ブラウザーを開き、次の URL にあるデバイス管理アプリに移動して、デバイスを確認します: <http://127.0.0.1:3003>。

    ![Device management UI][img-dm-ui]

9. **GW-device** デバイスを選択し、**[Device Jobs (デバイス ジョブ)]** ボックスの一覧の **[Firmware Update (ファームウェアの更新)]** を選択して、**[Start (開始)]** をクリックします。

10. Edison ボードに以前にコピーしたイメージ ファイルを使用するために、**[Package URI (パッケージ URI)]** フィールドに「**file:///home/root/edison.zip**」と入力します。**[Submit (送信)]**、**[Yes (はい)]**、**[Job History (ジョブ履歴)]** リンクの順にクリックして、新しい親ジョブと子ジョブが実行されていることを確認します。

    ![Job history link][img-history-link]

11. 数分後、Edison ボードに接続されているシリアル ターミナルに、Edison が再起動し、ファームウェアの変更が適用されたことが表示されます。

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Edison の再起動が完了したら、デバイス管理サンプル UI のページを最新の情報に更新して、2 つのファームウェア更新ジョブの状態が **[completed (完了済み)]** になっていることを確認します。

    ![Job status complete][img-job-status]

これで、Intel Edison ボードで IoT Hub ゲートウェイ ソフトウェアとデバイス管理クライアントを使用する方法を説明するチュートリアルが完了しました。このチュートリアルでは、次を実行しました。

- Edison ボードが起動するたびに起動するように構成された IoT Hub デバイス管理クライアントとゲートウェイ ソフトウェアを含むカスタム Intel Edison イメージを作成しました。
- IoT Hub に接続するように Edison ボードとデバイス管理クライアントを構成しました。
- Edison ボードを再起動し、新しいファームウェア イメージを適用するデバイス管理ジョブをサンプル UI から開始しました。

## 次のステップ

IoT Hub とサンプル UI を使用したデバイス管理の詳細については、「[Azure IoT Hub デバイス管理の概要][lnk-device-management]」を参照してください。

IoT Hub に物理デバイスを接続する方法の詳細については、「[IoT Gateway SDK (ベータ) - Linux を使用した実際のデバイスで D2C メッセージを送信する][lnk-gateway-physical]」を参照してください。



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0629_2016-->