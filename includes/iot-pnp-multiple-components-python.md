---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 94451cfefefe30bbae1748844f9303b2cfdd7be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612057"
---
このチュートリアルでは、複数のコンポーネントを使用する IoT プラグ アンド プレイのサンプル デバイス アプリケーションをビルドし、それを IoT ハブに接続して、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは Python で記述されており、Python 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * サンプル コードをダウンロードします。
> * サンプル デバイス アプリケーションを実行して、IoT ハブに接続することを確認します。
> * ソース コードを確認します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このチュートリアルを完了するには、開発用コンピューターに Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認することができます。  

```cmd/sh
python --version
```

[python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

## <a name="download-the-code"></a>コードのダウンロード

**azure-iot-device** パッケージは PIP として公開されています。

ローカルの Python 環境で、次のようにパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (Python)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、リポジトリを既にクローンしています。

Python SDK IoT リポジトリをクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](../articles/iot-pnp/concepts-modeling-guide.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

*azure-iot-sdk-python\azure-iot-device\samples\pnp* フォルダーには、IoT プラグ アンド プレイ デバイスのサンプル コードが含まれています。 温度コントローラー サンプルのファイルは次のとおりです。

- temp_controller_with_thermostats.py
- pnp_helper.py

温度コントローラーには、温度コントローラー DTDL モデルに基づく、複数のコンポーネントと既定のコンポーネントがあります。

任意のエディターで *temp_controller_with_thermostats.py* ファイルを開きます。 このファイルのコードによって、次のことが行われます。

1. ヘルパー メソッドにアクセスするために `pnp_helper.py` がインポートされます。

1. DTDL モデルで定義されている、2 つの異なるインターフェイスを一意に表す 2 つの Digital Twin Model Identifier (DTMI) が定義されます。 実際の温度コントローラー内のコンポーネントによって、これら 2 つのインターフェイスが実装される必要があります。 これら 2 つのインターフェイスは、中央リポジトリで既に公開されています。 これらの DTMI は、ユーザーに認識されている必要があり、デバイス実装のシナリオによって異なります。 現在のサンプルの場合、これら 2 つのインターフェイスは以下を表します。

    - サーモスタット
    - Azure によって作成されたデバイス情報。

1. 実装されているデバイスの DTMI `model_id` が定義されます。 DTMI はユーザー定義であり、DTMI モデル ファイル内の DTMI と一致している必要があります。

1. DTDL ファイル内のコンポーネントに指定された名前が定義されます。 2 つのサーモスタット (DTDL 内) と 1 つのデバイス情報コンポーネントがあります。 `serial_number` という定数は、既定のコンポーネントでも定義されています。 デバイスの `serial_number` を変更することはできません。

1. コマンド ハンドラーの実装が定義されます。 これらにより、コマンド要求を受信したときのデバイスの動作が定義されます。

1. コマンド応答を作成するための関数が定義されます。 これらにより、コマンド要求に対して、デバイスによってどのように応答されるかが定義されます。 コマンドによりカスタム応答を IoT ハブに戻す必要がある場合は、コマンドの応答関数を作成します。 コマンドの応答関数が指定されていない場合は、一般的な応答が送信されます。 このサンプルでは、**getMaxMinReport** コマンドにのみカスタム応答があります。

1. このデバイスからテレメトリを送信するための関数が定義されます。 サーモスタットと既定のコンポーネントの両方によってテレメトリが送信されます。 テレメトリを送信したコンポーネントを識別できるように、この関数によって、省略可能なコンポーネント名のパラメーターが受け取られます。

1. コマンド要求のリスナーが定義されます。

1. 必要なプロパティ更新のリスナーが定義されます。

1. 次のような `main` 関数があります。

    - デバイス SDK を使用してデバイス クライアントを作成し、IoT ハブに接続します。 IoT ハブによりデバイスが IoT プラグ アンド プレイ デバイスとして識別できるように、デバイスによって `model_id` が送信されます。

    - ヘルパー ファイルの `create_reported_properties` 関数を使用して、プロパティを作成します。 コンポーネント名、およびプロパティをキー値のペアとして、この関数に渡します。

    - `patch_twin_reported_properties` を呼び出すことによって、そのコンポーネントの読み取り可能なプロパティを更新します。

    - `execute_command_listener` 関数を使用するコマンド要求のリッスンを開始します。 この関数により、サービスからのコマンド要求のリスナーが設定されます。 リスナーを設定するときに、`method_name`、`user_command_handler`、および省略可能な `create_user_response_handler` をパラメーターとして指定します。
        - `method_name` によってコマンド要求が定義されます。 このサンプルでは、モデルによって、コマンドの **reboot**、および **getMaxMinReport** が定義されます。
        - `user_command_handler` 関数により、コマンドを受信したときにデバイスによって行われる必要がある内容が定義されます。
        - `create_user_response_handler` 関数により、コマンドが正常に実行されたときに IoT ハブに送信される応答が作成されます。 この応答はポータルで確認できます。 この関数が指定されていない場合は、一般的な応答がサービスに送信されます。

    - `execute_property_listener` を使用して、プロパティの更新をリッスンします。

    - `send_telemetry` を使用するテレメトリの送信が開始されます。 サンプル コードにより、3 つのテレメトリの送信関数を呼び出すためにループが使用されます。 それぞれが 8 秒ごとに呼び出されます。

    - すべてのリスナーとタスクを無効にし、**Q** または **q** キーが押されたときにループを終了します。

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) を参照してください。

次のコマンドを使用して、サンプルを実行します。

```cmd/sh
python temp_controller_with_thermostats.py
```

サンプル デバイスによって、数秒ごとにテレメトリ メッセージが IoT ハブに送信されます。

次の出力が表示されます。これは、デバイスによってハブにテレメトリ データが送信され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

![デバイス確認メッセージ](media/iot-pnp-multiple-components-python/multiple-component.png)

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
