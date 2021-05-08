---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611972"
---
このチュートリアルでは、複数のコンポーネントを使用する IoT プラグ アンド プレイのサンプル デバイス アプリケーションをビルドし、それを IoT ハブに接続して、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは C# で記述されており、C# 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * サンプル コードをダウンロードします。
> * サンプル コードをビルドします。
> * サンプル デバイス アプリケーションを実行して、IoT ハブに接続することを確認します。
> * ソース コードを確認します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>サンプル コードを使用して SDK リポジトリをクローンする

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (C#)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、リポジトリを既にクローンしています。

Azure IoT Samples for C# GitHub リポジトリからサンプルをクローンします。 任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Microsoft Azure IoT samples for .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリをクローンします。

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、C# で記述されたサンプルの温度コントローラー デバイスを IoT プラグ アンド プレイ デバイスとして使用します。 サンプル デバイスを実行するには、次のようにします。

1. Visual Studio 2019 で、*azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* プロジェクト ファイルを開きます。

1. Visual Studio で、 **[プロジェクト] > [TemperatureController Properties]\(TemperatureController のプロパティ\) > [デバッグ]** の順に移動します。 次に、プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |


1. これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 Visual Studio のこのインスタンスは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](../articles/iot-pnp/concepts-modeling-guide.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

デバイス コードにより、標準の `CreateFromConnectionString` メソッドが使用され、IoT ハブに接続されます。 デバイスにより、接続要求で実装される DTDL モデルのモデル ID が送信されます。 モデル ID を送信するデバイスは、IoT プラグ アンド プレイ デバイスです。

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

モデル ID は、次のスニペットに示すように、コードに格納されます。

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

デバイスが IoT ハブに接続された後、コードによってコマンド ハンドラーが登録されます。 `reboot` コマンドは、既定のコンポーネントで定義されています。 `getMaxMinReport` コマンドは、次の 2 つのサーモスタット コンポーネントのそれぞれに定義されています。

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

2 つのサーモスタット コンポーネントには、必要なプロパティの更新用の個別のハンドラーがあります。

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

このサンプル コードによって、各サーモスタット コンポーネントからテレメトリが送信されます。

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync` メソッドによって、各コンポーネントのメッセージを作成するために `PnpHhelper` クラスが使用されます。

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` クラスには、複数コンポーネントのモデルで使用できるその他のサンプル メソッドが含まれています。

2 つのサーモスタット コンポーネントからのテレメトリとプロパティを表示するには、Azure IoT エクスプローラー ツールを使用します。

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT エクスプローラーの複数コンポーネントのデバイス":::

また、Azure IoT エクスプローラー ツールを使用して、2 つのサーモスタット コンポーネントのいずれか、または既定のコンポーネントでコマンドを呼び出すこともできます。
