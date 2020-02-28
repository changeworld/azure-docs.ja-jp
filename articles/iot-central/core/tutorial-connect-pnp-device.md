---
title: チュートリアル - IoT プラグ アンド プレイ (プレビュー) デバイスを Azure IoT Central に接続する
description: このチュートリアルでは、デバイス機能モデルを使用してデバイス コードを作成する方法について説明します。 次に、デバイス コードを実行して、デバイスがご利用の IoT Central アプリケーションに接続されることを確認し、自動生成されたビューを使用します。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ca2ac62892d1c8d438cc37bffcbfede14058bc23
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026375"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>チュートリアル:デバイス機能モデルを使用して IoT プラグ アンド プレイ (プレビュー) デバイスを作成し、IoT Central アプリケーションに接続する

"_デバイス機能モデル_" (DCM) には、[IoT プラグ アンド プレイ (プレビュー)](../../iot-pnp/overview-iot-plug-and-play.md) デバイスの機能が記述されています。 IoT Central では、デバイスが初めて接続されるときに、DCM の使用によりデバイスのテンプレートとデバイス用の視覚化を作成することができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Visual Studio Code を使用して、DCM を使用する IoT プラグ アンド プレイ (プレビュー) デバイスを作成する。
> * デバイス コードを Windows で実行して、ご利用の IoT Central アプリケーションに接続することを確認する。
> * デバイスが送信するシミュレートされたテレメトリを表示する。

## <a name="prerequisites"></a>前提条件

**[カスタム アプリ] > [カスタム アプリケーション]** テンプレートを使用して IoT Central アプリケーションを作成するには、「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」クイックスタートを完了します。

このチュートリアルを完了するには、ご利用のローカル コンピューター上に次のソフトウェアをインストールする必要があります。

* **C++ ビルド ツール**と **NuGet パッケージ マネージャー コンポーネント**のワークロードを利用する [Visual Studio 用のビルド ツール](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)。 または、同じワークロードを利用する [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019、2017、2015 が既にインストールされている。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/): **CMake** をインストールするときに、 **[Add CMake to the system PATH]\(CMake をシステム パスに追加する\)** オプションを選択します。
* [Visual Studio Code](https://code.visualstudio.com/)。
* [Node.js](https://nodejs.org/)
* `dps-keygen` ユーティリティ:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT Tools のインストール

VS Code で Azure IoT Tools 拡張機能パックをインストールするには、次の手順に従います。

1. VS Code で、 **[拡張機能]** タブを選択します。
1. **Azure IoT Tools** を検索します。
1. **[インストール]** を選択します。

## <a name="prepare-the-development-environment"></a>開発環境の準備

このチュートリアルでは、[Vcpkg](https://github.com/microsoft/vcpkg) ライブラリ マネージャーを使用して、開発環境に Azure IoT C デバイス SDK をインストールします。

1. コマンド プロンプトを開きます。 次のコマンドを実行して Vcpkg をインストールします。

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    次に、ユーザー全体の[統合](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)をフックするために、次のコマンドを実行します。 このコマンドを初めて実行するときは、管理者権限が必要です。

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Azure IoT C device SDK の Vcpkg をインストールします。

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>デバイス キーの生成

デバイスを IoT Central アプリケーションに接続するためには、デバイス キーが必要です。 デバイス キーを生成するには:

1. 「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」クイックスタートの **[カスタム アプリ] > [プレビュー アプリケーション]** テンプレートを使用して作成した IoT Central アプリケーションにサインインします。

1. **[管理]** ページに移動し、 **[デバイス接続]** を選択します。

1. **[キーの表示]** を選択したときに表示される **ID スコープ**と**プライマリ キー**をメモしておきます。 これらの値を、このチュートリアルで後ほど使用します。

    ![デバイスの接続](./media/tutorial-connect-pnp-device/device-connection.png)

1. コマンド プロンプトを開き、次のコマンドを実行してデバイス キーを生成します。

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    生成された _デバイス キー_ をメモしておきます。この値は、このチュートリアルの後の手順で使用します。

## <a name="download-your-model"></a>ご利用のモデルのダウンロード

このチュートリアルでは、MxChip IoT DevKit デバイス用のパブリック DCM を使用します。 コードを実行するために実際の DevKit デバイスは必要ありません。このチュートリアルでは、Windows で実行するコードをコンパイルします。

1. `central_app` という名前のフォルダーを作成し、VS Code で開きます。

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**IoT プラグ アンド プレイ**」と入力して、 **[Open Model Repository]\(モデル リポジトリを開く\)** を選択します。 **[Public repository]\(パブリック リポジトリ\)** を選択します。 VS Code に、パブリック モデル リポジトリ内の DCM の一覧が表示されます。

1. ID が `urn:mxchip:mxchip_iot_devkit:1`の **MXChip IoT DevKit** DCM を選択します。 次に、 **[ダウンロード]** を選択します。 これで、`central_app` フォルダーにその DCM のコピーが作成されました。

![モデル リポジトリと DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> IoT Central で作業するには、デバイス機能モデルで、すべてのインターフェイスが同じファイル内にインラインで定義されている必要があります。

## <a name="generate-the-c-code-stub"></a>C コード スタブを生成する

**MXChip IoT DevKit** DCM とそれに関連するインターフェイスの用意ができたので、モデルを実装するデバイス コードを生成できます。 VS Code で C コード スタブを生成するには:

1. DCM ファイルが開いているフォルダーで、**Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**IoT プラグ アンド プレイ**」と入力して、 **[Generate Device Code Stub]\(デバイス コード スタブを生成する\)** を選択します。

    > [!NOTE]
    > IoT プラグ アンド プレイ コード ジェネレーター ユーティリティを初めて使用するときは、ダウンロードに数秒かかります。

1. ダウンロードした **MXChip IoT DevKit** DCM ファイルを選択します。

1. プロジェクト名「**devkit_device**」を入力します。

1. 使用する言語として、 **[ANSI C]** を選択します。

1. 接続方法として、 **[Via DPS (Device Provisioning Service) symmetric key]\(DPS (デバイス プロビジョニング サービス) の対称キーを使用する\)** を選択します。

1. プロジェクト タイプとして **[CMake Project on Windows]\(Windows 上での CMake プロジェクト\)** を選択します。 **[MXChip IoT DevKit Project]\(MXChip IoT DevKit プロジェクト\)** を選択しないでください。このオプションは、実際の DevKit デバイスがある場合に使用するものです。

1. SDK を含める方法として、 **[Via Vcpkg]\(Vcpkg 経由\)** を選択します。

1. VS Code では新しいウィンドウが開き、`devkit_device` フォルダーに生成されたデバイス コード スタブ ファイルが表示されます。

![生成されたデバイス コード](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>コードのビルド

デバイス SDK を使用して、生成されたデバイス コード スタブをビルドします。 ビルドしたアプリケーションにより、**MXChip IoT DevKit** デバイスがシミュレートされ、ご利用の IoT Central アプリケーションに接続されます。 アプリケーションでは、テレメトリとプロパティが送信され、コマンドが受け取られます。

1. コマンド プロンプトで、`devkit_device` フォルダーに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、生成されたコード スタブをビルドします。 `<directory of your Vcpkg repo>` プレースホルダーを、**Vcpkg** リポジトリのコピーへのパスに置き換えます。

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Visual Studio 2017 または2015 を使用している場合、使用しているビルド ツールに基づいて CMake ジェネレーターを指定する必要があります。

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. ビルドが正常に完了したら、同じコマンド プロンプトでご利用のアプリケーションを実行します。 `<scopeid>` と `<primarykey>` は、前に書き留めた値に置き換えてください。

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. そのデバイス アプリケーションによって IoT Hub へのデータの送信が開始されます。 前のコマンドを初めて実行したときに `Error registering device for DPS` エラーが表示されることがあります。 このエラーが表示された場合は、コマンドを再試行してください。

## <a name="view-the-device"></a>デバイスの表示

デバイス コードが IoT Central に接続された後、送信されたプロパティとテレメトリを表示できます。

1. ご利用の IoT Central アプリケーションで、 **[デバイス]** ページに移動し、 **[mxchip-01]** デバイスを選択します。 このデバイスは、デバイス コードが接続されたときに自動的に追加されたものです。

    ![[概要] ページ](./media/tutorial-connect-pnp-device/overview-page.png)

    数分後に、デバイスから送信されているテレメトリのグラフがこのページに表示されます。

1. **[バージョン情報]** ページを選択して、デバイスから送信されたプロパティ値を確認します。

1. **[コマンド]** ページを選択して、デバイス上でコマンドを呼び出します。 デバイス コードを実行しているコマンド プロンプトで、デバイスが応答していることを確認できます。

1. **[デバイス テンプレート]** ページに移動して、IoT Central によりパブリック リポジトリ内の DCM から作成されたテンプレートを確認します。

    ![[デバイス テンプレート] ページ](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、パブリック モデル リポジトリ内の DCM から生成された IoT プラグ アンド プレイ (プレビュー) デバイスを接続する方法を学習しました。

DCM と独自のモデルの作成方法の詳細については、攻略ガイドに進んでください。

> [!div class="nextstepaction"]
> [新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)
