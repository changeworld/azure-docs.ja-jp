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
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Azure IoT Edge アーキテクチャについて (Windows)

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>サンプルの実行

**build.cmd** スクリプトは、**iot-edge** リポジトリのローカル コピーの **build** フォルダーに出力を生成します。 この出力には多数のファイルが含まれていますが、このサンプルでは以下の 3 つに焦点を当てています。
- 2 つの IoT Edge モジュール: **build\\modules\\logger\\Debug\\logger.dll** および **build\\modules\\hello_world\\Debug\\hello\_world.dll**。 
- 実行可能ファイル: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**。 このプロセスは、コマンド ラインの引数として JSON 構成ファイルを使用します。

このサンプルで使用する 4 番目のファイルは、build フォルダーにはありませんが、iot-edge リポジトリの複製時に組み込まれています。
- JSON 構成ファイル: **samples\\hello\_world\\src\\hello\_world\_win.json**。 このファイルには 2 つのモジュールへのパスが含まれています。 また、logger.dll がその出力を書き込む場所を宣言します。 既定値は現行作業ディレクトリ内の **log.txt** です。 

   >[!NOTE]
   >サンプル モジュールを移動する場合、またはテストのために独自のモジュールを追加する場合は、それに合わせて構成ファイル内の **module.path** 値を更新してください。 モジュール パスは、**hello\_world\_sample.exe** があるディレクトリに対して相対的です。 

サンプルを実行するには、次の手順に従います。

1. **iot-edge** レポジトリのローカル コピーのルートにある **build** フォルダーに移動します。

1. 次のコマンドを実行します。

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. 次の出力は、サンプルが正常に実行されていることを表しています。

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. **Enter** キーを押してプロセスを停止します。


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
