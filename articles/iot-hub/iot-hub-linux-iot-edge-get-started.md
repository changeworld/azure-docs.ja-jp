---
title: "Azure IoT Edge の使用 (Linux) | Microsoft Docs"
description: "Linux コンピューターにゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Edge の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 38fd72fda6ea5d03d72c950803b09dd0b9e34fe4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Azure IoT Edge アーキテクチャについて (Linux)

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法

作業を開始する前に、Linux で SDK を使用するための[開発環境を設定する][lnk-setupdevbox]必要があります。

1. シェルを開きます。
1. **iot-edge** リポジトリのローカル コピーのルート フォルダーに移動します。
1. **tools\build.sh** スクリプトを実行します。 このスクリプトでは、**cmake** ユーティリティを使用して、**iot-edge** リポジトリのローカル コピーのルート フォルダーに **ビルド** という名前のフォルダーを作成し、メイクファイルを生成します。 スクリプトは、次にソリューションをビルドし、単体テストとエンド ツー エンド テストはスキップします。 単体テストをビルドして実行する場合は、**--run-unittests** パラメーターを追加します。 エンド ツー エンド テストをビルドして実行する場合は、**--run-e2e-tests** を追加します。

> [!NOTE]
> **build.sh** スクリプトを実行するたびに **ビルド** フォルダーが削除され、**iot-edge** リポジトリのローカル コピーのルート フォルダーに再作成されます。

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

1. **build.sh** スクリプトは、**iot-edge** リポジトリのローカル コピーの**ビルド** フォルダーに出力を生成します。 この出力には、このサンプルで使用する 2 つの IoT Edge モジュールが含まれています。

    このビルド スクリプトにより、**liblogger.so** が **build/modules/logger/** フォルダーに配置され、**libhello\_world.so** が **build/modules/hello_world/** フォルダーに配置されます。 次の JSON 設定ファイルの例に示すように、**モジュール パス**の値にはこれらのパスを使用します。
1. hello\_world\_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを受け取ります。 次のサンプルの JSON ファイルは、**samples/hello\_world/src/hello\_world\_lin.json** の SDK リポジトリに提供されています。 既定以外の場所に IoT Edge モジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、この構成ファイルはそのままで動作します。

   > [!NOTE]
   > モジュール パスは、hello\_world\_sample 実行可能ファイルが起動される現在の作業ディレクトリからの相対パスです。実行可能ファイルが配置されているディレクトリではありません。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。

    ```json
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links":
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
1. **build** フォルダーに移動します。
1. 次のコマンドを実行します。

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

