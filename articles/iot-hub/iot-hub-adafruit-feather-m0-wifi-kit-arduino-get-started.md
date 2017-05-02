---
title: "M0 をクラウドへ - Feather M0 WiFi を Azure IoT Hub に接続する | Microsoft Docs"
description: "Adafruit Feather M0 WiFi と呼ばれる Arduino デバイスを、IoT 資産の管理に役立つ Microsoft クラウド サービスである Azure IoT Hub に接続する方法を説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e2d44f821635ce9d91b67ecdc0653e2ba9c99b01
ms.lasthandoff: 04/13/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather M0 WiFi をクラウドの Azure IoT Hub に接続する
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![BME280、Feather M0 WiFi、および IoT Hub 間の接続](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

このチュートリアルでは、Arduino ボードの操作の基本を学習することから始めます。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。

## <a name="what-you-do"></a>作業内容

Adafruit Feather M0 WiFi を、作成する IoT Hub に接続します。 次に、BME280 から気温と湿度のデータを収集するために、M0 WiFi のサンプル アプリケーションを実行します。 最後に、センサー データを IoT Hub に送信します。



## <a name="what-you-learn"></a>学習内容

* IoT Hub を作成し、Feather M0 WiFi のデバイスを登録する方法
* センサー付きの Feather M0 WiFi を、お使いのコンピューターに接続する方法
* Feather M0 WiFi のサンプル アプリケーションを実行して、センサー データを収集する方法
* センサー データを IoT Hub に送信する方法

## <a name="what-you-need"></a>必要なもの

![このチュートリアルに必要なもの](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

この操作を完了するには、Feather M0 WiFi スターター キットの次のものが必要です。

* Feather M0 WiFi ボード
* マイクロ USB - タイプ A の USB ケーブル

また、開発環境に次のものが必要です。

* Mac PC または Windows か Ubuntu をインストールした PC。
* Feather M0 WiFi を接続するワイヤレス ネットワーク。
* 構成ツールをダウンロードするためのインターネット接続。
* [Arduino IDE](https://www.arduino.cc/en/main/software) バージョン1.6.8 以降。 それより前のバージョンでは AzureIoT ライブラリを使用できません。


次のものは、センサーがない場合のオプションです。 シミュレートされたセンサー データを使用するオプションもあります。

* BME280 気温・湿度センサー
* ブレッドボード
* M/M ジャンパー ワイヤ

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>センサー付きの Feather M0 WiFi をコンピューターに接続する
このセクションでは、センサーをボードに接続します。 その後、デバイスをコンピューターに接続して、さらに使用します。
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Feather M0 WiFi に DHT22 気温・湿度センサーを接続する

次のように、接続にはブレッドボードとジャンパー ワイヤを使用します。 センサーがない場合は、シミュレートされたセンサー データを代わりに使用できますので、このセクションをスキップしてください。

![接続の参照](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


センサーの各ピンで、次のように接続します。


| 開始 (センサー)           | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (ピン 27A)            | 3V (ピン 3A)            | 赤いケーブル     |
| GND (ピン 29A)            | GND (ピン 6A)           | 黒いケーブル   |
| SCK (ピン 30A)            | SCK (ピン 12A)          | 黄色のケーブル  |
| SDO (ピン 31A)            | MI (ピン 14A)           | 白いケーブル   |
| SDI (ピン 32A)            | M0 (ピン 13A)           | 青いケーブル    |
| CS (ピン 33A)             | GPIO 5 (ピン 15J)       | オレンジ色のケーブル  |

詳細については、[Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all)および[Adafruit Feather M0 WiFi Pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts)を参照してください。



これで、Feather M0 WiFi が動作中のセンサーに接続されたはずです。

![DHT22 と Feather Huzzah を接続する](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Feather M0 WiFi をコンピューターに接続する

次に示すように、マイクロ USB - タイプ A の USB ケーブルを使用して、Feather M0 WiFi をコンピューターに接続します。

![Feather Huzzah をコンピューターに接続する](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>シリアル ポートのアクセス許可を追加する (Ubuntu の場合のみ)

Ubuntu の場合は、Feather M0 WiFi の USB ポートを操作するアクセス許可があることを確認します。 シリアル ポートのアクセス許可を追加するには、次の手順に従います。


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

このセクションでは、Feather M0 WiFi でサンプル アプリケーションをデプロイし実行します。 このサンプル アプリケーションによって、Feather M0 WiFi の LED が点滅し、BME280 センサーから収集された気温と湿度のデータが IoT Hub に送信されます。

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>サンプル アプリケーションを GitHub から取得し Arduino IDE を準備する

このサンプル アプリケーションは GitHub でホストされています。 GitHub から、このサンプル アプリケーションが含まれているサンプル リポジトリを複製します。 サンプル リポジトリを複製するには、次の手順に従います。

1. コマンド プロンプトまたはターミナル ウィンドウを開きます。
1. サンプル アプリケーションを格納するフォルダーに移動します。
1. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Arduino IDE の Feather M0 WiFi パッケージをインストールします。

1. サンプル アプリケーションが格納されているフォルダーを開きます。
1. Arduino IDE のアプリ フォルダーにある app.ino ファイルを開きます。

   ![Arduino IDE のサンプル アプリケーションを開く](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. **[Tools]**(ツール) > **[Board]**(ボード) > **[Boards Manager]**(ボード マネージャー) をクリックし、`Arduino SAMD Boards` バージョン `1.6.2` 以降をインストールします。 

   `Arduino SAMD Boards` バージョン `1.6.2` 以降がインストールされていることがボード マネージャーに表示されます。

   ![ESP8266 のインストール](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. **[Tools]**(ツール) > **[Board]**(ボード) > **[Adafruit M0 WiFi]**(Adafruit M0 WiFi) をクリックします。

1. ドライバーをインストールします (Windows のみ)。Feather を接続する場合は、ドライバーのインストールが必要になることがあります。[ここ](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe)をクリックして、ドライバー インストーラーをダウンロードします。
   手順に従って、インストール対象のドライバーをインストールします。

### <a name="install-necessary-libraries"></a>必要なライブラリをインストールする

1. Arduino IDE で、**[スケッチ]** > **[ライブラリをインクルード]** > **[ライブラリを管理]** をクリックします。
1. 1 つずつ次のライブラリ名を検索します。 検索したライブラリごとに、**[インストール]** をクリックします。
   * `Adafruit_WINC1500`
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-bme280-sensor"></a>BME280 センサーがない場合

BME280 センサーがない場合は、サンプル アプリケーションで気温と湿度をシミュレートできます。 シミュレートされたデータを使用するようにサンプル アプリケーションを設定するには、次の手順に従います。

1. `app` フォルダー内の `config.h` ファイルを開きます。
1. 次のコード行を見つけて、値を `false` から `true` に変更します。
   ```c
   define SIMULATED_DATA true
   ```
   ![シミュレートされたデータをサンプル アプリケーションで使用するための構成](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. ファイルを `Control-s` という名前で保存します。

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>サンプル アプリケーションを Feather M0 WiFi にデプロイする

1. Arduino IDEで、**[ツール]** > **[ポート]** をクリックし、次に Feather M0 WiFi のシリアル ポートをクリックします。
1. **[スケッチ]** > **[マイコンボードに書き込む]** をクリックし、サンプル アプリケーションを Feather M0 WiFi にビルドし、デプロイします。

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
> 資格情報は Feather M0 WiFi の EEPROM に格納されています。 Feather M0 WiFi ボードのリセット ボタンをクリックすると、資格情報を削除するかどうかサンプル アプリケーションから質問されます。 この情報を消去するには `Y` を入力します。 情報を再度提供することを求められます。

### <a name="verify-the-sample-application-is-running-successfully"></a>サンプル アプリケーションが正常に実行されていることを確認する

[シリアルモニタ] ウィンドウで次のような出力を確認し、Feather M0 WiFi の LED が点滅していることを確認できれば、サンプル アプリケーションは正常に実行されています。

![Arduino IDE での最終出力](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>次のステップ

Feather M0 WiFi を IoT Hub に接続し、キャプチャしたセンサー データを IoT Hub に送信できるようになりました。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


