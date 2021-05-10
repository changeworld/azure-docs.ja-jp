---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 30ea75a2df63fa935314fc103fe1e7e092f655b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612177"
---
このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは C# で記述されており、Azure IoT Samples for C# に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows 上でこのクイックスタートを実行するには、開発用マシンに次のソフトウェアがインストールされている必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>コードのダウンロード

このクイックスタートでは、Azure IoT Samples for C# リポジトリをクローンしてビルドするために使用できる開発環境を準備します。

任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリをこの場所にクローンします。

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
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |

これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

Windows 上の Visual Studio でコードの実行をトレースするには、program.cs ファイルの `main` 関数にブレーク ポイントを追加します。

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、単純な IoT プラグ アンド プレイのサーモスタット デバイスが実装されます。 このサンプルにより実装されるモデルによって、IoT プラグ アンド プレイ [コンポーネント](../articles/iot-pnp/concepts-modeling-guide.md)は使用されません。 [サーモスタット デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

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
