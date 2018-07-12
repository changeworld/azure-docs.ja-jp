---
title: 'IoT DevKit をクラウドへ: IoT DevKit AZ3166 をリモート監視 IoT ソリューション アクセラレータに接続する | Microsoft Docs'
description: このチュートリアルでは、IoT DevKit AZ3166 上のセンサーの状態を監視および視覚化するためにリモート監視 IoT ソリューション アクセラレータに送信する方法を説明します。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: e900b952ab9bb2054b9e4174670894027cdd2618
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969454"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>MXChip IoT DevKit AZ3166 を IoT リモート監視ソリューション アクセラレータに接続する


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、DevKit でサンプル アプリを実行して、センサー データをソリューション アクセラレータに送信する方法を説明します。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 [Arduino 向け Visual Studio Code 拡張機能](https://aka.ms/arduino)を使用して開発できます。 また、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備


## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring サンプルを開く

1. 接続されている場合は、コンピューターから DevKit を切断します。

2. VS Code を起動します。

3. DevKit をコンピューターに接続します。 VS Code により DevKit が自動的に検出され、次のページが開きます。
  * DevKit 概要ページ
  * Arduino の例: DevKit の使用を開始するためのハンズオン サンプル

4. 左側の **[Arduino Examples]\(Arduino の例\)** セクションを展開し、**[Examples for MXCHIP AZ3166]\(MXCHIP AZ3166 の例\) > [AzureIoT]** を参照して、**[RemoteMonitoringv2]** を選択します。 プロジェクト フォルダーを含む新しい VS Code ウィンドウが開きます。

  ![リモート監視プロジェクトを開く](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > ウィンドウを偶然閉じた場合は、再度開くことができます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="add-a-new-physical-device"></a>新しい物理デバイスを追加する

ポータルで **[デバイス]** セクションに移動し、**[新規デバイス]** ボタンをクリックします。 

![新しいデバイスを追加する](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

*[new device form]\(新規デバイス フォーム\)* に入力する必要があります。
1. *[デバイスの種類]* セクションで **[物理]** をクリックします。
2. 独自のデバイス ID を定義します (たとえば *MXChip* または *AZ3166* など)。
3. *[認証キー]* セクションで **[キーの自動生成]** を選択します。
4. *[適用]* ボタンをクリックします。

![新規デバイス フォームを追加する](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

ポータルで、新しいデバイスのプロビジョニングが終了するまで待機します。

![新しいデバイスをプロビジョニングする ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


次に、新しいデバイスの構成が表示されます。
生成された**接続文字列**をコピーします。

![デバイスの接続文字列](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


この接続文字列は、次のセクションで必要になります。





## <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

Visual Studio Code に戻ります。 

1. `Ctrl+P` (macOS: `Cmd + P`) を使用し、「**task config-device-connection**」と入力します。

  ![Azure サブスクリプションと IoT Hub を選択します。](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. ターミナルから、IoT デバイスの接続文字列を使用するかどうか確認するメッセージが表示されます。 *[新規作成]* を選択して貼り付けます。

  ![接続文字列を貼り付ける](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. ターミナルによって、構成モードを開始するよう求められる場合があります。 構成モードを開始するには、A ボタンを押しながらリセット ボタンを押して放してから、A ボタンを放します。画面には、DevKit ID と [構成] が表示されます。

  ![デバイス DevKit 画面](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > このチュートリアルの最後のセクションに従っている場合は、接続文字列をクリップボードに保存してください。 それ以外の場合は、Azure Portal に移動し、リモート監視リソース グループの IoT Hub を検索します。 ここには、IoT Hub が接続されているデバイスが表示されますので、デバイスの接続文字列をコピーします。

  ![接続文字列を検索する](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


VS Code セクションの [Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) に新しい物理デバイスが表示されます。

![新しい IoT Hub デバイスを確認する](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>プロジェクトのテスト

サンプル アプリの実行時に、DevKit によって Wi-Fi 経由でセンサー データが IoT ソリューション アクセラレータに送信されます。 結果を表示するには、次の手順に従います。

1. IoT ソリューション アクセラレータに移動し、**[DASHBOARD]** をクリックします。

2. IoT ソリューション アクセラレータ コンソールに DevKit センサーの状態が表示されます。 

![IoT ソリューション アクセラレータのセンサー データ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

センサー名 (AZ3166) をクリックすると、ダッシュ ボードの右側に開いたタブに MX チップ センサー グラフがリアルタイムで表示されます。


## <a name="send-a-c2d-message"></a>C2D メッセージを送信する
Remote Monitoring v2 を使用すると、デバイス上のリモート メソッドを呼び出すことができます。
MX チップのコード例では、センサーが選択されたときにメソッド セクションに表示される 3 つのメソッドを発行します。

![メソッド  MX チップ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

"LedColor" メソッドを使用して MX チップ LED の 1 つの色を変更することができます。 それには、デバイスのチェックボックスをオンにして、[スケジュール] ボタンをクリックします。 

![メソッド  MX チップ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

すべてのメソッドが表示されるドロップダウン リストで ChangeColor メソッドを選択します。名前を記入し、[適用] をクリックします。

![ドロップダウン  MX チップ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

数秒経つと、物理 MX チップにより RGB LED (A ボタンの下) の色が変更されます。

![LED  MX チップ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>デバイス ID の変更

IoT Hub でデバイス ID を変更するには、[このガイド](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)に従います。


## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからお問い合わせください。

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

ここでは、DevKit デバイスを IoT ソリューション アクセラレータに接続して、センサー データを視覚化する方法を説明しました。推奨する次の手順は、以下のとおりです。

* [IoT ソリューション アクセラレータの概要](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip IoT DevKit デバイスを Microsoft IoT Central アプリケーションに接続する](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
