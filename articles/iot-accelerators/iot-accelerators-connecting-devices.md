---
title: C を使用してリモート監視するために Windows デバイスをプロビジョニングする - Azure | Microsoft Docs
description: Windows で実行されている C で記述されたアプリケーションを使用して、デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309872"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>デバイスをリモート監視ソリューション アクセラレータに接続する (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、リモート監視ソリューション アクセラレータに物理デバイスを接続する方法について説明します。

## <a name="create-a-c-client-solution-on-windows"></a>Windows で C のクライアント ソリューションを作成する

制限付きのデバイス上で実行される多くの組み込みアプリケーションと同様、デバイス アプリケーションのためのクライアント コードは C で書かれています。このチュートリアルでは、Windows を実行しているコンピューターでアプリケーションを構築します。

### <a name="create-the-starter-project"></a>スタート プロジェクトを作成する

Visual Studio 2017 でスタート プロジェクトを作成し、IoT Hub デバイス クライアントの NuGet パッケージを追加します。

1. Visual Studio で、Visual C++ **Windows コンソール アプリケーション** テンプレートを使用して、C コンソール アプリケーションを作成します。 プロジェクトの名前として「 **RMDevice**」と入力します。

    ![Visual C++ Windows コンソール アプリケーションを作成します](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. **ソリューション エクスプローラー**で、`stdafx.h`、`targetver.h` および `stdafx.cpp` ファイルを削除します。

1. **ソリューション エクスプローラー**で、ファイル `RMDevice.cpp` の名前を `RMDevice.c` に変更します。

    ![名前が変更された RMDevice.c ファイルが表示されたソリューション エクスプローラー](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[参照]** を選択し、次の NuGet パッケージを検索してインストールします。

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![インストールされている Microsoft.Azure.IoTHub パッケージが表示された NuGet パッケージ マネージャー](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックして **[プロパティ]** を選択し、プロジェクトの **[プロパティ ページ]** ダイアログ ボックスを開きます。 詳細については、[Visual C++ プロジェクトのプロパティの設定](https://docs.microsoft.com/cpp/ide/working-with-project-properties)に関するページを参照してください。

1. **[C/C++]** フォルダーを選択し、**[プリコンパイル済みヘッダー]** プロパティ ページを選択します。

1. **[プリコンパイル済みヘッダー]** を **[プリコンパイル済みヘッダーを使用しない]** に設定します。 **[適用]** を選択します。

    ![プリコンパイル済みヘッダーを使用していないプロジェクトが表示されたプロジェクト プロパティ](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. **[リンカー]** フォルダーをクリックして、**[入力]** プロパティ ページを選択します。

1. `crypt32.lib` を、**[追加の依存ファイル]** プロパティに追加します。 **[OK]** を選択し、もう一度 **[OK]** をクリックして、プロジェクトのプロパティ値を保存します。

    ![crypt32.lib を含むリンカーが表示されたプロジェクト プロパティ](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Parson JSON ライブラリを追加します。

Parson JSON ライブラリを **RMDevice** プロジェクトに追加し、必要な `#include` ステートメントを追加します。

1. 次のコマンドを使用して、コンピューター上の適切なフォルダーに Parson GitHub リポジトリを複製します。

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Parson リポジトリのローカル コピーから `parson.h` と `parson.c` のファイルを **RMDevice** プロジェクト フォルダーにコピーします。

1. Visual Studio で **RMDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。

1. **[既存項目の追加]** ダイアログで、**RMDevice** プロジェクト フォルダー内にある `parson.h` と `parson.c` の各ファイルを選択します。 **[追加]** を選択して、この 2 つのファイルをプロジェクトに追加します。

    ![parson.h および parson.c ファイルが表示されたソリューション エクスプローラー](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. Visual Studio で、`RMDevice.c` ファイルを開きます。 既存の `#include` ステートメントを次のコードで置き換えます。

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

    > [!NOTE]
    > これで、ソリューションをビルドすることによって、プロジェクトに正しい依存関係が設定されたことを確認できます。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

**remote\_monitoring\_run** 関数を呼び出すコードを追加し、デバイス アプリケーションをビルドして実行します。

1. **main\_ 関数を、\_remote**monitoring**run** 関数を呼び出す次のコードで置き換えます。

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **[ビルド]**、**[ソリューションのビルド]** の順に選択して、デバイス アプリケーションをビルドします。

1. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択して、サンプルを実行します。 コンソールには、次のメッセージが表示されます。

    * アプリケーションは、ソリューション アクセラレータにサンプル テレメトリを送信します。
    * ソリューションのダッシュ ボードで必要なプロパティ値セットを受信します。
    * ソリューションのダッシュ ボードから呼び出されたメソッドに応答します。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
