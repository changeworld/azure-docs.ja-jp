---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 06e2be14cf7592edf373b6f679ac5d9ff3c87269
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161458"
---
Azure IoT Edge の主要な機能の 1 つは、クラウドから IoT Edge デバイスにモジュールをデプロイできることです。 IoT Edge モジュールは、コンテナーとして実装されている実行可能ファイルのパッケージです。 このセクションでは、シミュレートされたデバイスのテレメトリを生成するモジュールをデプロイします。 

1. Azure Portal で、お使いの IoT ハブに移動します。
1. **[IoT Edge]** に移動し、IoT Edge デバイスを選びます。
1. **[Set Modules] \(モジュールの設定)** を選択します。
1. このページの **[Deployment Modules]\(展開モジュール\)** セクションで、**[追加]** をクリックし、**[IoT Edge モジュール]** を選択します。
1. **[名前]** フィールドに「`tempSensor`」と入力します。 
1. **[イメージの URI]** フィールドに「`mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`」と入力します。 
1. 他の設定はそのままにして、**[保存]** を選びます。

   ![名前とイメージの URI を入力した後に IoT Edge モジュールを保存します](./media/iot-edge-deploy-module/name-image.png)

1. **[Add modules]\(モジュールの追加\)** ステップに戻り、**[次へ]** を選びます。
1. **[ルートの指定]** ステップで、すべてのモジュールから IoT Hub にすべてのメッセージを送信する既定のルートを用意しておく必要があります。 そうしていない場合は、次のコードを追加し、**[次へ]** を選択します。

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. **[Review Deployment]\(展開のレビュー\)** ステップで、**[送信]** を選びます。
1. デバイスの詳細ページに戻り、**[更新]** を選びます。 最初にサービスを開始したときに作成された edgeAgent モジュールに加えて、**edgeHub** と呼ばれる別のランタイム モジュールと **tempSensor** モジュールが一覧表示されます。 

   ![デプロイ済みモジュールのリストに表示された tempSensor](./media/iot-edge-deploy-module/deployed-modules.png)
