---
title: "Raspberry Pi から クラウドへ (Node.js) - Raspberry Pi の Azure IoT Hub への接続 | Microsoft Docs"
description: "Raspberry Pi を Azure IoT Hub に接続し、Raspberry Pi で Azure クラウドにデータを送信します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ"
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: d82480c013f3d5f13fdbf49ff89f19cf71c1a50f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---

<a id="connect-raspberry-pi-to-azure-iot-hub-nodejs" class="xliff"></a>

# Raspberry Pi の Azure IoT Hub への接続 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi の操作の基礎について説明します。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](http://www.windowsondevices.com/)を参照してください。

キットをお持ちでない場合は、 [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)を試してください。 または、[こちら](https://azure.microsoft.com/develop/iot/starter-kits)で新しいキットを購入してください。


<a id="what-you-do" class="xliff"></a>

## 作業内容

* Raspberry Pi をセットアップします。
* IoT Hub を作成します。
* Pi のデバイスを IoT Hub に登録します。
* Pi でサンプル アプリケーションを実行し、センサー データを IoT Hub に送信します。

作成した IoT Hub に Raspberry Pi を接続します。 次に、BME280 センサーから気温と湿度のデータを収集するために、Pi のサンプル アプリケーションを実行します。 最後に、センサー データを IoT Hub に送信します。

<a id="what-you-learn" class="xliff"></a>

## 学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。
* Pi と BME280 センサーを接続する方法。
* Pi のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

<a id="what-you-need" class="xliff"></a>

## 必要なもの

![必要なもの](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

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

<a id="setup-raspberry-pi" class="xliff"></a>

## Raspberry Pi のセットアップ

<a id="install-the-raspbian-operating-system-for-pi" class="xliff"></a>

### Pi の Raspbian オペレーティング システムのインストール

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

<a id="enable-ssh-and-i2c" class="xliff"></a>

### SSH および I2C の有効化

1. Pi にモニター、キーボード、マウスを接続し、Pi を起動してから、`pi` をユーザー名として、`raspberry` をパスワードとして使用して Raspbian にログインします。
1. Raspberry アイコン > **[Preferences]\(設定)** > **[Raspberry Pi Configuration]\(Raspberry Pi 構成)** の順にクリックします。

   ![[Raspbian Preferences] (Raspbian 設定)メニュー](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. **[Interfaces]** (インターフェイス) タブで、**[I2C]** と **[SSH]** を **[Enable]** (有効) に設定し、**[OK]** をクリックします。 物理センサーがなく、シミュレートされたセンサー データを使用する場合は、この手順は省略可能です。

   ![I2C と SSH を Raspberry Pi で有効にする](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH と I2C を有効にする際の参考ドキュメントは [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) と [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c) に多数あります。

<a id="connect-the-sensor-to-pi" class="xliff"></a>

### センサーを Pi に接続する

ブレッドボードとジャンパー ワイヤを使用して、次のように LED と BME280 を Pi に接続します。 センサーがない場合は、このセクションをスキップします。

![Raspberry Pi とセンサーの接続](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 センサーでは、温度と湿度のデータを収集できます。 また、デバイスとクラウドの間で通信が行われると、LED が点滅します。 

センサーの各ピンで、次のように接続します。

| 開始 (センサーと LED)     | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (ピン 5G)             | 3.3V PWR (ピン 1)       | 白いケーブル   |
| GND (ピン 7G)             | GND (ピン 6)            | 茶色のケーブル   |
| SCK (ピン 8G)             | I2C1 SDA (ピン 3)       | オレンジ色のケーブル  |
| SDI (ピン 10G)            | I2C1 SCL (ピン 5)       | 赤いケーブル     |
| LED VDD (ピン 18F)        | GPIO 24 (ピン 18)       | 白いケーブル   |
| LED GND (ピン 17F)        | GND (ピン 20)           | 黒いケーブル   |

クリックすると [Raspberry Pi 2 & 3 Pin mappings](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) が表示されて参照できます。

BME280 が正常に Raspberry Pi に接続されると、下の図のようになります。

![接続された Pi と BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

<a id="connect-pi-to-the-network" class="xliff"></a>

### Pi のネットワークへの接続

micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。 イーサネット ケーブルを使用して Pi を有線ネットワークに接続するか、[Raspberry Pi Foundation の手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。 Pi がネットワークに正常に接続されたら、[Pi の IP アドレス](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)をメモしておく必要があります。

![接続先の有線ネットワーク](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> 必ず Pi をコンピューターと同じネットワークに接続してください。 たとえば、コンピューターがワイヤレス ネットワークに接続され、Pi がワイヤード (有線) ネットワークに接続されている場合、devdisco の出力に IP アドレスが表示されないことがあります。

<a id="run-a-sample-application-on-pi" class="xliff"></a>

## Pi でのサンプル アプリケーションの実行

<a id="clone-sample-application-and-install-the-prerequisite-packages" class="xliff"></a>

### サンプル アプリケーションの複製と前提条件となるパッケージのインストール

1. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Raspberry Pi に接続します。
    - Windows では [PuTTY](http://www.putty.org/)。 SSH を使用して接続するために、Pi の IP アドレスが必要です。
    - Ubuntu または macOS では組み込みの SSH クライアント。 SSH を使用して Pi を接続するには、`ssh pi@<ip address of pi>` を実行する必要がある場合があります。

   > [!NOTE] 
   既定のユーザー名は `pi` で、パスワードは`raspberry` です。

1. Pi に Node.js と NPM をインストールします。
   
   まず、次のコマンドで Node.js のバージョンを確認する必要があります。 
   
   ```bash
   node -v
   ```

   バージョンが 4.x 未満か、または、Pi に Node.js がない場合は、次のコマンドを実行して、Node.js をインストールするか、更新します。

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. 次のコマンドを実行して、サンプル アプリケーションを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. 次のコマンドによりすべてのパッケージをインストールします。 Azure IoT デバイス SDK、BME280 センサー ライブラリおよび配線 Pi ライブラリが含まれています。

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   ネットワーク接続によっては、このインストール プロセスが終了するまで数分かかる場合があります。

<a id="configure-the-sample-application" class="xliff"></a>

### サンプル アプリケーションの構成

1. 次のコマンドを実行して、config ファイルを開きます。

   ```bash
   nano config.json
   ```

   ![config ファイル](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   このファイルには、構成可能な 2 つの項目があります。 1 つ目は `interval` で、クラウドに送信する 2 つのメッセージの時間間隔を定義します。 2 つ目は `simulatedData` で、シミュレートされたセンサー データを使用するかどうかを表すブール値です。

   **センサーがない**場合は、`simulatedData` 値を `true` に設定し、シミュレートされたセンサー データをサンプル アプリケーションで作成して使用します。

1. Control + O > Enter > Control + X キーを押し、保存して終了します。

<a id="run-the-sample-application" class="xliff"></a>

### サンプル アプリケーションの実行

1. 次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。


IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

<a id="next-steps" class="xliff"></a>

## 次のステップ

サンプル アプリケーションを実行してセンサー データを収集し、IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
