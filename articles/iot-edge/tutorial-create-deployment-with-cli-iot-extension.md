---
title: "Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする | Microsoft Docs"
description: "Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Azure CLI 2.0 の IoT 拡張機能を使用して、IoT Edge デバイスにモジュールをデプロイする

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 Azure CLI 2.0 は、Windows、Linux、および MacOS で使用できます。

Azure CLI 2.0 を使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI 2.0 には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

このチュートリアルでは、まず、Azure CLI 2.0 と IoT 拡張機能のセットアップ手順を完了します。 次に、使用可能な CLI コマンドで IoT Edge デバイスにモジュールをデプロイする方法を説明します。

## <a name="installation"></a>インストール 

### <a name="step-1---install-python"></a>手順 1 - Python をインストールする

[Python 2.7x または Python 3.x](https://www.python.org/downloads/) が必要です。

### <a name="step-2---install-azure-cli-20"></a>手順 2 - Azure CLI 2.0 をインストールする

[インストール手順](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)に従って、環境に Azure CLI 2.0 をセットアップします。 Azure CLI 2.0 のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 簡単に Windows にインストールする方法の 1 つは、[MSI](https://aka.ms/InstallAzureCliWindows) をダウンロードしてインストールすることです。

### <a name="step-3---install-iot-extension"></a>手順 3 - IoT 拡張機能をインストールする

[IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension) で、拡張機能をインストールするいくつかの方法が説明されています。 最も簡単な方法は、`az extension add --name azure-cli-iot-ext` を実行することです。 インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認することができます。また、`az extension show --name azure-cli-iot-ext` を使用して、IoT 拡張機能の詳細を表示することもできます。 拡張機能を削除するには、`az extension remove --name azure-cli-iot-ext` を使用します。


## <a name="deploy-modules-to-an-iot-edge-device"></a>IoT Edge デバイスにモジュールをデプロイする
このチュートリアルでは、IoT Edge デプロイを作成する方法を説明します。 例では、Azure アカウントへのログイン、Azure リソース グループ (ソリューションの関連リソースを保持するコンテナー) の作成、IoT Hub の作成、3 つの IoT Edge デバイス ID の作成、タグの設定、およびこれらのデバイスをターゲットにした IoT Edge デプロイの作成の方法を示しています。 開始する前に、前に説明したインストール手順を完了してください。 まだ Azure アカウントがない場合は、今すぐ[無料の Azure アカウントを作成](https://azure.microsoft.com/free/?v=17.39a)することができます。 


### <a name="1-login-to-the-azure-account"></a>1.Azure アカウントにログインする
  
    az login

![login][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.eastus で IoTHubBlogDemo リソース グループを作成する

    az group create -l eastus -n IoTHubBlogDemo

![Create resource group][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>手順 3.新しく作成されたリソース グループに、IoT Hub として blogDemoHub を作成する

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub を作成する][3]


### <a name="4-create-an-iot-edge-device"></a>4.IoT Edge デバイスを作成する

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![IoT Edge デバイスを作成する][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5.IoT Edge デバイスに構成を適用する

デプロイ JSON テンプレートを txt ファイルとしてローカルに保存します。 apply-configuration コマンドを実行するときに、ファイルへのパスが必要になります。

以下に、1 つの tempSensor モジュールを含むデプロイ JSON テンプレートのサンプルを示します。

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
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
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
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
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

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![構成を適用する][5]

### <a name="6-list-modules"></a>6.モジュールの一覧を表示する
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![モジュールの一覧を表示する][6]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスで生成された生データをフィルター処理するコードを含む、Azure 関数を作成しました。 Azure IoT Edge を探索し続けるには、Azure IoT Edge デバイスをゲートウェイとして使用する方法を確認します。 

> [!div class="nextstepaction"]
> [IoT Edge ゲートウェイ デバイスを作成する](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

