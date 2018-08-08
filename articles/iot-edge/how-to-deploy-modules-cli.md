---
title: Azure IoT Edge モジュールのデプロイ (CLI) | Microsoft Docs
description: Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29c11139a2c773db2d26bf44984ad4dc72f2d870
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324607"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Azure CLI 2.0 を使用して Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 これを使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI 2.0 には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

この記事では、JSON 配置マニフェストを作成し、そのファイルを使用して IoT Edge デバイスにデプロイをプッシュする方法を示します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor-cli.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。 
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-cli.md)。
* ご使用の環境内の [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –-version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 
* [Azure CLI 2.0 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Azure CLI 2.0 を使用してモジュールをデプロイするには、配置マニフェストを .json ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。 

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
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
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
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

モジュールをデバイスにデプロイするには、モジュール情報で構成されている配置マニフェストを適用します。 

デプロイ マニフェストが保存されているフォルダーにディレクトリを変更します。 VS Code IoT Edge テンプレートのいずれかを使用している場合は、ソリューション ディレクトリの**config** フォルダー内の `deployment.json` ファイルを使用します。 `deployment.template.json` ファイルは使用しないでください。 

次のコマンドを使用して、IoT Edge デバイスに構成を適用します。

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

device id パラメーターでは大文字と小文字が区別されます。 content パラメーターは、保存した配置マニフェスト ファイルを指します。 

   ![モジュールを設定する](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを次のコマンドで表示できます。 

IoT Edge デバイス上のモジュールを参照します。
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

device id パラメーターでは大文字と小文字が区別されます。

   ![モジュールの一覧を表示する](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>次の手順

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)の方法を学習します