---
title: Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoTハブ に接続する方法 | Microsoft Docs
description: IoT ハブに接続する IoT プラグ アンド プレイ ブリッジを、Linux または Windows 上でビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 958402e61f6dc81a3e6618dbcd4df4c8dd6b9ced
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793060"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoT Hub に接続する方法

この攻略ガイドでは、IoT プラグ アンド プレイ ブリッジのサンプル環境アダプターをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 IoT プラグ アンド プレイ ブリッジは C で記述されており、C の Azure IoT device SDK が含まれています。このチュートリアルを終了すると、IoT プラグ アンド プレイ ブリッジを実行し、報告されたテレメトリを Azure IoT エクスプローラー内で確認できるようになります。:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Iot プラグ アンド プレイ ブリッジから報告されたテレメトリ (湿度、気温) のテーブルが表示された Azure IoT エクスプローラーを示しているスクリーンショット。":::

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Linux または Windows で実行できます。 この攻略ガイドのシェル コマンドは、パス区切り記号を "`\`" とする Windows 規則に従っています。Linux 方式に従う場合は、必ずこれらの区切り記号を "`/`" に置き換えてください。

前提条件は、次のように、オペレーティング システムによって異なります。

### <a name="linux"></a>Linux

このクイックスタートは、Ubuntu Linux を使用していることを前提としています。 このクイックスタートの手順は、Ubuntu 18.04 を使用してテストされました。

このクイックスタートを Linux で完了するには、ご利用のローカルの Linux 環境に以下のソフトウェアをインストールする必要があります。

`apt-get` コマンドを使用して、 **GCC** 、 **Git** 、 **cmake** 、および必要なすべての依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、 **GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows でこのクイックスタートを完了するには、ご利用のローカル Windows 環境に以下のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。Visual Studio を [インストール](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)するときに、 **[C++ によるデスクトップ開発]** ワークロードを必ず含めてください。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT エクスプローラー

このクイックスタートのパート 2 でサンプル デバイスとやり取りするには、 **Azure IoT エクスプローラー** ツールを使用します。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する " _IoT Hub 接続文字列_ " を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> また、Azure IoT エクスプローラー ツールを使用して、IoT ハブ接続文字列を見つけることもできます。

次のコマンドを実行して、ハブに追加したデバイスの " _デバイス接続文字列_ " を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>モデルの表示

後のステップで Azure IoT エクスプローラーを使用して、IoT ハブへの接続時にデバイスを表示します。 Azure IoT エクスプローラーには、デバイスから送信される **モデル ID** と一致するモデル ファイルのローカル コピーが必要です。 モデル ファイルを使用すると、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドを IoT エクスプローラーに表示できます。

以下の手順でコードをダウンロードすると、`pnpbridge/docs/schema` フォルダーの下にサンプル モデル ファイルが含まれます。 Azure IoT エクスプローラーを準備するには:

1. ローカル コンピューターに " *モデル* " という名前のフォルダーを作成します。
1. [EnvironmentalSensor.json](https://aka.ms/iot-pnp-bridge-env-model) を表示し、JSON ファイルを " *モデル* " フォルダーに保存します
1. [RootBridgeSampleDevice.json](https://aka.ms/iot-pnp-bridge-root-model) を表示し、JSON ファイルを " *モデル* " フォルダーに保存します。

## <a name="download-the-code"></a>コードのダウンロード

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[IoT プラグ アンド プレイ ブリッジ](https://aka.ms/iotplugandplaybridge)の GitHub リポジトリをこの場所にクローンします。

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

IoT プラグ アンド プレイ ブリッジ リポジトリをコンピューターにクローンした後、管理コマンド プロンプトを開き、クローンされたリポジトリのディレクトリに移動します。

```cmd
cd pnpbridge
git submodule update --init --recursive
```

この操作が完了するまで数分かかることが予想されます。

>[!NOTE]
> Git クローン サブ モジュールの更新に失敗する問題が発生した場合、これは Windows ファイル パスと git に関する既知の問題です。 https://github.com/msysgit/git/pull/110 を参照してください。 問題を解決するために、次のコマンドを試すことができます: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>構成 JSON の設定

IoT プラグ アンド プレイ ブリッジ リポジトリをコンピューターにクローンした後、クローンされたリポジトリの `pnpbridge/docs/schema` ディレクトリに移動します。ここに、[構成 JSON](https://aka.ms/iot-pnp-bridge-env-config) またはブリッジの環境センサー サンプルの `config.json` が見つかります。 構成ファイルの詳細については、[IoT プラグ アンド プレイ ブリッジの概念ドキュメント](concepts-iot-pnp-bridge.md)を参照してください。

`root-_interface_model_id` フィールドには、デバイスのモデルを識別する IoT プラグ アンド プレイ モデル ID をコピーする必要があります。 この例では `dtmi:com:example:SampleDevice;1` です。 `config.json` ファイル内の **pnp_bridge_parameters** ノードにある次のパラメーターを変更します。

* connection_string 
* symmetric_key 

>[!NOTE]
> symmetric_key は、接続文字列の SAS キーと一致している必要があります。

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 入力すると、`config.json` ファイルは次のようになります。

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 ブリッジを作成したら、この `config.json` をブリッジと同じディレクトリに配置するか、実行時にパスを指定する必要があります。

## <a name="build-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジの作成

リポジトリ ディレクトリ内の *pnpbridge* フォルダーに移動します。

Windows の場合は、[Visual Studio 用開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)で以下を実行します。

```cmd
cd scripts\windows
build.cmd
```

Linux の場合も同様に以下を実行します。

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows 上で、cmake コマンドによって生成されたソリューションを Visual Studio 2019 で開くことができます。 cmake ディレクトリ内の *azure_iot_pnp_bridge.sln* プロジェクト ファイルを開き、 *pnpbridge_bin* プロジェクトをソリューションのスタートアップ プロジェクトとして設定します。 これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

## <a name="start-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジの開始

 *pnpbridge* フォルダーに移動し、コマンド プロンプトで以下を実行して、環境センサー用の IoT プラグ アンド プレイ ブリッジ サンプルを開始します。

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

* [IoT プラグ アンド プレイ ブリッジとは](./concepts-iot-pnp-bridge.md)
* [IoT プラグ アンド プレイ ブリッジの GitHub 開発者向けリファレンスを参照する](https://aka.ms/iot-pnp-bridge-dev-doc)
* [GitHub の IoT プラグ アンド プレイ ブリッジ](https://aka.ms/iotplugandplaybridge)
