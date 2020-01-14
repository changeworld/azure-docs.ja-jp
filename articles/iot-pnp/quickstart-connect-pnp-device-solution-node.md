---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする | Microsoft Docs
description: Node.js を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスと接続してやりとりします。
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550742"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT プラグ アンド プレイ プレビューを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスの機能とやりとりできるので、IoT が簡略化されます。 このクイックスタートでは、Node.js を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、開発用マシンに Node.js が必要です。 [nodejs.org](https://nodejs.org) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご使用のハブに対する _IoT ハブ接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、Node.js 内に IoT プラグ アンド プレイ デバイスとして記述された環境センサーのサンプルを使用します。 次の手順では、デバイスをインストールして実行する方法を示します。

1. 任意のディレクトリでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Node.js 用 Azure IoT サンプル](https://github.com/azure-samples/azure-iot-samples-node)の GitHub リポジトリをこの場所にクローンします。

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. これで、このターミナル ウィンドウは "_デバイス_" ターミナルとして使用されるようになります。 クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-node/digital-twins/Quickstarts/Device** フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. _デバイス接続文字列_を構成します。

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 次のコマンドを実行して、サンプルを実行します。

    ```cmd/sh
    node sample_device.js
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これは、デバイスがハブへの利用統計情報の送信を開始し、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。後でサービスのサンプルも動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、Node.js 内の IoT ソリューションのサンプルを使用してサンプル デバイスとやりとりします。

1. 別のターミナル ウィンドウを開きます (これは "_サービス_" ターミナルになります)。 複製したリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-node/digital-twins/Quickstarts/Service** フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. "_IoT ハブ接続文字列_" を構成して、サービスがそれに接続できるようにします。

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. そのターミナルで "_デバイス_" に接続すると、そのオンライン状態を示す次のメッセージが表示されます。

    ```cmd/sh
    reported state property as online
    ```

1. **/azure-iot-samples-node/digital-twins/Quickstarts/Service** フォルダーで、**get_digital_twin.js** ファイルを開きます。 `<DEVICE_ID_GOES_HERE>` プレースホルダーをご利用のデバイス ID に置き換え、ファイルを保存します。

1. "_サービス_" ターミナルにアクセスし、次のコマンドを使用して、デバイス情報を読み取るためのサンプルを実行します。

    ```cmd/sh
    node get_digital_twin.js
    ```

1. "_サービス_" ターミナル出力内の `environmentalSensor` コンポーネントまでスクロールします。 `state` プロパティが _online_ として報告されていることがわかります。

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. ファイル **update_digital_twin_property.js** を開きます。

1. ファイルの先頭には、大文字のプレースホルダーを使用して定義された一連の定数があります。 `<DEVICE_ID_GOES_HERE>` プレースホルダーを実際のデバイス ID に置き換え、残りの定数を次の値で更新して、ファイルを保存します。

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. "_サービス_" ターミナルにアクセスし、次のコマンドを使用して、プロパティを更新するためのサンプルを実行します。

    ```cmd/sh
    node update_digital_twin_property.js
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
            "value": "online"
          }
        }
      }
    }
    ```

1. _デバイス_ ターミナルにアクセスして、デバイスが更新プログラムを受信済みであることを確認します。

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. "_サービス_" ターミナルに戻り、次のコマンドを実行してデバイス情報を再取得し、プロパティが更新されたことを確認します。
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. "_サービス_" ターミナル出力の `environmentalSensor` コンポーネントの下に、brightness の更新された値が報告されていることがわかります。 注: デバイスが更新を完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が実際に処理されるまで、この手順は繰り返すことができます。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. ファイル **invoke_command.js** を開きます。

1. ファイルの先頭にある `<DEVICE_ID_GOES_HERE>` プレースホルダーを実際のデバイス ID に置き換えます。 残りの定数を次の値で更新して、ファイルを保存します。

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. "_サービス_" ターミナルにアクセスします。 次のコマンドを使用して、コマンドを呼び出すためのサンプルを実行します。

    ```cmd/sh
    node invoke_command.js
    ```

1. "_サービス_" ターミナルの出力に、次の確認が表示されます。

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. _デバイス_ ターミナルにアクセスすると、コマンドが確認済みであることがわかります。

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
