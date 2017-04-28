---
title: "Raspberry Pi から クラウドへ (Node.js) - Raspberry Pi の Azure IoT Hub への接続 | Microsoft Docs"
description: "Raspberry Pi を Azure IoT Hub に接続し、Raspberry Pi で Azure クラウドにデータを送信します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ"
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/14/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 223a15ab8ae4af6ef1b0164446e0664330189d1b
ms.lasthandoff: 04/19/2017


---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi の Azure IoT Hub への接続 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi の操作の基礎について説明します。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](http://www.windowsondevices.com/)を参照してください。

キットをお持ちでない場合は、 [Raspberry Pi 3 エミュレーター](https://blogs.msdn.microsoft.com/iliast/2016/11/10/how-to-emulate-raspberry-pi/)を試してください。 または、[こちら](https://azure.microsoft.com/develop/iot/starter-kits)で新しいキットを購入してください。

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

### <a name="enable-ssh-and-i2c"></a>SSH および I2C の有効化

1. Pi にモニター、キーボード、マウスを接続し、Pi を起動してから、`pi` をユーザー名として、`raspberry` をパスワードとして使用して Raspbian にログインします。
1. Raspberry アイコン > **[Preferences](設定)** > **[Raspberry Pi Configuration](Raspberry Pi 構成)** の順にクリックします。

   ![[Raspbian Preferences] (Raspbian 設定)メニュー](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. **[Interfaces]** (インターフェイス) タブで、**[I2C]** と **[SSH]** を **[Enable]** (有効) に設定し、**[OK]** をクリックします。

   ![I2C と SSH を Raspberry Pi で有効にする](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH と I2C を有効にする際の参考ドキュメントは [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) と [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2) に多数あります。

### <a name="connect-the-sensor-to-pi"></a>センサーを Pi に接続する

### <a name="connect-the-sensor-to-pi"></a>センサーを Pi に接続する

ブレッドボードとジャンパー ワイヤを使用して、次のように LED と BME280 を Pi に接続します。 センサーがない場合は、このセクションをスキップします。

![Raspberry Pi とセンサーの接続](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)


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

micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。 イーサネット ケーブルを使用して Pi を有線ネットワークに接続するか、[Raspberry Pi Foundation の手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。

![接続先の有線ネットワーク](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Pi でのサンプル アプリケーションの実行

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>サンプル アプリケーションの複製と前提条件となるパッケージのインストール

1. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Intel NUC に接続します。
    - Windows では [PuTTY](http://www.putty.org/)。
    - Ubuntu または macOS では組み込みの SSH クライアント。

1. 次のコマンドを実行して、サンプル アプリケーションを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. 次のコマンドによりすべてのパッケージをインストールします。 Azure IoT デバイス SDK、BME280 センサー ライブラリおよび配線 Pi ライブラリが含まれています。

   ```bash
   cd iot-hub-node-raspberry-pi-clientapp
   npm install
   ```
   > [!NOTE] 
   ネットワーク接続によっては、このインストール プロセスが終了するまで数分かかる場合があります。

### <a name="configure-the-sample-application"></a>サンプル アプリケーションの構成

1. 次のコマンドを実行して、config ファイルを開きます。

   ```bash
   nano config.json
   ```

   ![config ファイル](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   このファイルには、構成可能な 2 つの項目があります。 1 つ目は `interval` で、クラウドに送信する 2 つのメッセージの時間間隔を定義します。 2 つ目は `simulatedData` で、シミュレートされたセンサー データを使用するかどうかのブール値です。

   **センサーがない**場合は、`simulatedData` 値を `true` に設定し、シミュレートされたセンサー データをサンプル アプリケーションで作成して使用します。

1. Control + O > Enter > Control + X キーを押し、保存して終了します。

### <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

1. 次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。


IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>次のステップ

サンプル アプリケーションを実行し、センサー データを収集して IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
