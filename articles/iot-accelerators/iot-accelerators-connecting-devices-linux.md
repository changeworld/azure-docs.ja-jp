---
title: C を使用してリモート監視するために Linux デバイスをプロビジョニングする - Azure | Microsoft Docs
description: Linux で実行されている C で記述されたアプリケーションを使用して、デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 5d7d6522dc663f13ce40cc638ba90ac4043d435c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611414"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>デバイスをリモート監視ソリューション アクセラレータに接続する (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、リモート監視ソリューション アクセラレータに物理デバイスを接続する方法について説明します。

## <a name="create-a-c-client-project-on-linux"></a>Linux 上で C クライアント プロジェクトを作成する

制限付きのデバイス上で実行される多くの組み込みアプリケーションと同様に、デバイス アプリケーションのためのクライアント コードは C で書かれています。このチュートリアルでは、Ubuntu (Linux) を実行しているコンピューターでアプリケーションを構築します。

次の手順を完了するには、Ubuntu バージョン 15.04 以降が実行されているデバイスが必要です。 次に進む前に、次のコマンドを使用して、前提条件となるパッケージを Ubuntu デバイスにインストールします。

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>デバイスにクライアント ライブラリをインストールする

Azure の IoT Hub クライアント ライブラリは、 **apt-get** コマンドを使用してパッケージとして Ubuntu デバイスにインストールし、使用できます。 IoT Hub クライアント ライブラリとヘッダー ファイルが含まれるパッケージを Ubuntu コンピューターにインストールするには、次の手順を実行します。

1. シェルで、コンピューターに Azure IoT リポジトリを追加します。

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. azure-iot-sdk-c-dev パッケージをインストールします

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Parson JSON パーサーをインストールする

IoT Hub クライアント ライブラリでは、メッセージ ペイロードの解析に Parson JSON のパーサーが使用されます。 次のコマンドを使用して、コンピューター上の適切なフォルダーに Parson GitHub リポジトリを複製します。

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>プロジェクトを準備する

Ubuntu コンピューターで、`remote_monitoring` という名前のフォルダーを作成します。 `remote_monitoring` フォルダーで次の手順を実行します。

- `main.c`、`remote_monitoring.c`、`remote_monitoring.h`、および `CMakeLists.txt` の 4 つのファイルを作成します。
- `parson` という名前のフォルダーを作成します。

`parson.c` と `parson.h` のファイルを Parson リポジトリのローカル コピーから、`remote_monitoring/parson` フォルダーにコピーします。

テキスト エディターで、`remote_monitoring.c` ファイルを開きます。 次の `#include` ステートメントを追加します。

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

## <a name="add-code-to-run-the-app"></a>アプリを実行するコードを追加する

テキスト エディターで、`remote_monitoring.h` ファイルを開きます。 次のコードを追加します。

```c
void remote_monitoring_run(void);
```

テキスト エディターで、`main.c` ファイルを開きます。 次のコードを追加します。

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

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
