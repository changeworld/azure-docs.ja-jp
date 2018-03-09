---
title: "Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする | Microsoft Docs"
description: "Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: 1986c9881c09ffa480103e009dc42d18aad4e2aa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 Azure CLI 2.0 は、Windows、Linux、および MacOS で使用できます。

Azure CLI 2.0 を使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI 2.0 には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

このチュートリアルでは、まず、Azure CLI 2.0 と IoT 拡張機能のセットアップ手順を完了します。 次に、使用可能な CLI コマンドで IoT Edge デバイスにモジュールをデプロイする方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure アカウント。 アカウントがまだない場合は、今すぐ[無料アカウントを作成する](https://azure.microsoft.com/free/?v=17.39a)ことができます。 

* [Python 2.7x または Python 3.x](https://www.python.org/downloads/)。

* ご使用の環境内の [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –-version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 Windows にインストールする簡単な方法の 1 つは、[MSI](https://aka.ms/InstallAzureCliWindows) をダウンロードしてインストールすることです。

* [Azure CLI 2.0 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension):
   1. `az extension add --name azure-cli-iot-ext` を実行します。 
   2. インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認するか、`az extension show --name azure-cli-iot-ext` を使用して、IoT 拡張機能の詳細を表示します。
   3. 拡張機能を削除するには、`az extension remove --name azure-cli-iot-ext` を使用します。


## <a name="create-an-iot-edge-device"></a>IoT Edge デバイスを作成する
この記事では、IoT Edge デプロイの作成方法について説明します。 例では、Azure アカウントへのサインイン、Azure リソース グループ (ソリューションの関連リソースを保持するコンテナー) の作成、IoT Hub の作成、3 つの IoT Edge デバイス ID の作成、タグの設定、およびこれらのデバイスをターゲットにした IoT Edge デプロイの作成の方法を示しています。 

Azure アカウントにログインします。 次のログイン コマンドを入力すると、Web ブラウザーでワンタイム コードを使用してサインインするように求められます。 

   ```cli
   az login
   ```

**IoTHubCLI** という新しいリソース グループを米国東部地域に作成します。 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Create resource group][2]

新しく作成されたリソース グループに、**CLIDemoHub** という IoT ハブを作成します。

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >各サブスクリプションには、無料の IoT ハブが 1 つ割り当てられます。 CLI コマンドを使用して無料のハブを作成するには SKU 値を `--sku F1` に置き換えます。 サブスクリプションに無料のハブが既にある場合は、2 つ目を作成しようとするとエラー メッセージが表示されます。 

IoT Edge デバイスを作成します。

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![IoT Edge デバイスを作成する][4]

## <a name="configure-the-iot-edge-device"></a>IoT Edge デバイスを構成する

デプロイ JSON テンプレートを作成し、txt ファイルとしてローカルに保存します。 apply-configuration コマンドを実行するときに、ファイルへのパスが必要になります。

デプロイ JSON テンプレートには、常に 2 つのシステム モジュールを含める必要があります。edgeAgent と edgeHub です。 これら 2 つに加え、このファイルを使用して IoT Edge デバイスに追加のモジュールをデプロイすることもできます。 次のサンプルを使用して、 IoT Edge デバイスに 1 つの tempSensor モジュールを構成します。

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
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

IoT Edge デバイスに構成を適用します。

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

IoT Edge デバイス上のモジュールを参照します。
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![モジュールの一覧を表示する][6]

## <a name="next-steps"></a>次の手順

* [IoT Edge デバイスをゲートウェイとして使用する](how-to-create-transparent-gateway.md)方法

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

