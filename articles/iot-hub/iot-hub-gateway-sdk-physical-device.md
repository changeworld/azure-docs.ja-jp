<properties
    pageTitle="Gateway SDK で実際のデバイスを使用する |Microsoft Azure"
    description="Texas Instruments SensorTag を使用し、Intel Edison Compute Module で動作するゲートウェイを介して IoT Hub にデータを送信する Azure IoT Hub Gateway SDK チュートリアル"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-(beta)-–-send-device-to-cloud-messages-with-a-real-device-using-linux"></a>IoT Gateway SDK (ベータ) - Linux を使用した実際のデバイスで D2C メッセージを送信する

この [Bluetooth 低エネルギー サンプル][lnk-ble-samplecode]のチュートリアルでは、[Microsoft Azure IoT Gateway SDK][lnk-sdk] を使用して、物理デバイスから IoT Hub に D2C テレメトリを転送する方法と、IoT Hub から物理デバイスにコマンドをルーティングする方法について説明します。

このチュートリアルでは、次の項目について説明します。

* **アーキテクチャ**: Bluetooth 低エネルギー サンプルのアーキテクチャに関する重要な情報。

* **ビルドおよび実行**: サンプルをビルドして実行するために必要な手順。

## <a name="architecture"></a>アーキテクチャ

このチュートリアルでは、Linux が動作する Intel Edison Compute Module で IoT Gateway をビルドして実行する方法を示します。 ゲートウェイのビルドには IoT Gateway SDK を使用します。 サンプルでは、温度データを収集するために Texas Instruments SensorTag Bluetooth Low Energy (BLE) デバイスを使用します。

ゲートウェイを実行すると、次のような処理が行われます。

- Bluetooth Low Energy (BLE) プロトコルを使用して SensorTag デバイスに接続する。
- AMQP プロトコルを使用して IoT Hub に接続する。
- SensorTag デバイスから IoT Hub にテレメトリを転送する。
- IoT Hub から SensorTag デバイスにコマンドをルーティングする。

ゲートウェイには、次のモジュールが含まれています。

- *BLE モジュール* : BLE デバイスと接続し、デバイスから温度データを受信してデバイスにコマンドを送信します。
- *BLE クラウド対デバイス モジュール*: クラウドから送信される JSON メッセージを *BLE モジュール*用の BLE 命令に変換します。
- *モジュール* : すべてのゲートウェイ メッセージをログに記録します。
- *ID マッピング モジュール* : BLE デバイスの MAC アドレスと Azure IoT Hub デバイス ID の間で変換を行います。
- *IoT Hub モジュール* : テレメトリ データを IoT Hub にアップロードし、IoT Hub からデバイス コマンドを受信します。
- *BLE プリンター モジュール* : BLE デバイスからのテレメトリを解釈し、トラブルシューティングとデバッグに利用できるように、書式設定されたデータをコンソールに出力します。

### <a name="how-data-flows-through-the-gateway"></a>Gateway を介したデータの流れ

次のブロック図には、テレメトリをアップロードする際のデータ フロー パイプラインを示します。

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

BLE デバイスから IoT Hub に転送されるテレメトリ項目の処理手順を次に示します。

1. BLE デバイスが温度サンプルを生成し、Bluetooth 経由でゲートウェイの BLE モジュールに送信する。
2. BLE モジュールがサンプルを受信し、デバイスの MAC アドレスと共にブローカーに発行する。
3. ID マッピング モジュールがこのメッセージを取得し、内部テーブルを使用してデバイスの MAC アドレスを IoT Hub デバイス ID (デバイス ID とデバイス キー) に変換する。 その後、温度サンプルのデータ、デバイスの MAC アドレス、デバイス ID、デバイス キーを含む新しいメッセージを発行する。
4. IoT Hub モジュールがこの (ID マッピング モジュールで生成された) 新しいメッセージを受信し、IoT Hub に発行する。
5. ロガー モジュールがメッセージ バスから受信したすべてのメッセージをディスク ファイルに記録する。

次のブロック図には、デバイス コマンドのデータ フロー パイプラインを示します。

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. IoT Hub モジュールは IoT Hub に対して定期的に新しいコマンド メッセージのポーリングを行う。
2. 新しいコマンド メッセージを受信すると、IoT Hub モジュールはそれをブローカーに発行する。
3. ID マッピング モジュールがコマンド メッセージを取得し、内部テーブルを使用して IoT Hub デバイス ID をデバイスの MAC アドレスに変換する。 その後、プロパティ マップ内にターゲット デバイスの MAC アドレスを含む新しいメッセージを発行する。
4. BLE クラウド対デバイス モジュールは、このメッセージを受け取って、BLE モジュール用の適切な BLE 命令に変換する。 その後、新しいメッセージを発行する。
5. BLE モジュールがこのメッセージを取得し、BLE デバイスと通信して I/O 命令を実行する。
6. ロガー モジュールがメッセージ バスから受信したすべてのメッセージをディスク ファイルに記録する。

## <a name="prepare-your-hardware"></a>ハードウェアの準備

このチュートリアルでは、Intel Edison ボードに接続した [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) デバイスを使用することを前提としています。

### <a name="set-up-the-edison-board"></a>Edison ボードを設定する

チュートリアルを開始する前に、Edison デバイスをワイヤレス ネットワークに接続できることを確認する必要があります。 Edison デバイスを設定するには、それをホスト コンピューターに接続する必要があります。 Intel では、次の各オペレーティング システム向けにファースト ステップ ガイドを提供しています。

- 「[Get Started with the Intel Edison Development Board on Windows 64-bit (Windows 64 ビットで Intel Edison Development Board を使用する)][lnk-setup-win64]」
- 「[Get Started with the Intel Edison Development Board on Windows 32-bit (Windows 32 ビットで Intel Edison Development Board を使用する)][lnk-setup-win32]」
- 「[Get Started with the Intel Edison Development Board on Mac OS X (Mac OS X で Intel Edison Development Board を使用する)][lnk-setup-osx]」
- 「[Getting Started with the Intel® Edison Board on Linux (Linux で Intel® Edison Board を使用する)][lnk-setup-linux]」

Edison デバイスを設定して使用方法を理解するには、これらの "ファースト ステップ" 記事で、現在のチュートリアルには不要な最後の手順「Choose IDE (IDE の選択)」を除くすべての手順を完了する必要があります。 Edison の設定プロセスでは、次のような設定を行います。

- Edison に最新のファームウェアを書き込む。
- ホストから Edison へのシリアル接続を確立する。
- **configure_edison** スクリプトを実行してパスワードを設定し、Edison 上で WiFi を有効にする。

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Edison ボードから SensorTag デバイスへの接続を有効にする

サンプルを実行する前に、Edison ボードから SensorTag デバイスに接続できることを確認する必要があります。

最初に、Edison から SensorTag デバイスに接続できることを確認する必要があります。

1. Edison で Bluetooth のブロックを解除し、バージョン番号が **5.37**であることを確認します。
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. **bluetoothctl** コマンドを実行します。 これで対話型の Bluetooth シェルが表示されます。 

3. **power on** コマンドを入力して Bluetooth コントローラーの電源を入れます。 次のような出力が表示されます。
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. 対話型の Bluetooth シェルにいる間に、 **scan on** コマンドを入力して Bluetooth デバイスをスキャンします。 次のような出力が表示されます。
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. 小さいボタンを押して SensorTag デバイスを検出可能な状態にします (緑色の LED が点滅します)。 Edison では次のように SensorTag デバイスが検出されます。
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    この例では、SensorTag デバイスの MAC アドレスが **A0:E6:F8:B5:F6:00**であることがわかります。

6. **scan off** コマンドを入力してスキャンをオフにします。
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. SensorTag デバイスの MAC アドレスを使用し、**connect <MAC address>** コマンドを入力してデバイスに接続します。 次に示す出力サンプルは一部省略されている点に注意してください。
    
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
    
    注: **list-attributes** コマンドを使用すると、もう一度デバイスの GATT 特性を一覧表示することができます。

8. 接続が確認できたので、**disconnect** コマンドを使用してデバイスから切断し、**quit** コマンドを使用して Bluetooth シェルを終了します。
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

これで BLE Gateway サンプルを Edison デバイスで実行する準備が整いました。

## <a name="run-the-ble-gateway-sample"></a>BLE Gateway サンプルの実行

BLE サンプルを Edison で実行するには、次の 3 つのタスクを完了する必要があります。

- IoT Hub に 2 つのサンプル デバイスを構成する。
- Edison デバイスで Gateway SDK をビルドする。
- Edison デバイスで BLE サンプルを構成して実行する。

この記事の執筆時点では、Gateway SDK でサポートされているのは、Linux で BLE モジュールを使用するゲートウェイのみです。

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>IoT Hub に 2 つのサンプル デバイスを構成する

- Azure サブスクリプションで [IoT Hub を作成][lnk-create-hub]します。このチュートリアルを実行するには、Hub の名前が必要です。 Azure サブスクリプションがまだない場合は、[無料アカウント][lnk-free-trial]を取得できます。
- **SensorTag_01** という名前の 1 つのデバイスを IoT Hub に追加し、その ID とデバイス キーをメモしておきます。 [Device Explorer または iothub-explorer][lnk-explorer-tools] ツールを使用すると、前の手順で作成した IoT Hub にこのデバイスを追加し、デバイスのキーを取得することができます。 このデバイスは、ゲートウェイの構成時に SensorTag デバイスにマップします。

### <a name="build-the-gateway-sdk-on-your-edison-device"></a>Edison デバイスで Gateway SDK をビルドする

Edison 上の **git** のバージョンでは、サブモジュールはサポートされていません。 Gateway SDK のソース全体を Edison にダウンロードするには、次の 2 つの方法があります。

- 方法 1: [Microsoft Azure IoT Gateway SDK][lnk-sdk] リポジトリを Edison 上で複製し、各サブモジュールについては手動でリポジトリを複製する。
- 方法 2: [Microsoft Azure IoT Gateway SDK][lnk-sdk] リポジトリを、**git** でサブモジュールがサポートされているデスクトップ デバイス上で複製し、サブモジュールを含む完全なリポジトリを Edison にコピーする。

方法 2 を選択した場合は、次の **git** コマンドを使用して Gateway SDK とそのすべてのサブモジュールを複製します。

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

その後、ローカル リポジトリ全体を zip 形式で圧縮して 1 つのアーカイブ ファイルにしてから、Edison にコピーする必要があります。 **Putty** に付属する **pscp** などのユーティリティを使用すると、アーカイブ ファイルを Edison にコピーできます。 次に例を示します。

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Gateway SDK リポジトリの完全なコピーを Edison 上に用意したら、SDK が含まれているフォルダーから次のコマンドを使用してビルドすることができます。

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Edison デバイスで BLE サンプルを構成して実行する

サンプルを起動して実行するには、ゲートウェイに関与しているモジュールをそれぞれ構成する必要があります。 この構成は JSON ファイル形式で指定し、5 個の関連モジュールすべてを構成することが必要です。 リポジトリ内には **gateway_sample.json** という名前のサンプル JSON ファイルが用意されており、独自の構成ファイルを構築するための雛形として使用することができます。 このファイルは、Gateway SDK リポジトリのローカル コピー内の **samples/ble_gateway_hl/src** フォルダーにあります。

以降のセクションでは、この構成ファイルを BLE サンプル用に編集する方法を説明しており、Gateway SDK リポジトリが Edison デバイスの **/home/root/azure-iot-gateway-sdk/** フォルダーにあることを前提としています。 リポジトリが他の場所にある場合は、パスを適宜修正する必要があります。

#### <a name="logger-configuration"></a>ロガーの構成

ゲートウェイ リポジトリが **/home/root/azure-iot-gateway-sdk/**フォルダーにあると仮定し、ロガー モジュールを次のように構成します。

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>BLE モジュールの構成

BLE デバイスの構成サンプルでは、Texas Instruments SensorTag デバイスを BLE デバイスとして想定しています。 GATT 周辺機器として動作する標準 BLE デバイスはすべて使用できますが、GATT 特性の ID とデータを更新することが必要になります (書き込み命令の場合)。 SensorTag デバイスの MAC アドレスを次のように追加します。 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
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
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>ID マッピング モジュールの構成

SensorTag デバイスの MAC アドレスと、IoT Hub に追加した **SensorTag_01** デバイスのデバイス ID とキーを追加します。

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
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
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>ルーティング構成

次の構成は、次のことを保証します。
- **Logger** モジュールがすべてのメッセージを受信し、それらを記録します。
- **SensorTag** モジュールが **mapping** モジュールと **BLE Printer** モジュールの両方にメッセージを送信します。
- **mapping** モジュールが IoT Hub に送信されるメッセージを **IoTHub** モジュールに送信します。
- **IoTHub** モジュールがメッセージを **mapping** モジュールに送り返します。
- **mapping** モジュールがメッセージを **SensorTag** モジュールに送り返します。

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

サンプルを実行するには、JSON 構成ファイルへのパスを渡して **ble_gateway_hl** バイナリを実行します。 **gateway_sample.json** ファイルを使用した場合、実行するコマンドは次のようになります。

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

このサンプルを実行する前に、SensorTag の小さいボタンを押して検出可能にすることが必要になる場合があります。

サンプルを実行する際に [Device Explorer または iothub-explorer][lnk-explorer-tools] ツールを使用すると、ゲートウェイによって SensorTag デバイスから転送されるメッセージを監視できます。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

BLE モジュールでは、Azure IoT Hub からデバイスへの命令の送信もサポートしています。 [Azure IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) または [IoT Hub Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) を使用して JSON メッセージを送信し、BLE ゲートウェイ モジュールから BLE デバイスに渡すことができます。 たとえば、Texas Instruments SensorTag デバイスを使用する場合、次の JSON メッセージを IoT Hub からデバイスに送信できます。

- すべての LED とブザーをリセットする (オフにする)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- I/O を "リモート" として構成する

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 赤色の LED をオンにする

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 緑色の LED をオンにする

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- ブザーをオンにする

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

HTTP プロトコルを使用するデバイスで IoT Hub に接続する場合の既定の動作では、25 分ごとに新しいコマンドを確認します。 そのため、複数のコマンドを個別に送信した場合、デバイスが各コマンドを受信するまでに 25 分の待機時間が発生します。

> [AZURE.NOTE] ゲートウェイは起動時にも毎回新しいコマンドを確認するため、ゲートウェイを停止して起動することでコマンドを強制的に処理させることができます。

## <a name="next-steps"></a>次のステップ

Gateway SDK に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

- [Azure IoT Gateway SDK][lnk-sdk]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!--HONumber=Oct16_HO2-->


