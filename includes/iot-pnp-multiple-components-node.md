---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 1d54429d901ca6caeadabfa7580e7270ceabcf45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612155"
---
このチュートリアルでは、複数のコンポーネントを使用する IoT プラグ アンド プレイのサンプル デバイス アプリケーションをビルドし、それを IoT ハブに接続して、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは Node.js 用に記述されており、Node.js 用 Azure IoT Hub デバイス SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * サンプル コードをダウンロードします。
> * サンプル デバイス アプリケーションを実行して、IoT ハブに接続することを確認します。
> * ソース コードを確認します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このチュートリアルを完了するには、開発用コンピューターに Node.js が必要です。 [nodejs.org](https://nodejs.org) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

## <a name="download-the-code"></a>コードのダウンロード

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (Node)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、リポジトリを既にクローンしています。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Node.js 用 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub リポジトリをこの場所にクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>必要なライブラリをインストールする

デバイス SDK を使用して、含まれているサンプル コードをビルドします。 ビルドするアプリケーションにより、IoT ハブに接続する、複数のコンポーネントを使用するプラグ アンド プレイ デバイスがシミュレートされます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. ローカル ターミナル ウィンドウで、クローンしたリポジトリのフォルダーに移動し、 */azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。 次に、次のコマンドを実行して、必要なライブラリをインストールします。

```cmd/sh
npm install
```

これにより、フォルダー内のサンプルを実行するために必要な関連する npm ファイルがインストールされます。

## <a name="review-the-code"></a>コードの確認

*azure-iot-sdk-node\device\samples\pnp* フォルダーに移動します。

*azure-iot-sdk-node\device\samples\pnp* フォルダーには、IoT プラグ アンド プレイの温度コントローラー デバイス用のサンプル コードが含まれています。

*pnpTemperatureController* ファイル内のコードにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](../articles/iot-pnp/concepts-modeling-guide.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

任意のコード エディターで *pnpTemperatureController.js* ファイルを開きます。 このサンプル コードは、次の方法を示しています。

- 実装されているデバイスの DTMI である、`modelId` を定義します。 この DTMI はユーザー定義であり、[温度コントローラー DTDL モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)の DTMI と一致している必要があります。

- 温度コントローラー DTDL モデルで定義されているコンポーネントを実装します。 実際の温度コントローラー内のコンポーネントによって、これら 2 つのインターフェイスが実装される必要があります。 これら 2 つのインターフェイスは、中央リポジトリで既に公開されています。 このサンプルには、次の 2 つのインターフェイスがあります。

  - Thermostat
  - Azure によって開発されたデバイスの情報

- コンポーネント名を定義します。 このサンプルには、2 つのサーモスタットと 1 つのデバイス情報コンポーネントが含まれています。

- コマンド名を定義します。 これらは、デバイスによって応答されるコマンドです。

- `serialNumber` 定数を定義します。 `serialNumber` は、特定のデバイスに固定されています。

- コマンド ハンドラーを定義します。

- コマンド応答を送信するための関数を定義します。

- コマンド要求をログに記録するためのヘルパー関数を定義します。

- プロパティを作成するためのヘルパー関数を定義します。

- プロパティ更新のリスナーを定義します。

- このデバイスからテレメトリを送信するための関数を定義します。 サーモスタットと既定のコンポーネントの両方によってテレメトリが送信されます。 この関数により、パラメーターとしてコンポーネント名が受信されます。

- 次のような `main` 関数を定義します。

  - デバイス SDK を使用してデバイス クライアントを作成し、IoT ハブに接続します。 IoT Hub によりデバイスが IoT プラグ アンド プレイ デバイスとして識別できるように、デバイスによって `modelId` が提供されます。

  - `onDeviceMethod` 関数を使用するコマンド要求のリッスンを開始します。 関数によって、サービスからのコマンド要求のリスナーが設定されます。

    - デバイス DTDL によって、`reboot` および `getMaxMinReport` コマンドが定義されます。
    - `commandHandler` 関数によって、コマンドに対するデバイスの応答方法が定義されます。

  - `setInterval` と `sendTelemetry` を使用するテレメトリの送信を開始します。

  - `helperCreateReportedPropertiesPatch` 関数を使用してプロパティを作成し、`updateComponentReportedProperties` を使用してプロパティを更新します。

  - `desiredPropertyPatchListener` を使用して、プロパティの更新をリッスンします。

  - すべてのリスナーとタスクを無効にし、**Q** または **q** キーが押されたときにループを終了します。

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md) を参照してください。

コードを確認したので、次のコマンドを使用してサンプルを実行します。

```cmd\sh
node pnpTemperatureController.js
```

次の出力が表示されます。これは、デバイスがハブへの利用統計情報の送信を開始し、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

![デバイス確認メッセージ](media/iot-pnp-multiple-components-node/multiple-component.png)

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
