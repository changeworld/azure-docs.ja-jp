---
title: Visual Studio Code からモジュールをデプロイする - Azure IoT Edge
description: Visual Studio Code を Azure IoT Tools と共に使用して、デプロイ マニフェストによる構成のとおりに、IoT Edge モジュールを IoT Hub から IoT Edge デバイスにプッシュします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209207"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code から Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

この記事では、JSON 配置マニフェストを作成し、そのファイルを使用して IoT Edge デバイスにデプロイをプッシュする方法を示します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、[Visual Studio Code を使用して IoT Edge モジュールを大規模にデプロイする](how-to-deploy-monitor-vscode.md)方法に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device.md#register-with-visual-studio-code)。
* [Visual Studio Code](https://code.visualstudio.com/)。
* Visual Studio Code 用の [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Visual Studio Code を使用してモジュールをデプロイするには、配置マニフェストを .JSON ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を動作させるには、Azure アカウントにサインインし、作業している IoT ハブを選択する必要があります。

1. Visual Studio Code で**エクスプローラー** ビューを開きます。

1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![Azure IoT Hub セクションを展開する](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。

1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。

1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。

1. Azure サブスクリプションを選択します。

1. IoT ハブを選択します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

モジュールをデバイスにデプロイするには、モジュール情報で構成されている配置マニフェストを適用します。

1. Visual Studio Code エクスプローラー ビューで **[Azure IoT Hub]** セクションを展開し、 **[デバイス]** ノードを展開します。

1. デプロイ マニフェストで構成する IoT Edge デバイスを右クリックします。

    > [!TIP]
    > 選択したデバイスが IoT Edge デバイスであることを確認するには、そのデバイスを選択してモジュールの一覧を展開し、 **[$edgeHub]** と **[$edgeAgent]** が存在することを確認します。 すべての IoT Edge デバイスには、これらの 2 つのモジュールが含まれています。

1. **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

1. 使用する配置マニフェスト JSON ファイルに移動し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

   ![Select Edge Deployment Manifest (Edge 配置マニフェストの選択)](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

デプロイの結果は、VS Code の出力に出力されます。 ターゲット デバイスが実行され、インターネットに接続されている場合、成功したデプロイは数分以内に適用されます。

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを **[Azure IoT Hub]** セクションで表示できます。 IoT Edge デバイスの横の矢印を選択して展開します。 現在実行中のすべてのモジュールが表示されます。

デバイスに新しいモジュールを最近デプロイした場合は、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーをポイントし、更新アイコンをクリックしてビューを更新します。

モジュールの名前を右クリックして、モジュール ツインを表示および編集します。

## <a name="next-steps"></a>次のステップ

[Visual Studio Code を使用して IoT Edge を大規模にデプロイおよび監視する](how-to-deploy-monitor.md)方法を確認します。
