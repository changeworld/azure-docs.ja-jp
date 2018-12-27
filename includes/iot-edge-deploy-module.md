---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108024"
---
Azure IoT Edge の主要な機能の 1 つは、クラウドから IoT Edge デバイスにモジュールをデプロイできることです。 IoT Edge モジュールは、コンテナーとして実装されている実行可能ファイルのパッケージです。 このセクションでは、[Azure Marketplace の IoT Edge モジュールのセクション](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)から事前に構成したモジュールをデプロイします。 このモジュールは、シミュレートされたデバイスのテレメトリを生成します。

1. Azure portal で、`Simulated Temperature Sensor` を検索に入力して、Marketplace の結果を開きます。

   ![Azure portal で検索したシミュレートされた温度センサー](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. **[サブスクリプション]** フィールドで、まだ選択されていない場合は、使用している IoT ハブのサブスクリプションを選択します。

3. **[IoT Hub]** フィールドで、まだ選択されていない場合は、使用している IoT ハブの名前を選択します。

4. **[デバイスの検索]** をクリックして、お使いの IoT Edge デバイス (`myEdgeDevice` という名前) を選択してから、**[作成]** を選択します。

5. ウィザードの **[モジュールの追加]** ステップで、**SimulatedTemperatureSensor** モジュールをクリックして構成の設定を確認し、**[保存]** をクリックして **[次へ]** を選択します。

6. ウィザードの **[ルートの指定]** ステップで、ルートがすべてのモジュールから IoT ハブ (`$upstream`) へすべてのメッセージを送信する既定のルートに適切に設定されていることを確認します。 そうしていない場合は、次のコードを追加し、**[次へ]** を選択します。

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. ウィザードの **[デプロイの確認]** ステップで、**[送信]** を選びます。

8. デバイスの詳細ページに戻り、**[更新]** を選びます。 最初にサービスを開始したときに作成された edgeAgent モジュールに加えて、**edgeHub** と呼ばれる別のランタイム モジュールと **SimulatedTemperatureSensor** モジュールが一覧表示されます。

   新しいモジュールが表示されるまでに、数分かかる場合があります。 IoT Edge デバイスは、その新しいデプロイ情報をクラウドから取得し、コンテナーを起動した後、その新しい状態を IoT Hub にレポートする必要があります。 

   ![デプロイ済みのモジュールの一覧に SimulatedTemperatureSensor を表示する](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
