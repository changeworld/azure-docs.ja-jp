---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612854"
---
このチュートリアルでは、複数のコンポーネントを使用する IoT プラグ アンド プレイのサンプル デバイス アプリケーションをビルドし、それを IoT ハブに接続して、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは C で記述され、C 対応の Azure IoT device SDK に含められています。ソリューション ビルダーは、Azure IoT エクスプローラー ツールを使用すれば、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * サンプル コードをダウンロードします。
> * サンプル コードをビルドします。
> * サンプル デバイス アプリケーションを実行して、IoT ハブに接続することを確認します。
> * ソース コードを確認します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このチュートリアルは Linux または Windows で実行できます。 このチュートリアルのシェル コマンドは、パス区切り記号を "`/`" とする Linux 規則に従っています。Windows 方式に従う場合は、必ずこれらの区切り記号を "`\`" に置き換えてください。

前提条件は、次のように、オペレーティング システムによって異なります。

### <a name="linux"></a>Linux

このチュートリアルは、Ubuntu Linux を使用することを前提としています。 このチュートリアルの手順は、Ubuntu 18.04 を使用してテストされました。

このチュートリアルを Linux で完了するには、ご利用のローカルの Linux 環境に以下のソフトウェアをインストールする必要があります。

`apt-get` コマンドを使用して、**GCC**、**Git**、**cmake**、および必要なすべての依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。Visual Studio を [インストール](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)するときに、 **[C++ によるデスクトップ開発]** ワークロードを必ず含めてください。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/)。

## <a name="download-the-code"></a>コードのダウンロード

[Linux または Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (C)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、コードを既にダウンロードしています。

このチュートリアルでは、Azure IoT Hub Device C SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-c)の GitHub リポジトリをこの場所にクローンします。

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

この操作は、完了するまでに数分かかります。

## <a name="build-and-run-the-code"></a>コードのビルドと実行

コードをビルドして実行するには、Visual Studio を使用するか、コマンドラインで `cmake` を使用します。

### <a name="use-visual-studio"></a>Visual Studio を使用する

1. クローンされたリポジトリのルート フォルダーを開きます。 数秒後、プロジェクトを実行してデバッグするのに必要なものがすべて Visual Studio の **CMake** サポートによって作成されます。
1. Visual Studio の準備ができたら、**ソリューション エクスプローラー** で、サンプル *iothub_client/samples/pnp/pnp_temperature_controller/* に移動します。
1. *pnp_temperature_controller.c* ファイルを右クリックし、 **[Add Debug Configuration]\(デバッグ構成の追加\)** を選択します。 **[既定値]** を選択します。
1. Visual Studio によって、*launch.vs.json* ファイルが開かれます。 このファイルを、次のスニペットに示すように編集して、必要な環境変数を設定します。 「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](../articles/iot-pnp/set-up-environment.md)」を完了したときに、スコープ ID と登録プライマリ キーを書き留めました。

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. *pnp_temperature_controller.c* ファイルを右クリックし、 **[スタートアップ アイテムとして設定]** を選択します。
1. Visual Studio でコードの実行をトレースするには、*pnp_temperature_controller.c* ファイルの `main` 関数にブレークポイントを追加します。
1. これで、 **[デバッグ]** メニューからサンプルを実行およびデバッグできるようになりました。

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

### <a name="use-cmake-at-the-command-line"></a>コマンド ラインで `cmake` を使用する

サンプルをビルドするには、次の手順に従います。

1. クローンされたデバイス SDK のルート フォルダーに _cmake_ サブフォルダーを作成し、そのフォルダーに移動します。

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、SDK とサンプル用のプロジェクト ファイルを生成およびビルドします。

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md) を参照してください。

サンプルを実行するには

1. _cmake_ フォルダーから、実行可能ファイルを含むフォルダーに移動し、そのファイルを実行します。

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT Explorer を使用してコードを検証する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルでは、[複数のコンポーネント](../articles/iot-pnp/concepts-modeling-guide.md)を使用してモデルが実装されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

### <a name="iot-plug-and-play-helper-functions"></a>IoT プラグ アンド プレイ ヘルパー関数

このサンプルの場合、コードで使用されるいくつかのヘルパー関数は */common* フォルダーからのものです。

*pnp_device_client_ll* には、パラメーターとして `model-id` が指定される IoT プラグ アンド プレイ用の connect メソッドが含まれています: `PnP_CreateDeviceClientLLHandle`。

*pnp_protocol*: 以下の IoT プラグ アンド プレイ ヘルパー関数が含まれています。

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

これらのヘルパー関数は、独自のプロジェクトで使用するのに十分な汎用性を備えています。 このサンプルでそれらは、モデル内の各コンポーネントに対応する次の 3 つのファイル内で使用されます。

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

たとえば、*pnp_deviceinfo_component* ファイル内の `SendReportedPropertyForDeviceInformation` 関数では、次の 2 つのヘルパー関数が使用されています。

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

このサンプルの各コンポーネントは、このパターンに従っています。

### <a name="code-flow"></a>コード フロー

`main` 関数によって、接続が初期化され、モデル ID が送信されます。

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

このコードでは、`PnP_CreateDeviceClientLLHandle` を使用して IoT ハブに接続し、オプションとして `modelId` が設定され、ダイレクト メソッドおよびデバイス ツイン更新用にデバイス メソッドとデバイス ツイン コールバック ハンドラーが設定されます。

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` には接続情報も含まれます。 環境変数 **IOTHUB_DEVICE_SECURITY_TYPE** では、サンプルが IoT ハブに接続するのに接続文字列またはデバイス プロビジョニング サービスのどちらを使用するかが決定されます。

デバイスはモデル ID を送信すると、IoT プラグ アンド プレイ デバイスになります。

コールバック ハンドラーが配置されると、デバイスはツイン更新とダイレクト メソッド呼び出しに反応します。

* デバイス ツイン コールバックの場合は、`PnP_TempControlComponent_DeviceTwinCallback` によって、データを処理する `PnP_ProcessTwinData` 関数が呼び出されます。 `PnP_ProcessTwinData` は "*ビジター パターン*" を使用して JSON を解析してから、各プロパティにアクセスします。これにより、各要素に対して `PnP_TempControlComponent_ApplicationPropertyCallback` が呼び出されます。

* コマンド コールバックでは、`PnP_TempControlComponent_DeviceMethodCallback` 関数がヘルパー関数を使用して、コマンドおよびコンポーネントの名前を解析します。

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    次に、`PnP_TempControlComponent_DeviceMethodCallback` 関数は、コンポーネントに対してコマンドを呼び出します。

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main` 関数は、IoT ハブに送信される読み取り専用プロパティを初期化します。

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main` 関数は、各コンポーネントのイベントとテレメトリ データを更新するためのループに入ります。

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry` 関数を見れば、`PNP_THERMOSTAT_COMPONENT` 構造体の使用方法がわかります。 このサンプルでは、この構造体を使用して、気温コントローラー内の 2 個のサーモスタットに関する情報が格納されます。 このコードでは、`PnP_CreateTelemetryMessageHandle` 関数を使用して、メッセージが準備および送信されます。

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main` 関数によって最終的にさまざまなコンポーネントが破棄され、ハブへの接続が閉じられます。
