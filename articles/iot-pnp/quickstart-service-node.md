---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ デバイスとやり取りする (Node.js) | Microsoft Docs
description: Node.js を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ デバイスと接続してやり取りします。
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 6ad6e48642e7b7df4b93b37b5ef66381833d8bbc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574995"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-nodejs"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスとやり取りする (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT プラグ アンド プレイを使用すると、基盤となるデバイスの実装に関する知識がなくてもデバイスの機能とやり取りできるので、IoT が簡略化されます。 このクイックスタートでは、Node.js を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

このクイックスタートを完了するには、開発用マシンに Node.js が必要です。 [nodejs.org](https://nodejs.org) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>サンプル コードを使用して SDK リポジトリをクローンする

[Node SDK リポジトリ](https://github.com/Azure/azure-iot-sdk-node)にあるサンプルをクローンします。 任意のフォルダーでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Node.js 用 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) の GitHub リポジトリをクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md) を参照してください。

このクイックスタートでは、Node.js に IoT プラグ アンド プレイ デバイスとして記述されたサンプルのサーモスタット デバイスを使用できます。 サンプル デバイスを実行するには、次のようにします。

1. ターミナル ウィンドウを開いて、GitHub からクローンした Microsoft Azure IoT SDK for Node.js リポジトリが格納されているローカル フォルダーに移動します。

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 クローンしたリポジトリのフォルダーに移動し、 */azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. 次のコマンドを使用して、サンプルのサーモスタット デバイスを実行します。

    ```cmd/sh
    node simple_thermostat.js
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](set-up-environment.md)」では、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このクイックスタートでは、Node.js 内の IoT ソリューションのサンプルを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。

1. クローンした Node SDK リポジトリで、 */azure-iot-sdk-node/service/samples/javascript* フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. **デバイス** ターミナルでサンプルのサーモスタット デバイスを実行したときに、そのオンライン状態を示す次のメッセージが表示されました。

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. "**サービス**" ターミナルにアクセスし、次のコマンドを使用して、デバイス情報を読み取るためのサンプルを実行します。

    ```cmd/sh
    node get_digital_twin.js
    ```

1. **サービス** ターミナル出力のデジタル ツインの応答に注目してください。 デバイスのモデル ID と関連プロパティが報告されているのがわかります。

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. 次のスニペットは、デバイス ツインのモデル ID を取得する、*get_digital_twin.js* のコードを示しています。

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

このシナリオでは、`Model Id: dtmi:com:example:Thermostat;1`が出力されます。

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. コード エディターで、*update_digital_twin.js* ファイルを開きます。

1. サンプル コードを確認します。 JSON 修正プログラムを作成して、デバイスのデジタル ツインを更新する方法を確認できます。 このサンプルでは、コードによって、サーモスタットの温度が 42 の値に置き換えられます。

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. **サービス** ターミナルで、次のコマンドを使用して、プロパティを更新するためのサンプルを実行します。

    ```cmd/sh
    node update_digital_twin.js
    ```

1. **デバイス** ターミナルで、デバイスによって更新情報が受信されていることを確認します。

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. **サービス** ターミナルで、次のコマンドを実行して、プロパティが更新されていることを確認します。

    ```cmd/sh
    node get_digital_twin.js
    ```

1. **サービス** ターミナル出力の `thermostat1` コンポーネントの下のデジタル ツイン応答で、更新されたターゲット温度が報告されているのがわかります。 デバイスでの更新が完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が処理されるまで、この手順は繰り返します。

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. *invoke_command.js* ファイルを開き、コードを確認します。

1. "**サービス**" ターミナルにアクセスします。 次のコマンドを使用して、コマンドを呼び出すためのサンプルを実行します。

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. **サービス** ターミナル出力に、次の確認が表示されます。

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. **デバイス** ターミナルで、コマンドが確認されていることがわかります。

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ モデリング開発者ガイド](concepts-developer-guide-device-csharp.md)
