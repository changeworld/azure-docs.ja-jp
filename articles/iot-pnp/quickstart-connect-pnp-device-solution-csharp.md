---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする | Microsoft Docs
description: C# (.NET) を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスと接続してやり取りします。
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963973"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT プラグ アンド プレイ プレビューを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスの機能とやりとりできるので、IoT が簡略化されます。 このクイックスタートでは、C# (および .NET) を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、お使いの開発用マシンに .NET Core (2.x.x または 3.x.x) をインストールする必要があります。 「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core/)」から、複数のプラットフォームに対応する .NET Core SDK の好みのバージョンをダウンロードできます。

ローカル ターミナル ウィンドウで次のコマンドを実行すると、開発マシン上の .NET のバージョンを確認できます。 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご使用のハブに対する "_IoT ハブ接続文字列_" を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、C# で IoT プラグ アンド プレイ デバイスとして記述されたサンプル環境センサーを使用します。 次の手順では、デバイスをインストールして実行する方法を示します。

1. 任意のディレクトリでターミナル ウィンドウを開きます。 次のコマンドを実行して、[C# (.NET) 用 Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp)の GitHub リポジトリをこの場所にクローンします。

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. これで、このターミナル ウィンドウは "_デバイス_" ターミナルとして使用されるようになります。 クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** フォルダーに移動します。

1. "_デバイス接続文字列_" を構成します。

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 必要なパッケージをビルドし、次のコマンドを使用してサンプルを実行します。

    ```cmd\sh
        dotnet run
    ```

1. デバイスが正常に登録され、クラウドからの更新を待機していることを示すメッセージが表示されます。 これは、デバイスがコマンドとプロパティの更新情報を受信する準備が整い、ハブへの利用統計情報の送信を開始したことを示します。 このターミナルは閉じないでください。後でサービスのサンプルも動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、C# で記述されたサンプル IoT ソリューションを使用してサンプル デバイスとやり取りします。

1. 別のターミナル ウィンドウを開きます (これは "_サービス_" ターミナルになります)。 クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-csharp/digitaltwin/Samples/service** フォルダーに移動します。

1. "_IoT ハブ接続文字列_" と "_デバイス ID_" を構成して、サービスがこれらの両方に接続できるようにします。

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. そのターミナルで "_デバイス_" に接続すると、そのオンライン状態を示す次のメッセージが表示されます。

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. "_サービス_" ターミナルにアクセスし、次のコマンドを使用して、デバイス情報を読み取るためのサンプルを実行します。

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. "_サービス_" ターミナル出力内の `environmentalSensor` コンポーネントまでスクロールします。 デバイスがオンラインであるかどうかを示すために使用される `state` プロパティが _true_ として報告されていることがわかります。

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. "_サービス_" ターミナルにアクセスし、次の変数を設定して、更新するプロパティを定義します。
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. 次のコマンドを使用して、プロパティを更新するためのサンプルを実行します。

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. "_サービス_" ターミナル出力に、更新されたデバイス情報が表示されます。 `environmentalSensor` コンポーネントまでスクロールして、brightness の新しい値 42 を確認します。

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. "_デバイス_" ターミナルにアクセスして、デバイスが更新プログラムを受信済みであることを確認します。

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. "_サービス_" ターミナルに戻り、次のコマンドを実行してデバイス情報を再取得し、プロパティが更新されたことを確認します。
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. "_サービス_" ターミナル出力の `environmentalSensor` コンポーネントの下に、brightness の更新された値が報告されていることがわかります。 注: デバイスが更新を完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が実際に処理されるまで、この手順は繰り返すことができます。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. "_サービス_" ターミナルにアクセスし、次の変数を設定して、呼び出すコマンドを定義します。
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. 次のコマンドを使用して、コマンドを呼び出すためのサンプルを実行します。

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. "_サービス_" ターミナルの出力に、次の確認が表示されます。

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. "_デバイス_" ターミナルにアクセスすると、コマンドが確認済みであることがわかります。

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
