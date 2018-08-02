---
title: Windows IoT Core デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法を学習します。
author: miriambrus
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39eb6f137750f7f741c88dcdf9a55f34d24eaa59
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205750"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Windows IoT Core デバイスを Azure IoT Central アプリケーションに接続する

この記事では、デバイス開発者として、Windows IoT Core デバイスを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

1. **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
2. Windows 10 IoT Core オペレーティング システムを実行しているデバイス。 このチュートリアルでは、Raspberry Pi を使用します。


## <a name="sample-devkits-application"></a>**サンプル Devkit** アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Windows IoT Core** デバイス テンプレートが含まれています。 

- デバイスの**湿度**、**温度**、**圧力**の測定値を含むテレメトリ。 
- **ファン速度**を示す設定。
- デバイス プロパティ**ダイ番号**および**場所**クラウド プロパティを含むプロパティ。


デバイス テンプレートの構成について詳しくは、「[Windows IoT Core デバイス テンプレートの詳細](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)」をご覧ください。

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションでは、**Windows IoT Core** デバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="prepare-the-windows-iot-core-device"></a>Windows IoT Core デバイスを準備する

Windows IoT Core デバイスを設定するにはには、「Windows IoT Core デバイスを設定する」(https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device)) にあるステップ バイ ステップ ガイドに従ってください。

### <a name="add-a-real-device"></a>実デバイスを追加する

Azure IoT Central アプリケーションでは、**Windows IoT Core** デバイス テンプレートから実デバイスを追加し、デバイスの接続文字列を書きとめます。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

## <a name="prepare-the-windows-10-iot-core-device"></a>Windows 10 IoT Core デバイスを準備する

### <a name="what-youll-need"></a>必要なもの

Windows 10 IoT Core 物理デバイスを設定するには、まず Windows 10 IoT Core を実行しているデバイスを用意する必要があります。 Windows 10 IoT Core デバイスを設定する方法は、[ここで](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice)学習してください。

Azure IoT Central と通信できるクライアント アプリケーションも必要になります。 Azure SDK を使用して独自のカスタム アプリケーションを構築し、Visual Studio を使用してそれをデバイスにデプロイするか、または[事前に構築されたサンプル](https://developer.microsoft.com/en-us/windows/iot/samples)をダウンロードし、それを単純にデバイスにデプロイして実行することができます。 

### <a name="deploying-the-sample-client-application"></a>サンプル クライアント アプリケーションのデプロイ

前の手順のクライアント アプリケーションを準備するために、それを Windows 10 IoT デバイスにデプロイするには:

**接続文字列が、使用するクライアント アプリケーションのデバイスに格納されていることを確認する**
* デスクトップで、接続文字列を connection.string.iothub という名前のテキスト ファイルに保存します。
* このテキスト ファイルをデバイスのドキュメント フォルダーにコピーします。`[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

それが完了したら、任意のブラウザーに「http://[device-IP-address]:8080」と入力することによって、[Windows デバイス ポータル](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal)を開く必要があります。

そこから、下に示す操作を実行します。
1. 左側の [アプリ] ノードを展開します。
2. [Quick-run samples] (サンプルのクイック実行) をクリックします。
3. [Azure IoT Hub Client] (Azure IoT Hub クライアント) をクリックします。
4. [デプロイと実行] をクリックします。

![Windows デバイス ポータル上の Azure IoT Hub クライアントの GIF](./media/howto-connect-windowsiotcore/iothubapp.gif)

成功すると、デバイス上でアプリケーションが起動し、次のようになります。

![Azure IoT Hub クライアント アプリのスクリーンショット](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

Azure IoT Central では、Raspberry Pi 上で実行されているコードがアプリケーションとどのように対話するかを確認できます。

* 実デバイスの **[Measurements] (測定)** ページで、テレメトリを表示できます。
* **[プロパティ]** ページで、報告された [Die Number] (サイコロの数字) プロパティの値を表示できます。
* **[設定]** ページで、電圧やファン速度などの Raspberry Pi 上のさまざまな設定を変更できます。

## <a name="download-the-source-code"></a>ソース コードをダウンロードする

クライアント アプリケーションのソース コードを調査したり変更したりする場合は、それを GitHub の[ここ](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients)からダウンロードできます。 コードを変更する予定がある場合は、使用しているデスクトップ オペレーティング システムの readme ファイル[ここ](https://github.com/Microsoft/Windows-iotcore-samples)の手順に従う必要があります。

> [!NOTE]
> **git** が開発環境にインストールされていない場合は、それを [https://git-scm.com/download](https://git-scm.com/download) からダウンロードできます。

## <a name="windows-iot-core-device-template-details"></a>Windows IoT Core デバイス テンプレートの詳細

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Windows IoT Core** デバイス テンプレートが含まれています。

### <a name="telemetry-measurements"></a>テレメトリ測定

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>設定

数値設定

| 表示名 | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1,000    | 0       |


### <a name="properties"></a>Properties

| Type            | 表示名 | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| Text            | Location     | location   | 該当なし       |
