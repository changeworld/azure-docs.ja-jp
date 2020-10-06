---
title: IoT プラグ アンド プレイのサンプル C# デバイス コードを IoT Hub に接続する | Microsoft Docs
description: IoT ハブに接続する IoT プラグ アンド プレイのサンプル デバイス コードを Windows 上でビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d1deac1c7932a8f3cec06d9c264ba401f7f1341d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577035"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは C# で記述されており、C# 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows 上でこのクイックスタートを実行するには、開発用マシンに次のソフトウェアがインストールされている必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>コードのダウンロード

このクイックスタートでは、Azure IoT Hub Device C# SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Microsoft Azure IoT Samples for .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリをこの場所にクローンします。

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>コードのビルド

これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

1. Visual Studio 2019 で、*azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* プロジェクト ファイルを開きます。

1. Visual Studio で、 **[プロジェクト] > [Thermostat のプロパティ] > [デバッグ]** の順に移動します。 プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [環境の設定](set-up-environment.md)の完了時に書き留めておいた値 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [環境の設定](set-up-environment.md)の完了時に書き留めておいた値 |

これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

Windows 上の Visual Studio でコードの実行をトレースするには、program.cs ファイルの `main` 関数にブレーク ポイントを追加します。

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、単純な IoT プラグ アンド プレイのサーモスタット デバイスが実装されます。 このサンプルにより実装されるモデルによって、IoT プラグ アンド プレイ [コンポーネント](concepts-components.md)は使用されません。 [サーモスタット デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

デバイス コードにより、標準の `CreateFromConnectionString` メソッドが使用され、IoT ハブに接続されます。 デバイスにより、接続要求で実装される DTDL モデルのモデル ID が送信されます。 モデル ID を送信するデバイスは、IoT プラグ アンド プレイ デバイスです。

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

モデル ID は、次のスニペットに示すように、コードに格納されます。

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

プロパティの更新、コマンドの処理、テレメトリの送信を行うコードは、IoT プラグ アンド プレイ規則を使用しないデバイスのコードと同じです。

IoT ハブから送信されたペイロード内の JSON オブジェクトを解析するために、このサンプルによって JSON ライブラリが使用されます。

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
