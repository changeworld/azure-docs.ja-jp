---
title: "Azure IoT Edge で物理デバイスを使用する | Microsoft Docs"
description: "Texas Instruments SensorTag デバイスを使用して Raspberry Pi 3 デバイスで動作する IoT Edge ゲートウェイを介して IoT Hub にデータを送信する方法。 ゲートウェイのビルドには Azure IoT Edge を使用します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 63545f007a2696714d21ab7d778a6e78e6183806
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-physical-device-linux"></a>Azure IoT Edge を使用して物理デバイス (Linux) に D2C メッセージを送信する

この [Bluetooth 低エネルギー サンプル][lnk-ble-samplecode]のチュートリアルでは、[Azure IoT Edge][lnk-sdk] を使用して次の処理を行う方法について説明します。

* 物理デバイスから IoT Hub に D2C テレメトリを転送する。
* IoT Hub から物理デバイスにコマンドをルーティングする。

このチュートリアルでは、次の項目について説明します。

* **アーキテクチャ**: Bluetooth 低エネルギー サンプルのアーキテクチャに関する重要な情報。
* **ビルドおよび実行**: サンプルをビルドして実行するために必要な手順。

## <a name="architecture"></a>アーキテクチャ

このチュートリアルでは、Raspbian Linux が動作する Raspberry Pi 3 で IoT Edge ゲートウェイをビルドして実行する方法を示します。 ゲートウェイのビルドには IoT Edge を使用します。 サンプルでは、温度データを収集するために Texas Instruments SensorTag Bluetooth Low Energy (BLE) デバイスを使用します。

IoT Edge ゲートウェイを実行すると、次のような処理が行われます。

* Bluetooth Low Energy (BLE) プロトコルを使用して SensorTag デバイスに接続する。
* HTTP プロトコルを使用して IoT Hub に接続する。
* SensorTag デバイスから IoT Hub にテレメトリを転送する。
* IoT Hub から SensorTag デバイスにコマンドをルーティングする。

ゲートウェイには、次の IoT Edge モジュールが含まれています。

* *BLE モジュール* : BLE デバイスと接続し、デバイスから温度データを受信してデバイスにコマンドを送信します。
* *BLE クラウド対デバイス モジュール*: クラウドから送信される JSON メッセージを *BLE モジュール*用の BLE 命令に変換します。
* "*ロガー モジュール*": すべてのゲートウェイ メッセージをローカル ファイルに記録します。
* *ID マッピング モジュール* : BLE デバイスの MAC アドレスと Azure IoT Hub デバイス ID の間で変換を行います。
* *IoT Hub モジュール* : テレメトリ データを IoT Hub にアップロードし、IoT Hub からデバイス コマンドを受信します。
* *BLE プリンター モジュール* : BLE デバイスからのテレメトリを解釈し、トラブルシューティングとデバッグに利用できるように、書式設定されたデータをコンソールに出力します。

### <a name="how-data-flows-through-the-gateway"></a>Gateway を介したデータの流れ

次のブロック図には、テレメトリをアップロードする際のデータ フロー パイプラインを示します。

![テレメトリをアップロードする際のゲートウェイ パイプライン](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

BLE デバイスから IoT Hub に転送されるテレメトリ項目の処理手順を次に示します。

1. BLE デバイスが温度サンプルを生成し、Bluetooth 経由でゲートウェイの BLE モジュールに送信する。
1. BLE モジュールがサンプルを受信し、デバイスの MAC アドレスと共にブローカーに発行する。
1. ID マッピング モジュールがこのメッセージを取得し、内部テーブルを使用してデバイスの MAC アドレスを IoT Hub デバイス ID に変換する。 IoT Hub デバイス ID は、デバイス ID とデバイス キーで構成されます。 その後、温度サンプルのデータ、デバイスの MAC アドレス、デバイス ID、デバイス キーを含む新しいメッセージをモジュールが発行する。
1. IoT Hub モジュールがこの (ID マッピング モジュールで生成された) 新しいメッセージを受信し、IoT Hub に発行する。
1. ロガー モジュールが、ブローカーからのすべてのメッセージをローカル ファイルに記録する。

次のブロック図には、デバイス コマンドのデータ フロー パイプラインを示します。

![デバイス コマンドのゲートウェイ パイプライン](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. IoT Hub モジュールは IoT Hub に対して定期的に新しいコマンド メッセージのポーリングを行う。
1. 新しいコマンド メッセージを受信すると、IoT Hub モジュールはそれをブローカーに発行する。
1. ID マッピング モジュールがコマンド メッセージを取得し、内部テーブルを使用して IoT Hub デバイス ID をデバイスの MAC アドレスに変換する。 その後、プロパティ マップ内にターゲット デバイスの MAC アドレスを含む新しいメッセージを発行する。
1. BLE クラウド対デバイス モジュールは、このメッセージを受け取って、BLE モジュール用の適切な BLE 命令に変換する。 その後、新しいメッセージを発行する。
1. BLE モジュールがこのメッセージを取得し、BLE デバイスと通信して I/O 命令を実行する。
1. ロガー モジュールがメッセージ バスから受信したすべてのメッセージをディスク ファイルに記録する。

## <a name="prepare-your-hardware"></a>ハードウェアの準備

このチュートリアルでは、Raspbian を実行している Raspberry Pi 3 に接続した [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) デバイスを使用することを前提としています。

### <a name="install-raspbian"></a>Raspbian をインストールする

次のオプションのいずれかを使用して、Raspbian を Raspberry Pi 3 デバイスにインストールします。

* [NOOBS][lnk-noobs] グラフィカル ユーザー インターフェイスを使用して Raspbian の最新版をインストールする。
* Raspbian オペレーティング システムの最新のイメージを手動で[ダウンロード][lnk-raspbian]し、SD カードに書き込む。

### <a name="install-bluez-537"></a>BlueZ 5.37 をインストールする

BLE モジュールは、BlueZ スタックを介して Bluetooth ハードウェアと通信を行います。 モジュールが正常に動作するためには、BlueZ Version 5.37 が必要です。 次の手順に従って、BlueZ の適切なバージョンをインストールします。

1. 現在の Bluetooth デーモンを停止します。

    `sudo systemctl stop bluetooth`

1. BlueZ の依存関係をインストールします。

    `sudo apt-get update`

    `sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev`

1. bluez.org から BlueZ のソース コードをダウンロードします。

    `wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz`

1. ソース コードを解凍します。

    `tar -xvf bluez-5.37.tar.xz`

1. 新しく作成したフォルダーにディレクトリを変更します。

    `cd bluez-5.37`

1. ビルドする BlueZ コードを構成します。

    `./configure --disable-udev --disable-systemd --enable-experimental`

1. BlueZ をビルドします。

    `make`

1. ビルドした BlueZ をインストールします。

    `sudo make install`

1. `/lib/systemd/system/bluetooth.service` ファイル内の新しい Bluetooth デーモンを指すように Bluetooth の systemd サービス構成を変更します。 "ExecStart" 行を次の行で置き換えます。

    `ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E`

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Raspberry Pi 3 デバイスから SensorTag デバイスへの接続を有効にする

サンプルを実行する前に、Raspberry Pi 3 から SensorTag デバイスに接続できることを確認する必要があります。


1. `rfkill` ユーティリティがインストールされていることを確認します。

    `sudo apt-get install rfkill`

1. Raspberry Pi 3 で Bluetooth のブロックを解除し、バージョン番号が **5.37** であることを確認します。

    `sudo rfkill unblock bluetooth`

    `bluetoothctl --version`

1. Bluetooth サービスを開始し、**bluetoothctl** コマンドを実行して Bluetooth の対話型シェルを起動します。

    `sudo systemctl start bluetooth`

    `bluetoothctl`

1. **power on** コマンドを入力して Bluetooth コントローラーの電源を入れます。 次のような出力が表示されます。

    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. 対話型の Bluetooth シェルで、**scan on** コマンドを入力して Bluetooth デバイスをスキャンします。 次のような出力が表示されます。

    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. 小さいボタンを押して SensorTag デバイスを検出可能な状態にします (緑色の LED が点滅します)。 Raspberry Pi 3 によって、次のように SensorTag デバイスが検出されます。

    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    この例では、SensorTag デバイスの MAC アドレスが **A0:E6:F8:B5:F6:00**であることがわかります。

1. **scan off** コマンドを入力してスキャンをオフにします。

    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. MAC アドレスを使用して SensorTag デバイスに接続します (**connect \<MAC アドレス\>** を入力します)。 次に示す出力サンプルは、わかりやすくするために一部省略されています。

    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > **list-attributes** コマンドを使用すると、もう一度デバイスの GATT 特性を一覧表示することができます。

1. 接続が確認できたので、**disconnect** コマンドを使用してデバイスから切断し、**quit** コマンドを使用して Bluetooth シェルを終了します。

    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

これで IoT Edge の BLE サンプルを Raspberry Pi 3 で実行する準備が整いました。

## <a name="run-the-iot-edge-ble-sample"></a>IoT Edge の BLE サンプルを実行する

IoT Edge の BLE サンプルを実行するには、次の 3 つのタスクを完了する必要があります。

* IoT Hub に 2 つのサンプル デバイスを構成する。
* IoT Edge を Raspberry Pi 3 デバイス上にビルドする。
* Raspberry Pi 3 デバイスで BLE サンプルを構成して実行する。

この記事の執筆時点では、IoT Edge でサポートされているのは、Linux で BLE モジュールを使用するゲートウェイのみです。

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>IoT Hub に 2 つのサンプル デバイスを構成する

* Azure サブスクリプションで [IoT Hub を作成][lnk-create-hub]します。このチュートリアルを実行するには、Hub の名前が必要です。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* **SensorTag_01** という名前の 1 つのデバイスを IoT Hub に追加し、その ID とデバイス キーをメモしておきます。 [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用すると、前の手順で作成した IoT Hub にこのデバイスを追加し、デバイスのキーを取得することができます。 このデバイスは、ゲートウェイの構成時に SensorTag デバイスにマップします。

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Azure IoT Edge を Raspberry Pi 3 上にビルドする

Azure IoT Edge の依存関係をインストールします。

`sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev`

次のコマンドを使用して、IoT Edge とそのすべてのサブモジュールをホーム ディレクトリに複製します。

`cd ~`

`git clone --recursive https://github.com/Azure/iot-edge.git`

`cd iot-edge`

`git submodule update --init --recursive`

IoT Edge レポジトリの完全なコピーを Raspberry Pi 3 上に用意したら、SDK が含まれているフォルダーから次のコマンドを使用してビルドすることができます。

`./tools/build.sh`

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Raspberry Pi 3 で BLE サンプルを構成して実行する

サンプルを起動して実行するには、Edge ゲートウェイに関与しているモジュールをそれぞれ構成する必要があります。 この構成は JSON ファイル形式で指定し、5つの IoT Edge モジュールをすべて構成する必要があります。 リポジトリ内には **gateway\_sample.json** という名前のサンプル JSON ファイルが用意されており、独自の構成ファイルを構築するための雛形として使用することができます。 このファイルは、IoT Edge レポジトリのローカル コピー内の **samples/ble_gateway/src** フォルダーにあります。

以降のセクションでは、この構成ファイルを BLE サンプル用に編集する方法を説明しており、IoT Edge レポジトリが Raspberry Pi 3 の **/home/pi/iot-edge/** フォルダーにあることを前提としています。 リポジトリが他の場所にある場合は、パスを適宜修正してください。

#### <a name="logger-configuration"></a>ロガーの構成

ゲートウェイ レポジトリが **/home/pi/iot-edge/** フォルダーにあると仮定し、ロガー モジュールを次のように構成します。

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>BLE モジュールの構成

BLE デバイスの構成サンプルでは、Texas Instruments SensorTag デバイスを BLE デバイスとして想定しています。 GATT 周辺機器として動作する標準 BLE デバイスはすべて使用できますが、GATT 特性の ID とデータを更新することが必要になります (書き込み命令の場合)。 SensorTag デバイスの MAC アドレスを次のように追加します。

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>IoT Hub モジュール

IoT Hub の名前を追加します。 サフィックス値は通常、 **azure-devices.net**です。

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>ID マッピング モジュールの構成

SensorTag デバイスの MAC アドレスと、IoT Hub に追加した **SensorTag_01** デバイスのデバイス ID とキーを追加します。

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>BLE プリンター モジュールの構成

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>BLEC2D モジュールの構成

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>ルーティング構成

次の構成は、IoT Edge モジュール間の次のルーティングを保証します。

* **Logger** モジュールがすべてのメッセージを受信し、それらを記録します。
* **SensorTag** モジュールが **mapping** モジュールと **BLE Printer** モジュールの両方にメッセージを送信します。
* **mapping** モジュールが IoT Hub に送信されるメッセージを **IoTHub** モジュールに送信します。
* **IoTHub** モジュールがメッセージを **mapping** モジュールに送り返します。
* **mapping** モジュールがメッセージを **BLEC2D** モジュールに送信します。
* **BLEC2D** モジュールがメッセージを **SensorTag** モジュールに送り返します。

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

サンプルを実行するには、JSON 構成ファイルへのパスを **ble\_gateway** バイナリにパラメーターとして渡します。 次のコマンドは、**gateway_sample.json** 構成ファイルを使用することを前提としています。 このコマンドを Raspberry Pi の **iot-edge** フォルダーから実行します。

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

このサンプルを実行する前に、SensorTag デバイスの小さいボタンを押して検出可能にすることが必要になる場合があります。

サンプルを実行する際に[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)または [iothub-explorer](https://github.com/Azure/iothub-explorer) ツールを使用すると、IoT Edge ゲートウェイによって SensorTag デバイスから転送されるメッセージを監視することができます。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

BLE モジュールでは、IoT Hub からデバイスへのコマンドの送信もサポートしています。 [デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)または [iothub-explorer](https://github.com/Azure/iothub-explorer) ツールを使用して BLE ゲートウェイ モジュールから BLE デバイスに転送する JSON メッセージを送信することができます。

Texas Instruments の SensorTag デバイスを使用している場合、IoT Hub からコマンドを送信して、赤色の LED、緑色の LED、またはブザーをオンにすることができます。 IoT Hub からコマンドを送信するには、最初に次の 2 つの JSON メッセージをこの順序で送信します。 次に、いずれかのコマンドを送信して、LED またはブザーをオンにすることができます。

1. すべての LED とブザーをリセットします (オフにします)。

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. I/O を "リモート" として構成します。

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

その後、次のいずれかのコマンドを送信して、SensorTag デバイスで LED またはブザーをオンにすることができます。

* 赤色の LED をオンにします。

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* 緑色の LED をオンにします。

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* ブザーをオンにします。

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>次のステップ

IoT Edge に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

* [Azure IoT Edge][lnk-sdk]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 

