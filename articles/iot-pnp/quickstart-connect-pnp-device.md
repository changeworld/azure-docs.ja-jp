---
title: IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを IoT Hub に接続する (Windows) | Microsoft Docs
description: IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを Windows 上でビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152068"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>クイック スタート:Windows 上で実行されているサンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する (C Windows)

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、デバイスからハブに送信される情報を Azure IoT Explorer ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは C で記述されており、Azure IoT Hub Device C SDK に含まれています。 ソリューション開発者は Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、ご利用のローカル コンピューター上に次のソフトウェアをインストールする必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio のインストール時に、**NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer をインストールする

ほとんどの最新の更新プログラムでは、"資産" の下にある .msi ファイルを選択して、ツールの [[リポジトリ]](https://github.com/Azure/azure-iot-explorer/releases) ページから **Azure IoT Explorer** の最新のリリースをダウンロードしてインストールします。

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイックスタートでは、Azure IoT Hub Device C SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-c)の GitHub リポジトリをこの場所にクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

この操作は、完了するまでに数分かかります。

## <a name="build-the-code"></a>コードのビルド

デバイス SDK を使用して、含まれているサンプル コードをビルドします。 ビルドしたアプリケーションでは、IoT ハブに接続するデバイスのシミュレーションが行われます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. デバイス SDK ルート フォルダーに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、デバイス SDK と生成されたコード スタブをビルドします。

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > ご利用の C++ コンパイラが cmake で見つからない場合は、前のコマンドを実行したときにビルド エラーが発生します。 それが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)で次のコマンドを実行してみてください。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

SDK でサンプル アプリケーションを実行して、IoT ハブにテレメトリを送信する IoT プラグ アンド プレイ デバイスをシミュレートします。 このサンプル アプリケーションを実行するには、以下のコマンドを使用して、"_デバイス接続文字列_" をパラメーターとして渡します。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへの利用統計情報の送信を開始しました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT Explorer を使用してコードを検証する

1. Azure IoT エクスプローラーを開きます。 **[アプリの構成]** ページが表示されます。

1. ご利用の _IoT Hub 接続文字列_を入力し、 **[接続]** を選択します。

1. 接続すると、 **[デバイス]** の概要ページが表示されます。

1. ツールがデバイスからインターフェイス モデル定義を確実に読み取れるようにするために、 **[設定]** を選択します。 [設定] メニューで、 **[On the connected device]\(接続されているデバイス上\)** がプラグ アンド プレイ構成に既に表示されている場合があります。そうでない場合は、 **[+ Add module definition source]\(+ モジュール定義ソースの追加\)** を選択し、 **[On the connected device]\(接続されているデバイス上\)** を選択 してそれを追加します。

1. **[デバイス]** の概要ページに戻り、前に作成したデバイス ID を見つけます。 デバイス アプリケーションがコマンド プロンプトでまだ実行されている状態で、Azure IoT エクスプローラー内のデバイスの **[接続の状態]** が _[接続済み]_ として報告されていることを確認します (そうでない場合は、そうなるまで **[最新の情報に更新]** を押します)。 詳細を表示するには、デバイスを選択します。

1. **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** という ID を持つインターフェイスを展開して、インターフェイスと IoT プラグ アンド プレイのプリミティブ (プロパティ、コマンド、テレメトリ) を表示します。

1. **[テレメトリ]** ページを選択し、" _[開始]_ " を押して、デバイスから送信されているテレメトリ データを表示します。

1. **[プロパティ (書き込み不可)]** ページを選択して、デバイスから報告された書き込みができないプロパティを表示します。

1. **[プロパティ (書き込み可能)]** ページを選択して、更新を行うことができる書き込み可能なプロパティを表示します。

1. プロパティの**名前**を展開し、新しい名前で更新して、 **[書き込み可能なプロパティの更新]** を選択します。 

1. 新しい名前が **[報告されるプロパティ]** 列に表示されるのを確認するには、ページの上部にある **[更新]** ボタンを選択します。

1. **[コマンド]** ページを選択して、デバイスでサポートされているすべてのコマンドを表示します。

1. **点滅**コマンドを展開し、新しい点滅時間間隔を設定します。 **[コマンドの送信]** を選択して、デバイス上でコマンドを呼び出します。

1. シミュレートするデバイス コマンド プロンプトにアクセスして、出力された確認メッセージを読み、コマンドが想定どおりに実行されたことを確認します。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: IoT プラグ アンド プレイ プレビュー デバイスに接続してやりとりする](howto-develop-solution.md)
