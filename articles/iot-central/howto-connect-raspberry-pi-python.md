---
title: Raspberry Pi を Azure IoT Central アプリケーションに接続する (Python) | Microsoft Docs
description: デバイス開発者として、Python を使用して Raspberry Pi を Azure IoT Central アプリケーションに接続する方法。
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205590"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Raspberry Pi を Azure IoT Central アプリケーションに接続する (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

この記事では、デバイス開発者として、Python プログラミング言語を使用して Raspberry Pi を Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

* **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
* Raspbian オペレーティング システムを実行している Raspberry Pi デバイス。 GUI 環境にアクセスするには、Raspberry Pi に接続されているモニター、キーボード、およびマウスが必要です。 Raspberry Pi から[インターネットに接続](https://www.raspberrypi.org/learning/software-guide/wifi/)できる必要があります。
* 必要に応じて、Raspberry Pi 用の [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) アドオン ボード。 このボードは、Azure IoT Central アプリケーションに送信するテレメトリ データをさまざまなセンサーから収集します。 **Sense Hat** ボードがない場合は、代わりにエミュレーターを使用できます。

## <a name="sample-devkits-application"></a>**サンプル Devkit** アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。 

- デバイスの**湿度**、**温度**、**圧力**、**磁力計** (X、Y、Z 軸に沿って測定)、**加速度計** (X、Y、Z 軸に沿って測定)、**ジャイロスコープ** (X、Y、Z 軸に沿って測定) の測定値を含むテレメトリ。
- **電圧**、**電流**、**ファン速度**、**IR** のトグルを示す設定。
- デバイス プロパティ**ダイ番号**および**場所**クラウド プロパティを含むプロパティ。


デバイス テンプレートの構成について詳しくは、「[Raspberry PI デバイス テンプレートの詳細](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)」をご覧ください。
    

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションでは、**Raspberry Pi** デバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="configure-the-raspberry-pi"></a>Raspberry Pi を構成する

次の手順では、GitHub からサンプル Python アプリケーションをダウンロードして構成する方法について説明します。 このサンプル アプリケーションには次の機能があります。

* テレメトリおよびプロパティの値を Azure IoT Central に送信する。
* Azure IoT Central で行われた設定変更に応答する。

> [!NOTE]
> Raspberry Pi Python のサンプルに関する詳細については、GitHub の [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) ファイルを参照してください。

1. Raspberry Pi デスクトップの Web ブラウザーを使用して、[Azure IoT Central ファームウェア リリース](https://github.com/Microsoft/microsoft-iot-central-firmware/releases)のページに移動します。

1. 最新のファームウェアを含む zip ファイルを Raspberry Pi 上のホーム フォルダーにダウンロードします。 ファイル名は `RaspberryPi-IoTCentral-X.X.X.zip` のようになっています。

1. ファームウェア ファイルを解凍するには、Raspberry Pi デスクトップの **File Manager** (ファイル マネージャー) を使用します。 zip ファイルを右クリックし、**[Extract here] (ここに抽出)** を選択します。 この操作により、`RaspberryPi-IoTCentral-X.X.X` というフォルダーがホーム フォルダーに作成されます。

1. Raspberry Pi に **Sense Hat** ボードが接続されていない場合は、エミュレーターを有効にする必要があります。
    1. **File Manager (ファイル マネージャー)** の `RaspberryPi-IoTCentral-X.X.X` フォルダーで **config.iot** ファイルを右クリックし、**[Text Editor] (テキスト エディター)** を選択します。
    1. `"simulateSenseHat": false,` の行を `"simulateSenseHat": true,` に変更します。
    1. 変更を保存し、**Text Editor (テキスト エディター)** を閉じます。

1. **ターミナル** セッションを開始し、`cd` コマンドを使用して、前の手順で作成したフォルダーに移動します。

1. サンプル アプリケーションの実行を開始するには、**ターミナル** ウィンドウに「`./start.sh`」と入力します。 **Sense HAT エミュレーター**を使用している場合は、その GUI が表示されます。 GUI を使用して、Azure IoT Central アプリケーションに送信されるテレメトリ値を変更することができます。

1. **ターミナル** ウィンドウに `Device information being served at http://192.168.0.60:8080` のようなメッセージが表示されます。 URL は環境によって異なる場合があります。 URL をコピーし、Web ブラウザーを使用して構成ページに移動します。

    ![デバイスの構成](media/howto-connect-raspberry-pi-python/configure.png)

1. 実デバイスを Azure IoT Central アプリケーションに追加したときに書きとめたデバイス接続文字列を入力します。 次に、**[Configure Device] (デバイスの構成)** を選択します。 **[Device configured, your device should start sending data to Azure IoT Central momentarily] (デバイスが構成されました。デバイスはすぐに Azure IoT Central へのデータ送信を開始します)** というメッセージが表示されます。

1. Azure IoT Central アプリケーションでは、Raspberry Pi 上で実行されているコードがアプリケーションとどのように対話するかを確認できます。

    * 実デバイスの **[Measurements] (測定)** ページで、Raspberry Pi から送信されたテレメトリを表示できます。 **Sense HAT エミュレーター**を使用している場合は、Raspberry Pi の GUI でテレメトリの値を変更することができます。
    * **[プロパティ]** ページで、報告された **[Die Number] (サイコロの数字)** プロパティの値を表示できます。
    * **[設定]** ページで、電圧やファン速度などの Raspberry Pi 上のさまざまな設定を変更できます。 Raspberry Pi で変更が承認されると、設定が**同期**されたことが Azure IoT Central に表示されます。


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI デバイス テンプレートの詳細

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。

### <a name="telemetry-measurements"></a>テレメトリ測定

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1,000    | 0              |
| magnetometerY  | mgauss | -1000   | 1,000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1,000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>設定

数値設定

| 表示名 | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電圧      | setVoltage | ボルト | 0              | 0       | 240     | 0       |
| Current      | setCurrent | アンペア  | 0              | 0       | 100     | 0       |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1,000    | 0       |

トグル設定

| 表示名 | フィールド名 | オンテキスト | オフテキスト | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | オフ     |

### <a name="properties"></a>Properties

| Type            | 表示名 | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| Text            | Location     | location   | 該当なし       |

## <a name="next-steps"></a>次の手順

ここでは、Raspberry Pi を Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は以下のとおりです。

* [汎用の Node.js クライアント アプリケーションを Azure IoT Central に接続する](howto-connect-nodejs.md)
