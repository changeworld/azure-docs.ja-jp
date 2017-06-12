---
title: "Azure IoT Edge の使用 (Windows) | Microsoft Docs"
description: "Windows コンピューターに Azure IoT Edge ゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Edge の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 117b949d4f8b84fe5ce43fc1dc38e3326bcfcfba
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Azure IoT Edge アーキテクチャについて (Windows)

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法

作業を開始する前に、Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]する必要があります。

1. **開発者コマンド プロンプト for VS 2015** または **開発者コマンド プロンプト for VS 2017** コマンド プロンプトを開きます。
1. **iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。
1. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドします。 Visual Studio ソリューションは、**iot-edge** レポジトリのローカル コピーの **build** フォルダーにあります。 スクリプトに追加のパラメーターを指定して、ユニット テストとエンド ツー エンド テストをビルドして実行できます。 これらのパラメーターは、それぞれ、**--run-unittests** と **--run-e2e-tests** です。

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

1. **build.cmd** スクリプトにより、リポジトリのローカル コピーに**ビルド**という名前のフォルダーが作成されます。 このフォルダーには、このサンプルで使用する 2 つの IoT Edgeモジュールが含まれています。

    このスクリプトにより、**logger.dll** が **build\\modules\\logger\\Debug** フォルダーに配置され、**hello\_world.dll** が **build\\modules\\hello_world\\Debug** フォルダーに配置されます。 次の JSON 設定ファイルに示されているように、**モジュール パス**の値には、これらのパスを使用します。
1. hello\_world\_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取ります。 次のサンプルの JSON ファイルは、**samples\\hello\_world\\src\\hello\_world\_win.json** の SDK リポジトリで提供されています。 既定以外の場所にモジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、この構成ファイルはそのままで動作します。

   > [!NOTE]
   > モジュール パスは、hello\_world\_sample.exe が配置されているディレクトリに対して相対的です。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. **iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。

1. 次のコマンドを実行します。

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

