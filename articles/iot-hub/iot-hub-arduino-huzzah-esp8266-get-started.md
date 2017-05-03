---
title: "ESP8266 をクラウドへ - Feather HUZZAH ESP8266 を Azure IoT Hub に接続する | Microsoft Docs"
description: "Adafruit Feather HUZZAH ESP8266 と呼ばれる Arduino デバイスを、IoT 資産の管理に役立つ Microsoft クラウド サービスである Azure IoT Hub に接続する方法を説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3650f628747f8a9e743711f5c7a175d2a2523565
ms.lasthandoff: 04/12/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather HUZZAH ESP8266 をクラウドの Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22、Feather HUZZAH ESP8266、IoT Hub 間の接続](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>作業内容


Adafruit Feather HUZZAH ESP8266 を、作成する IoT Hub に接続します。 次に、ESP8266 でサンプル アプリケーションを実行して、DHT22 センサーから気温と湿度のデータを収集します。 最後に、センサー データを IoT Hub に送信します。

> [!NOTE]
> 他の ESP8266 ボードを使っている場合でも、以下の手順に従って IoT Hub に接続できます。 ただし、お使いの ESP8266 ボードによっては、`LED_PIN` の再構成が必要になることがあります。 たとえば、AI-Thinker の ESP8266 をお使いであれば `0` から `2` への変更が必要になることがあります。 キットをお持ちでない場合は、 [Azure Web サイト](http://azure.com/iotstarterkits) から入手できます。




## <a name="what-you-learn"></a>学習内容

* IoT Hub を作成し、Feather HUZZAH ESP8266 のデバイスを登録する方法
* センサー付きの Feather HUZZAH ESP8266 を、お使いのコンピューターに接続する方法
* Feather HUZZAH ESP8266 のサンプル アプリケーションを実行して、センサー データを収集する方法
* センサー データを IoT Hub に送信する方法

## <a name="what-you-need"></a>必要なもの

![このチュートリアルに必要なもの](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

この操作を完了するには、Feather HUZZAH ESP8266 スターター キットの次のものが必要です。

* Feather HUZZAH ESP8266 ボード
* マイクロ USB - タイプ A の USB ケーブル

また、開発環境に次のものが必要です。

* Mac PC または Windows か Ubuntu をインストールした PC。
* Feather HUZZAH ESP8266 を接続するワイヤレス ネットワーク。
* 構成ツールをダウンロードするためのインターネット接続。
* [Arduino IDE](https://www.arduino.cc/en/main/software) バージョン1.6.8 以降。 それより前のバージョンでは AzureIoT ライブラリを使用できません。





次のものは、センサーがない場合のオプションです。 シミュレートされたセンサー データを使用するオプションもあります。

* Adafruit DHT22 気温・湿度センサー
* ブレッドボード
* M/M ジャンパー ワイヤ

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>IoT Hub を作成し、Feather HUZZAH ESP8266 のデバイスを登録する

### <a name="to-create-your-iot-hub-in-the-azure-portal-follow-these-steps"></a>Azure Portal で IoT Hub を作成するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
1. **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順にクリックします。

   ![IoT Hub を作成する](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. **[IoT Hub]** ウィンドウで、IoT Hub に必要な情報を入力します。

   ![IoT Hub を作成するための基本情報](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **[名前]**: IoT Hub の名前。 入力した名前が有効である場合は、緑色のチェック マークが表示されます。
   * **[価格とスケールティア]**: このデモでは、無料の F1 レベルを選択します。 [[pricing and scale tier] (価格とスケール レベル)](https://azure.microsoft.com/pricing/details/iot-hub/) を参照してください。
   * **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/resource-group-portal.md)に関する記事をご覧ください。
   * **[場所]**: IoT Hub が作成される場所に最も近い場所を選択します。
   * **[ダッシュボードにピン留めする]**: ダッシュボードから IoT Hub に簡単にアクセスできるようにするには、このオプションをオンにします。

1. **[作成]**をクリックします。 IoT Hub の作成には数分かかります。 **[通知]** ウィンドウで進行状況を確認できます。

   ![[通知] ウィンドウで IoT Hub 作成の進行状況を監視する](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. IoT Hub が作成されたら、ダッシュボードで その IoT Hub をクリックします。 この記事の中で後で使用する **[ホスト名]** をメモしたら、**[共有アクセス ポリシー]** をクリックします。

   ![IoT Hub のホスト名を取得する](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. **[共有アクセス ポリシー]** ウィンドウで、**[iothubowner]** ポリシーをクリックし、IoT Hub の **[接続文字列]** 値をコピーして保存します。 この値は、この記事の中で、後で使用します。 詳細については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。

   ![IoT Hub 接続文字列を取得する](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

IoT Hub の作成は以上です。 **[ホスト名]** と **[接続文字列]** の値を保存したことを確認してください。 この記事の中で、後で使用します。


### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>IoT Hub に Feather HUZZAH ESP8266 のデバイスを登録する

すべての IoT Hub には、その IoT Hub への接続が許可されたデバイスに関する情報を保存する ID レジストリがあります。 デバイスを IoT Hub に接続できるようにするには、デバイスのエントリが IoT Hub の ID レジストリに存在する必要があります。


このセクションでは、*iothub explorer* と呼ばれる CLI ツールを使用します。 このツールを使用して、Feather HUZZAH ESP8266 のデバイスを IoT Hub の ID レジストリに登録します。



> [!NOTE]
> iothub explorer を正しく動作させるには、Node.js 4.x 以降が必要です。

Feather HUZZAH ESP8266 のデバイスを登録するには、次の手順に従います。

1. NPM を含め Node.js の最新の LTS バージョンを[ダウンロード](https://nodejs.org/en/download/)しインストールします。
1. NPM を使用して iothub-explorer をインストールします。

   * Windows 7 以降:

     管理者としてコマンド プロンプトを起動します。 次のコマンドを実行して、iothub-explorer をインストールします。

     ```bash
     npm install -g iothub-explorer
     ```

   * Ubuntu 16.04 以降:

     キーボード ショートカットの Ctrl + Alt + T キーでターミナルを開き、次のコマンドを実行します。

     ```bash
     sudo npm install -g iothub-explorer
     ```

   * MacOS 10.1 以降:

     ターミナルを開き、次のコマンドを実行します。

     ```bash
     npm install -g iothub-explorer
     ```

3. 次のコマンドを実行して、IoT Hub にログインします。

   ```bash
   iothub-explorer login [your IoT hub connection string]
   ```

4. 新しいデバイスを登録します。 次の例では、`deviceID` は `new-device` です。 次のコマンドを実行して、接続文字列を取得します。

   ```bash
   iothub-explorer create new-device --connection-string
   ```

登録したデバイスの接続文字列をメモします。 これは、後で使用します。


> [!NOTE]
> 登録したデバイスの接続文字列を表示するには、`iothub-explorer list` コマンドを実行します。


## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>センサー付きの HUZZAH ESP8266 をコンピューターに接続する
このセクションでは、センサーをボードに接続します。 その後、デバイスをコンピューターに接続して、さらに使用します。
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266 に DHT22 気温・湿度センサーを接続する

次のように、接続にはブレッドボードとジャンパー ワイヤを使用します。 センサーがない場合は、シミュレートされたセンサー データを代わりに使用できますので、このセクションをスキップしてください。

![接続の参照](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


センサー ピンでは次の配線を使用します。


| 開始 (センサー)           | 終了 (ボード)           | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (ピン 31F)            | 3V (ピン 58 H)           | 赤いケーブル     |
| DATA (ピン 32F)           | GPIO 2 (ピン 46A)       | 青いケーブル    |
| GND (ピン 34F)            | GND (ピン 56I)          | 黒いケーブル   |

詳細については、[Adafruit 社の DHT22 センサーの設定に関するページ](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor)と [Adafruit 社の Feather HUZZAH Esp8266 のピン配列に関するページ](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)をご覧ください。





これで、Feather Huzzah ESP8266 が動作中のセンサーに接続されたはずです。

![DHT22 と Feather HUZZAH を接続する](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Feather HUZZAH ESP8266 をコンピューターに接続する

次に示すように、マイクロ USB - タイプ A の USB ケーブルを使用して、Feather HUZZAH ESP8266 をコンピューターに接続します。

![Feather HUZZAH をコンピューターに接続する](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>シリアル ポートのアクセス許可を追加する (Ubuntu のみ)


Ubuntu を使用する場合は、Feather HUZZAH ESP8266 の USB ポートを操作するアクセス許可があることを確認します。 シリアル ポートのアクセス許可を追加するには、次の手順に従います。


1. ターミナルで次のコマンドを実行します。

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   次の出力のどちらかを取得します。

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   この出力では、`uucp` または `dialout` が USB ポートのグループの所有者名になっていることを確認してください。

1. 次のコマンドを実行して、グループにユーザーを追加します。

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` は先ほどの手順で取得したグループの所有者名です。 `<username>` は Ubuntu のユーザー名です。

1. 変更を表示するために、Ubuntu からサインアウトし、もう一度サインインします。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>センサー データを収集し IoT Hub に送信する

このセクションでは、Feather HUZZAH ESP8266 でサンプル アプリケーションをデプロイし実行します。 このサンプル アプリケーションによって、Feather HUZZAH ESP8266 の LED が点滅し、DHT22 センサーから収集された気温と湿度のデータが IoT Hub に送信されます。

### <a name="get-the-sample-application-from-github"></a>サンプル アプリケーションを GitHub から取得する

このサンプル アプリケーションは GitHub でホストされています。 GitHub から、このサンプル アプリケーションが含まれているサンプル リポジトリを複製します。 サンプル リポジトリを複製するには、次の手順に従います。

1. コマンド プロンプトまたはターミナル ウィンドウを開きます。
1. サンプル アプリケーションを格納するフォルダーに移動します。
1. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Arduino IDE の Feather HUZZAH ESP8266 パッケージをインストールします。

1. サンプル アプリケーションが格納されているフォルダーを開きます。
1. Arduino IDE のアプリ フォルダーにある app.ino ファイルを開きます。

   ![Arduino IDE のサンプル アプリケーションを開く](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. Arduino IDE で、**[ファイル]** > **[環境設定]** をクリックします。
1. **[環境設定]** ダイアログ ボックスで、**[追加のボードマネージャのURL]** ボックスの横にあるアイコンをクリックします。
1. ポップアップ ウィンドウで次の URL を入力し、**[OK]** をクリックします。

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Arduino IDE で パッケージの URL を指定する](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. **[環境設定]** ダイアログ ボックスで、**[OK]** をクリックします。
1. 次に**[ツール]** > **[ボード]** > **[ボードマネージャ]** をクリックし、「esp8266」を検索します。

   ESP8266 バージョン 2.2.0 以降がインストールされていることがボード マネージャーに表示されます。

   ![ESP8266 のインストール](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. **[ツール]** > **[ボード]** > **[Adafruit HUZZAH ESP8266]** をクリックします。

### <a name="install-necessary-libraries"></a>必要なライブラリをインストールする

1. Arduino IDE で、**[スケッチ]** > **[ライブラリをインクルード]** > **[ライブラリを管理]** をクリックします。
1. 1 つずつ次のライブラリ名を検索します。 検索したライブラリごとに、**[インストール]** をクリックします。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>DHT22 センサーがない場合

DHT22 センサーがない場合は、サンプル アプリケーションで気温と湿度をシミュレートできます。 シミュレートされたデータを使用するようにサンプル アプリケーションを設定するには、次の手順に従います。

1. `app` フォルダー内の `config.h` ファイルを開きます。
1. 次のコード行を見つけて、値を `false` から `true` に変更します。
   ```c
   define SIMULATED_DATA true
   ```
   ![シミュレートされたデータをサンプル アプリケーションで使用するための構成](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. ファイルを `Control-s` という名前で保存します。

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>サンプル アプリケーションを Feather HUZZAH ESP8266 にデプロイする

1. Arduino IDEで、**[ツール]** > **[ポート]** をクリックし、次に Feather HUZZAH ESP8266 のシリアル ポートをクリックします。
1. **[スケッチ]** > **[マイコンボードに書き込む]** をクリックし、サンプル アプリケーションを Feather HUZZAH ESP8266 にビルドし、デプロイします。

### <a name="enter-your-credentials"></a>資格情報を入力する

アップロードが正常に完了したら、次の手順に従って資格情報を入力します。

1. Arduino IDE で、**[ツール]** > **[シリアルモニタ]** をクリックします。
1. [シリアルモニタ] ウィンドウで、右下隅の 2 つのドロップダウン リストを確認します。
1. 左のドロップダウン リストで **[改行なし]** を選択します。
1. 右のドロップダウン リストで **[115200 baud]** を選択します。
1. [シリアルモニタ] ウィンドウの上部にある入力ボックスで、次の情報を求められた場合は入力して **[送信]** をクリックします。
   * Wi-Fi SSID
   * Wi-Fi パスワード
   * デバイスの接続文字列

> [!Note]
> 資格情報は Feather HUZZAH ESP8266 の EEPROM に格納されています。 Feather HUZZAH ESP8266 ボードのリセット ボタンをクリックすると、資格情報を削除するかどうかサンプル アプリケーションから質問されます。 この情報を消去するには `Y` を入力します。 情報を再度提供することを求められます。

### <a name="verify-the-sample-application-is-running-successfully"></a>サンプル アプリケーションが正常に実行されていることを確認する

[シリアルモニタ] ウィンドウで次のような出力を確認し、Feather HUZZAH ESP8266 の LED が点滅していることを確認できれば、サンプル アプリケーションは正常に実行されています。

![Arduino IDE での最終出力](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>次のステップ

Feather HUZZAH ESP8266 を IoT Hub に接続し、キャプチャしたセンサー データを IoT Hub に送信できるようになりました。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


