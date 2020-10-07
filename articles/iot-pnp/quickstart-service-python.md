---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ デバイスを操作する (Python) | Microsoft Docs
description: Python を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して操作します。
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574970"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>クイックスタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスを操作する (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT プラグ アンド プレイを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスのモデルを操作できるため、IoT が簡略化されます。 このクイックスタートでは、Python を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

このクイックスタートを完了するには、お使いの開発用マシン上に Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認できます。  

```cmd/sh
python --version
```

**azure-iot-device** パッケージは PIP として公開されています。

ローカルの Python 環境で、次のようにパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

次のコマンドを実行して、**azure-iot-hub** パッケージをインストールします。

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) を参照してください。

このクイックスタートでは、IoT プラグ アンド プレイ デバイスとして、Python で記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. 任意のフォルダーでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub リポジトリをこの場所に複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 複製したリポジトリのフォルダーに移動し、 */azure-iot-sdk-python/azure-iot-device/samples/pnp* フォルダーに移動します。

1. 次のコマンドを使用して、サンプルのサーモスタット デバイスを実行します。

    ```cmd/sh
    python simple_thermostat.py
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、Python のサンプルの IoT ソリューションを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。 

1. 複製された Python SDK リポジトリの */azure-iot-sdk-python/azure-iot-hub/samples* フォルダーに移動します。

1. この samples フォルダーには、Digital Twin Manager クラスを使用した操作をデモンストレーションする *get_digital_twin_sample.py、update_digitial_twin_sample.py、invoke_command_sample.py、および invoke_component_command_sample-.py* という 4 つのサンプル ファイルが含まれています。  これらのサンプルは、IoT プラグ アンド プレイ デバイスとやり取りするために、各 API をどのように使用するかを示しています。

### <a name="get-digital-twin"></a>デジタル ツインを取得する

[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](set-up-environment.md)に関するページでは、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このサンプルを実行するには、**サービス** ターミナルで次のコマンドを使用します。

```cmd/sh
python get_digital_twin_sample.py
```

出力には、デバイスのデジタル ツインが示され、そのモデル ID が出力されます。

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

次のスニペットは、*get_digital_twin_sample.py* からのサンプル コードを示しています。

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>デジタル ツインを更新する

このサンプルは、"*パッチ*" を使用して、デバイスのデジタル ツインを介してプロパティを更新する方法を示しています。 *update_digital_twin_sample.py* からの次のスニペットは、パッチを作成する方法を示しています。

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

このサンプルを実行するには、**サービス** ターミナルで次のコマンドを使用します。

```cmd/sh
python update_digital_twin_sample.py
```

以下の出力を示す**デバイス** ターミナルで更新が適用されていることを確認できます。

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

**サービス** ターミナルで、パッチが成功したことを確認します。

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>コマンドを呼び出す

コマンドを呼び出すには、サンプル *invoke_command_sample.py* を実行します。 このサンプルは、単純なサーモスタット デバイスでコマンドを呼び出す方法を示しています。 このサンプルを実行する前に、**サービス** ターミナルで `IOTHUB_COMMAND_NAME` および `IOTHUB_COMMAND_PAYLOAD` 環境変数を設定します。

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

**サービス** ターミナルで、次のコマンドを使用してこのサンプルを実行します。
  
```cmd/sh
python invoke_command_sample.py
```

**サービス** ターミナルで、デバイスからの確認メッセージが表示されます。

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

**デバイス** ターミナルで、デバイスがコマンドを受け取ったことを確認します。

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ モデリング開発者ガイド](concepts-developer-guide-device-csharp.md)
