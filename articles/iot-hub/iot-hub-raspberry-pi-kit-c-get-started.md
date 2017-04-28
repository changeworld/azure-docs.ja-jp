---
title: "Raspberry Pi から クラウドへ (C) - Raspberry Pi の Azure IoT Hub への接続 | Microsoft Docs"
description: "Raspberry Pi を Azure IoT Hub に接続し、Raspberry Pi で Azure クラウドにデータを送信します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ"
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/13/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 387dcace5be29de52b465bc53fa81a3dbf876390
ms.lasthandoff: 04/19/2017


---

# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi の Azure IoT Hub への接続 (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi の操作の基礎について説明します。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](http://www.windowsondevices.com/)を参照してください。

## <a name="what-you-do"></a>作業内容

* Raspberry Pi をセットアップします。
* IoT Hub を作成します。
* Pi のデバイスを IoT Hub に登録します。
* Pi でサンプル アプリケーションを実行し、センサー データを IoT Hub に送信します。

作成した IoT Hub に Raspberry Pi を接続します。 次に、BME280 センサーから気温と湿度のデータを収集するために、Pi のサンプル アプリケーションを実行します。 最後に、センサー データを IoT Hub に送信します。

## <a name="what-you-learn"></a>学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。
* Pi と BME280 センサーを接続する方法。
* Pi のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

## <a name="what-you-need"></a>必要なもの

![必要なもの](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 または Raspberry Pi 3 ボード。
* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* モニター、USB キーボード、Pi に接続するマウス。
* Mac PC または Windows か Linux をインストールした PC。
* インターネット接続。
* 16 GB 以上の microSD カード。
* USB-SD アダプターまたは microSD カード (microSD カードに オペレーティング システム イメージを書き込むため)。
* 5V 2A の AC アダプターと約 2 m の micro USB ケーブル。

省略可能な品目を次に示します。

* 温度、気圧、および湿度用の組み立て済み Adafruit BME280 センサー。
* ブレッドボード
* 6 つの F/M ジャンパー ワイヤ。
* 拡散型 10 mm LED。


> [!NOTE] 
サンプル コードはセンサー データのシミュレーションをサポートしているため、これらの品目は省略可能です。


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Raspberry Pi のセットアップ

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi の Raspbian オペレーティング システムのインストール

microSD カードに Raspbian イメージをインストールするための準備をします。

1. Raspbian をダウンロードします。
   1. [Raspbian Jessie with Pixel (.zip ファイル) をダウンロードします](https://www.raspberrypi.org/downloads/raspbian/)。
   1. コンピューター上のフォルダーに Raspbian イメージを抽出します。
1. microSD カードに Raspbian をインストールします。
   1. [Etcher SD カード書き込みユーティリティをダウンロードしてインストールします](https://etcher.io/)。
   1. Etcher を実行し、手順 1. で抽出した Raspbian イメージを選択します。
   1. microSD カード ドライブを選択します。 適切なドライブが既に選択されている場合があります。
   1. [Flash (フラッシュ)] をクリックして、microSD カードに Raspbian をインストールします。
   1. インストールが完了したら、コンピューターから microSD カードを取り出します。 Etcher では完了時に microSD カードを自動的に取り出すか、マウント解除するため、microSD カードを直接取り出しても問題ありません。
   1. microSD カードを Pi に挿入します。

### <a name="enable-ssh-and-spi"></a>SSH および SPI の有効化

1. Pi にモニター、キーボード、マウスを接続し、Pi を起動してから、`pi` をユーザー名として、`raspberry` をパスワードとして使用して Raspbian にログインします。
1. Raspberry アイコン > **[Preferences](設定)** > **[Raspberry Pi Configuration](Raspberry Pi 構成)** の順にクリックします。

   ![[Raspbian Preferences] (Raspbian 設定)メニュー](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. **[Interfaces]** タブで、**[SPI]** と **[SSH]** を **[Enable]** に設定し、**[OK]** をクリックします。

   ![Raspberry Pi で SPI と SSH を有効にする](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH および SPI を有効にする場合は、[raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) および [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) でさらに参考ドキュメントを見つけることができます。

### <a name="connect-the-sensor-to-pi"></a>センサーを Pi に接続する

ブレッドボードとジャンパー ワイヤを使用して、次のように LED と BME280 を Pi に接続します。 センサーがない場合は、このセクションをスキップします。

![Raspberry Pi とセンサーの接続](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

センサーの各ピンで、次のように接続します。

| 開始 (センサーと LED)     | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (ピン 5G)         | GPIO 4 (ピン 7)         | 白いケーブル   |
| LED GND (ピン 6G)         | GND (ピン 6)            | 黒いケーブル   |
| VDD (ピン 18F)            | 3.3V PWR (ピン 17)      | 白いケーブル   |
| GND (ピン 20F)            | GND (ピン 20)           | 黒いケーブル   |
| SCK (ピン 21F)            | SPI0 SCLK (ピン 23)     | オレンジ色のケーブル  |
| SDO (ピン 22F)            | SPI0 MISO (ピン 21)     | 黄色のケーブル  |
| SDI (ピン 23F)            | SPI0 MOSI (ピン 19)     | 緑のケーブル   |
| CS (ピン 24F)             | SPI0 CS (ピン 24)       | 青いケーブル    |

クリックすると [Raspberry Pi 2 & 3 Pin mappings](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) が表示されて参照できます。

BME280 が正常に Raspberry Pi に接続されると、下の図のようになります。

![接続された Pi と BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。 イーサネット ケーブルを使用して Pi を有線ネットワークに接続するか、[Raspberry Pi Foundation の手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。

![接続先の有線ネットワーク](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Pi でのサンプル アプリケーションの実行

### <a name="install-the-prerequisite-packages"></a>前提条件となるパッケージのインストール

1. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Raspberry Pi に接続します。
    - Windows では [PuTTY](http://www.putty.org/)。
    - Ubuntu または macOS では組み込みの SSH クライアント。

1. 次のコマンドを実行して、C および Cmake 用 Microsoft Azure IoT デバイス SDK の前提条件となるパッケージをインストールします。

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake
   ```


### <a name="configure-the-sample-application"></a>サンプル アプリケーションの構成

1. 次のコマンドを実行して、サンプル アプリケーションを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. 次のコマンドを実行して、config ファイルを開きます。

   ```bash
   cd iot-hub-c-raspberry-pi-clientapp
   nano config.json
   ```

   ![config ファイル](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   このファイルには、構成可能な 2 つのマクロがあります。 1 つ目は `INTERVAL` で、クラウドに送信する 2 つのメッセージの時間間隔を定義します。 2 つ目は `SIMULATED_DATA` で、シミュレートされたセンサー データを使用するかどうかのブール値です。

   **センサーがない**場合は、`SIMULATED_DATA` 値を `1` に設定し、シミュレートされたセンサー データをサンプル アプリケーションで作成して使用します。

1. Control + O > Enter > Control + X キーを押し、保存して終了します。

### <a name="build-and-run-the-sample-application"></a>サンプル アプリケーションのビルドおよび実行

1. 次のコマンドを実行して、サンプル アプリケーションをビルドします。

   ```bash
   cmake . && make
   ```
   ![ビルドの出力](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. 次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo ./app '<device connection string>'
   ```

   > [!NOTE] 
   デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。


IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>次のステップ

サンプル アプリケーションを実行し、センサー データを収集して IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

