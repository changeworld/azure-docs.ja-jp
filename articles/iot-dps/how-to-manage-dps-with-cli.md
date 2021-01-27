---
title: Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service を管理する
description: Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service (DPS) を管理する方法を説明します
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678963"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service を管理する方法

[Azure CLI](/cli/azure) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 Azure CLI は Windows、Linux、macOS で使用できます。 Azure CLI を使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。

IoT 拡張機能によって、Azure CLI には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

このチュートリアルでは、まず、Azure CLI と IoT 拡張機能のセットアップ手順を完了します。 次に、CLI コマンドで基本的なデバイス プロビジョニング サービス操作を実行する方法について説明します。 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>[前提条件]

- [Python 2.7x または Python 3.x](https://www.python.org/downloads/) が必要です。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0.70 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="basic-device-provisioning-service-operations"></a>基本的なデバイス プロビジョニング サービス操作

例では、Azure アカウントへのログイン、Azure リソース グループ (Azure ソリューションの関連リソースを保持するコンテナー) の作成、IoT Hub の作成、デバイス プロビジョニング サービスの作成、既存のデバイス プロビジョニング サービスの一覧表示、およびリンクされた IoT Hub の作成を CLI コマンドで行う方法を示しています。 

開始する前に、前に説明したインストール手順を完了してください。 まだ Azure アカウントがない場合は、今すぐ[無料の Azure アカウントを作成](https://azure.microsoft.com/free/?v=17.39a)することができます。 


### <a name="1-log-in-to-the-azure-account"></a>1.Azure アカウントにログインする
  
```azurecli
az login
```

![コマンド az login を実行するコマンド プロンプト ウィンドウのスクリーンショット。](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.eastus で IoTHubBlogDemo リソース グループを作成する

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![リソース グループの作成](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3.2 つのデバイス プロビジョニング サービスを作成する

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![デバイス プロビジョニング サービスの作成](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4.このリソース グループ内のすべての既存のデバイス プロビジョニング サービスを一覧表示する

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![デバイス プロビジョニング サービスの一覧表示](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5.新しく作成されたリソース グループに、IoT Hub として blogDemoHub を作成する

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![IoT Hub を作成する](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6.1 つの既存の IoT Hub をデバイス プロビジョニング サービスにリンクする

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Hub をリンクする](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

次のチュートリアルに進み、負荷分散されたハブに複数のデバイスをプロビジョニングする方法を学習してください。 

> [!div class="nextstepaction"]
> [負荷分散された IoT ハブにデバイスをプロビジョニングする](./tutorial-provision-multiple-hubs.md)