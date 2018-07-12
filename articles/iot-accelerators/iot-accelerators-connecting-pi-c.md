---
title: C を使用してリモート監視するために Raspberry Pi をプロビジョニング - Azure | Microsoft Docs
description: C で記述されたアプリケーションを使用して、Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 23e84a8d577bb1c4950de3acd76b0f8528551ae0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611443"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、リモート監視ソリューション アクセラレータに物理デバイスを接続する方法について説明します。 制限付きのデバイス上で実行される多くの組み込みアプリケーションと同様、Raspberry Pi デバイス アプリケーションのためのクライアント コードは C で書かれています。このチュートリアルでは、Raspbian OS を実行する Raspberry Pi でアプリケーションを構築します。

### <a name="required-hardware"></a>必要なハードウェア

Raspberry Pi でコマンド ラインにリモート接続するためのデスクトップ コンピューター。

[Raspberry Pi 3 用 Microsoft IoT スタート キット](https://azure.microsoft.com/develop/iot/starter-kits/)または同等のコンポーネント。 このチュートリアルでは、キット内の次のものを使用します。

- Raspberry Pi 3
- microSD カード (NOOBS をインストール済み)
- USB ミニ ケーブル
- イーサネット ケーブル

### <a name="required-desktop-software"></a>必要なデスクトップ ソフトウェア

Raspberry Pi でコマンド ラインにリモートでアクセスするための SSH クライアントがデスクトップ コンピューターに必要です。

- Windows には SSH クライアントは含まれていません。 [PuTTY](http://www.putty.org/) を使用することをお勧めします。
- ほとんどの Linux ディストリビューションと Mac OS には、コマンド ライン SSH ユーティリティが含まれています。 詳細については、「[SSH Using Linux or Mac OS (Linux または Mac OS を使用した SSH 接続)](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)」を参照してください。

### <a name="required-raspberry-pi-software"></a>必要な Raspberry Pi ソフトウェア

この記事では、ユーザーが最新バージョンの [Raspbian OS を Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/) にインストール済みであることを前提としています。

次の手順では、ソリューション アクセラレータに接続する C アプリケーションを構築するために、Raspberry Pi を準備する方法を示しています。

1. **ssh** を使用して Raspberry Pi に接続します。 詳細については、[Raspberry Pi の Web サイト](https://www.raspberrypi.org/)の [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) のセクションを参照してください。

1. 次のコマンドを使用して Raspberry Pi を更新します。

    ```sh
    sudo apt-get update
    ```

1. Raspberry Pi に、必要な開発ツールとライブラリを追加するには、次のコマンドを使用します。

    ```sh
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Raspberry Pi に IoT Hub クライアント ライブラリをダウンロード、ビルド、インストールするには、次のコマンドを使用します。

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    mkdir cmake
    cd cmake
    cmake ..
    make
    sudo make install
    ```

## <a name="create-a-project"></a>プロジェクトの作成

Raspberry Pi への **ssh** 接続を使用して、次の手順を実行します。

1. `remote_monitoring` という名前のフォルダーを Raspberry Pi のホーム フォルダーで作成します。 シェル内でこのフォルダーに移動します。

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. `remote_monitoring` フォルダーに、**main.c**、**remote_monitoring.c**、**remote_monitoring.h**、**CMakeLists.txt** の 4 ファイルを作成します。

1. テキスト エディターで、**remote_monitoring.c** ファイルを開きます。 Raspberry Pi では、**nano** または **vi** テキスト エディターを使用できます。 次の `#include` ステートメントを追加します。

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

**remote_monitoring.c** ファイルを保存し、エディターを終了します。

## <a name="add-code-to-run-the-app"></a>アプリを実行するコードを追加する

テキスト エディターで、**remote_monitoring.h** ファイルを開きます。 次のコードを追加します。

```c
void remote_monitoring_run(void);
```

**remote_monitoring.h** ファイルを保存し、エディターを終了します。

テキスト エディターで、 **main.c** ファイルを開きます。 次のコードを追加します。

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

**main.c** ファイルを保存し、エディターを終了します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

次の手順では、 *CMake* を使用してクライアント アプリケーションをビルドする方法について説明します。

1. テキスト エディターで、`remote_monitoring` フォルダーの **CMakeLists.txt** ファイルを開きます。

1. 次の手順を追加して、クライアント アプリケーションをビルドする方法を定義します。

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. **CMakeLists.txt** ファイルを保存し、エディターを終了します。

1. `remote_monitoring` フォルダーで、CMake が生成する *make* ファイルを格納するフォルダーを作成します。 続いて、**cmake** と **make** コマンドを次のように実行します。

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. クライアント アプリケーションを実行し、テレメトリを IoT Hub に送信します。

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
