---
title: DevKit デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法を学習します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ea9ff8f93ede3b9ec5e7eed83c6049b0c23de7e8
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205461"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する

この記事では、デバイス開発者として、MXChip IoT DevKit (DevKit) デバイスを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

1. **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
1. DevKit デバイス。 DevKit デバイスを購入するには、「[MXChip IoT DevKit](http://mxchip.com/az3166)」を参照してください。


## <a name="sample-devkits-application"></a>**サンプル Devkit** アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **MXChip** デバイス テンプレートが含まれています。 

- デバイスの**湿度**、**温度**、**圧力**、**磁力計** (X、Y、Z 軸に沿って測定)、**加速度計** (X、Y、Z 軸に沿って測定)、**ジャイロスコープ** (X、Y、Z 軸に沿って測定) の測定値を含むテレメトリ。
- **デバイス状態**の測定例を含む状態。
- **ボタン B 押下**イベントでのイベント測定。 
- **電圧**、**電流**、**ファン速度**、**IR** のトグルを示す設定。
- デバイス プロパティ**ダイ番号**および場所プロパティであり**メーカー** クラウド プロパティ内にある**デバイスの場所**を含むプロパティ。 


構成について詳しくは、「[MXChip デバイス テンプレートの詳細](howto-connect-devkit.md#mxchip-device-template-details)」をご覧ください。


## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションでは、**MXChip** デバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="prepare-the-devkit-device"></a>DevKit デバイスを準備する

> [!NOTE]
> 以前にデバイスを使用したことがあり、WiFi の資格情報が保存されていて、別の WiFi ネットワーク、接続文字列、またはテレメトリ測定を使用するようにデバイスを再構成するには、ボード上の **A** ボタンと **B** ボタンの両方を同時に押します。 それで機能しない場合は、**reset** ボタンを押して再試行します。

#### <a name="before-you-start-configuring-the-device"></a>デバイスの構成を開始する前に:
1. IoT Central の**サンプル Devkit**で、`Device Explorer`-> `select MXChip Template` -> `Click on +New and choose **Real** Device` -> `Connect this device` に移動します (右側) 
2. プライマリ接続文字列をコピーします。
3. 接続文字列を保存します。DevKit デバイスを準備するときに、インターネットから一時的に切断します。 


#### <a name="to-prepare-the-devkit-device"></a>DevKit デバイスを準備するには:


1. GitHub 上の[リリース](https://github.com/Azure/iot-central-firmware/releases) ページから、MXChip 用の事前に構築された最新の Azure IoT Central ファームウェアをダウンロードします。 リリース ページ上のダウンロード ファイル名は `AZ3166-IoT-Central-X.X.X.bin` のようになります。

1. USB ケーブルを使用して、DevKit デバイスを開発用コンピューターに接続します。 Windows では、DevKit デバイス上のストレージにマッピングされたドライブでファイル エクスプローラー ウィンドウが開きます。 たとえば、このドライブは **AZ3166 (D:)** と呼ばれることがあります。

1. **iotCentral.bin** ファイルをドライブ ウィンドウにドラッグします。 コピーが完了すると、デバイスが新しいファームウェアで再起動します。

1. DevKit デバイスが再起動すると、次の画面が表示されます。

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 画面に何か表示されている場合は、デバイスの **A** ボタンと **B** ボタンを同時に押して、デバイスを再起動します。 

1. これで、デバイスがアクセス ポイント (AP) モードになりました。 コンピューターまたはモバイル デバイスからこの WiFi アクセス ポイントに接続できます。

1. コンピューター、電話、またはタブレット上で、デバイスの画面に表示されている WiFi ネットワーク名に接続します。 このネットワークに接続するとき、インターネット アクセスは存在しません。 この状態は予測されたものであり、このネットワークにはデバイスを構成する間の短時間しか接続されません。

1. Web ブラウザーを開き、[http://192.168.0.1/start](http://192.168.0.1/start) に移動します。 次の Web ページが表示されます。

    ![デバイス構成ページ](media/howto-connect-devkit/configpage.png)

    Web ページで、次の操作を行います。 
    - WiFi ネットワークの名前を追加する 
    - WiFi ネットワークのパスワード
    - PIN コードがデバイス LCD に表示される 
    - デバイスの接続文字 (手順に従って既に保存しているはずです) 接続文字列は `https://apps.iotcentral.com` -> `Device Explorer` -> `Device` -> `Select or Create a new Real Device` -> `Connect this device` (右上) で確認できます
    - すべての使用可能なテレメトリ測定を選択する 

1. **[デバイスの構成]** を選択すると、次のページが表示されます。

    ![構成されたデバイス](media/howto-connect-devkit/deviceconfigured.png)

1. デバイス上の **Reset** ボタンを押します。



## <a name="view-the-telemetry"></a>テレメトリを表示する

DevKit デバイスが再起動すると、デバイスの画面に次のものが表示されます。

* 送信されたテレメトリ メッセージの数。
* エラーの数。
* 受信された必要なプロパティの数、および送信された報告されるプロパティの数。

デバイスを揺さぶると、送信された報告されるプロパティの数が増えます。 デバイスは、**[Die Number] (サイコロの数字)** デバイス プロパティとして乱数を送信します。

Azure IoT Central でテレメトリ測定や報告されるプロパティの値を表示したり、設定を構成したりできます。

1. **Device Explorer** を使用して、追加した MXChip 実デバイスの **[Measurements] (測定)** ページに移動します。

    ![実デバイスに移動する](media/howto-connect-devkit/realdevicenew.png)

1. **[Measurements] (測定)** ページで、MXChip デバイスから来たテレメトリを表示できます。

    ![実デバイスからのテレメトリを表示する](media/howto-connect-devkit/devicetelemetrynew.png)

1. **[プロパティ]** ページで、デバイスによって報告された最後のサイコロの数字とデバイスの場所を表示できます。

    ![デバイスのプロパティを表示する](media/howto-connect-devkit/devicepropertynew.png)

1. **[設定]** ページで、MXChip デバイス上の設定を更新できます。

    ![デバイス設定を表示する](media/howto-connect-devkit/devicesettingsnew.png)

1. **[ダッシュボード]** ページで、場所のマップを確認できます

    ![デバイス ダッシュボードの表示](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>ソース コードをダウンロードする

デバイス コードを調査したり変更したりする場合は、それを GitHub からダウンロードできます。 コードを変更する予定がある場合は、この手順に従って、使用しているデスクトップ オペレーティング システムの[開発環境を準備する](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)必要があります。

ソース コードをダウンロードするには、デスクトップ コンピューター上で次のコマンドを実行します。

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

前のコマンドは、ソース コードを `iot-central-firmware` という名前のフォルダーにダウンロードします。 

> [!NOTE]
> **git** が開発環境にインストールされていない場合は、それを [https://git-scm.com/download](https://git-scm.com/download) からダウンロードできます。

## <a name="review-the-code"></a>コードの確認

開発環境を準備したときにインストールされた Visual Studio Code を使用して、`iot-central-firmware` フォルダー内の `AZ3166` フォルダーを開きます。 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

テレメトリが Azure IoT Central アプリケーションにどのように送信されたかを確認するには、ソース フォルダー内の **main_telemetry.cpp** ファイルを開きます。

関数 `buildTelemetryPayload` は、デバイス上のセンサからのデータを使用して JSON テレメトリ ペイロードを作成します。

関数 `sendTelemetryPayload` は、その JSON ペイロードを Azure IoT Central アプリケーションが使用する IoT Hub に送信するために **iotHubClient.cpp** 内の `sendTelemetry` を呼び出します。

プロパティ値が Azure IoT Central アプリケーションにどのように報告されたかを確認するには、ソース フォルダー内の **main_telemetry.cpp** ファイルを開きます。

関数 `telemetryLoop` は、加速度計がダブル タップを検出したときに **doubleTap** 報告されるプロパティを送信します。 これは、**iotHubClient.cpp** ソース ファイル内の `sendReportedProperty` 関数を使用します。

**iotHubClient.cpp** ソース ファイル内のコードは、[C 用の Microsoft Azure IoT SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-c)からの関数を使用して IoT Hub と対話します。

サンプル コードを変更、構築、およびデバイスにアップロードする方法については、`AZ3166` フォルダー内の **readme.md** ファイルを参照してください。

## <a name="mxchip-device-template-details"></a>MXChip デバイス テンプレートの詳細 

サンプル Devkit アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ MXChip デバイス テンプレートが含まれています。

### <a name="measurements"></a>測定

#### <a name="telemetry"></a>テレメトリ 

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


#### <a name="states"></a>States 
| Name          | 表示名   | 正常 | 注意 | 危険 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | デバイス状態   | 緑  | オレンジ  | 赤    | 

#### <a name="events"></a>events 
| Name             | 表示名      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | ボタン B の押下  | 

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
| デバイス プロパティ | デバイスの場所   | location  | location    |
| Text            | メーカー     | manufacturedIn   | 該当なし       |



## <a name="next-steps"></a>次の手順

ここでは、DevKit デバイスを Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は以下のとおりです。

* [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md)
