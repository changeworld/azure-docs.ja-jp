---
title: IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを IoT Hub に接続する | Microsoft Docs
description: Java を使用して、IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964825"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>クイック スタート:サンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、デバイスからハブに送信される情報を Azure IoT Explorer ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは Java で記述されており、Java 用 Azure IoT サンプル コレクションの一部として提供されています。 ソリューション開発者は Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、お使いの開発用マシン上に Java SE 8 が必要です。 Maven 3 もインストールする必要があります。

これらの設定方法の詳細については、Java 用 Microsoft Azure IoT device SDK の「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md)」を参照してください。

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer をインストールする

ほとんどの最新の更新プログラムでは、"資産" の下にある .msi ファイルを選択して、ツールの [[リポジトリ]](https://github.com/Azure/azure-iot-explorer/releases) ページから **Azure IoT Explorer** の最新のリリースをダウンロードしてインストールします。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご使用のハブに対する _IoT ハブ接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイックスタートでは、Java 用 Azure IoT サンプルをクローンしてビルドするために使用できる開発環境を準備します。

任意のディレクトリでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Java 用 Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-java)の GitHub リポジトリをこの場所にクローンします。

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

この操作が完了するまでに数分かかる場合があります。

## <a name="build-the-code"></a>コードのビルド

クローンしたサンプル コードを使用して、IoT ハブに接続するデバイスをシミュレートするアプリケーションをビルドします。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. ローカル ターミナル ウィンドウで、クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** フォルダーに移動します。 次に、次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションをビルドします。

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. _デバイス接続文字列_を構成します。

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

サンプル アプリケーションを実行して、IoT ハブにテレメトリを送信する IoT プラグ アンド プレイ デバイスをシミュレートします。 サンプル アプリケーションを実行するには、次のコマンドを使用します。

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

デバイスが接続されたこと、さまざまな設定手順が実行されていること、およびサービスの更新を待っていることを示すメッセージが表示され、その後にテレメトリ ログが表示されます。 これは、デバイスがコマンドとプロパティの更新情報を受信する準備が整い、ハブへの利用統計情報の送信を開始したことを示します。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT Explorer を使用してコードを検証する

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. ツールがデバイスからインターフェイス モデル定義を確実に読み取れるようにするために、 **[設定]** を選択します。 [設定] メニューで、 **[On the connected device]\(接続されているデバイス上\)** がプラグ アンド プレイ構成に既に表示されている場合があります。そうでない場合は、 **[+ Add module definition source]\(+ モジュール定義ソースの追加\)** を選択し、 **[On the connected device]\(接続されているデバイス上\)** を選択 してそれを追加します。

1. **[デバイス]** の概要ページに戻り、前に作成したデバイス ID を見つけます。 デバイス アプリケーションがコマンド プロンプトでまだ実行されている状態で、Azure IoT エクスプローラー内のデバイスの **[接続の状態]** が _[接続済み]_ として報告されていることを確認します (そうでない場合は、そうなるまで **[最新の情報に更新]** を押します)。 詳細を表示するには、デバイスを選択します。

1. **urn:java_sdk_sample:EnvironmentalSensor:1** という ID を持つインターフェイスを展開して、インターフェイスと IoT プラグ アンド プレイのプリミティブ (プロパティ、コマンド、テレメトリ) を表示します。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: IoT プラグ アンド プレイ プレビュー デバイスに接続してやりとりする](howto-develop-solution.md)
