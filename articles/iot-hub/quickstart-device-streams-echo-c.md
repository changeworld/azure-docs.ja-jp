---
title: Azure IoT Hub デバイス ストリーム C クイック スタート (プレビュー) | Microsoft Docs
description: このクイック スタートでは、デバイス ストリームを介して IoT デバイスと通信する C サービス側アプリケーションを実行します。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830735"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して C でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 パブリック プレビュー中、C SDK ではデバイス側のデバイス ストリームのみがサポートされます。 そのため、このクイック スタートでは、デバイス側アプリケーションを実行する手順についてのみ説明しています。 対応するサービス側アプリケーションも実行する必要があり、それについては [C# クイック スタート](./quickstart-device-streams-echo-csharp.md)または [Node.js クイック スタート](./quickstart-device-streams-echo-nodejs.md)のガイドに記載されています。

このクイック スタートのデバイス側 C アプリケーションには、以下の機能があります。

* IoT デバイスへのデバイス ストリームを確立します。

* サービス側から送信されたデータを受信し、それをエコーバックします。

コードは、デバイス ストリームの開始プロセスと、それを使用してデータを送受信する方法を示しています。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にした [Visual Studio 2017](https://www.visualstudio.com/vs/) をインストールします。
* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイック スタートでは、[C 対応の Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を GitHub から複製してビルドするために使用される開発環境を準備します。 GitHub 上の SDK には、このクイック スタートで使用されるサンプル コードが含まれます。 


1. [CMake ビルド システム](https://cmake.org/download/) バージョン 3.11.4 をダウンロードします。 ダウンロードしたバイナリを、対応する暗号化ハッシュ値を使用して検証します。 次の例では、Windows PowerShell を使用して、x64 MSI 配布のバージョン 3.11.4 の暗号化ハッシュを検証しています。

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    この記事の執筆時点では、CMake サイトにバージョン 3.11.4 用に次のハッシュ値が一覧表示されていました。

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

2. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    このリポジトリのサイズは現在約 220 MB です。

3. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 Windows では、シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

Windows では、Visual Studio 2015 または 2017 プロンプト用の開発者コマンド プロンプトで、次のコマンドを実行します。

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行して IoT Hub CLI 拡張機能を追加し、デバイス ID を作成します。 

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyDevice**: これは、登録済みデバイスに付けられた名前です。 示されているように、MyDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    次の例のような、デバイスの接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。


## <a name="communicate-between-device-and-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

デバイス側アプリケーションを実行するには、次の手順を実行する必要があります。
- [デバイス ストリームに関するこの記事](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c)の手順に従って、開発環境を設定してください。

- ソース ファイル `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` を編集してデバイスの資格情報を指定し、デバイスの接続文字列を指定します。
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- 次のようにコードをコンパイルします。

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- コンパイルされたプログラムを実行します。

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

前述のように、IoT Hub C SDK ではデバイス側のデバイス ストリームのみがサポートされます。 サービス側アプリケーションについては、[C# クイック スタート](./quickstart-device-streams-echo-csharp.md)または [Node.js クイック スタート](./quickstart-device-streams-echo-nodejs.md)のガイドに記載されている対応するサービス プログラムを使用してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブの設定、デバイスの登録、C アプリケーションによるハブへのシミュレートされた利用統計情報の送信、および Azure Cloud Shell によるハブからの利用統計情報の読み取りを行いました。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)