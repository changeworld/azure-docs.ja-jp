---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 324e1d3c9d86f8c17581665507ebe52b946bd006
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114407914"
---
## <a name="create-an-iot-hub"></a>IoT Hub の作成
このセクションでは、Azure CLI を使用して IoT ハブとリソース グループを作成します。  Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 IoT ハブは、IoT アプリケーションとデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

IoT ハブとリソース グループを作成するには、次のようにします。

1. CLI アプリを起動します。  このクイックスタートの以降の部分で CLI コマンドを実行するには、コマンド構文をコピーして CLI アプリに貼り付け、変数の値を編集して Enter キーを押します。
    - Cloud Shell を使用する場合は、CLI コマンドの **[Try It]\(試してみる\)** ボタンを選択すると、分割されたブラウザー ウィンドウで Cloud Shell が起動します。 また、別のブラウザー タブで [Cloud Shell](https://shell.azure.com/bash) を開くこともできます。
    - Azure CLI をローカルで使用している場合は、CLI コンソール アプリを起動し、Azure CLI にサインインします。

1. [az extension add](/cli/azure/extension#az_extension_add) を実行して、*azure-iot* 拡張機能をインストールするか、最新バージョンにアップグレードします。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. CLI アプリで [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成します。 次のコマンドは、*myResourceGroup* という名前のリソース グループを *eastus* という場所に作成します。 
    >[!NOTE]
    > 必要に応じて、別の場所を設定することもできます。 選択できる場所を確認するには、`az account list-locations` を実行します。 コマンド例を見るとわかるように、このチュートリアルでは *eastus* を使用しています。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) コマンドを実行して、IoT ハブを作成します。 IoT ハブの作成には数分かかることがあります。 

    *YourIotHubName*: 以下のコマンドでは、このプレースホルダーとその前後の中かっこを実際の IoT ハブの名前に置き換えます。 IoT ハブ名は Azure でグローバルに一意である必要があります。 以降、このクイックスタートに出現しているプレースホルダーにはすべて、実際の IoT ハブの名前を使用してください。

    ```azurecli-interactive
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > IoT ハブを作成したら、このクイックスタートの以降の部分では、引き続き CLI コマンドを使用してハブとやり取りすることができます。 必要に応じて、CLI コマンドの代わりに Azure IoT エクスプローラーを使用することもできます。 IoT エクスプローラーは、既存の IoT ハブに接続し、デバイスの追加、管理、監視を行うことができる GUI アプリケーションです。 詳細については、「[Azure IoT エクスプローラーをインストールして使用する](../articles/iot-fundamentals/howto-use-iot-explorer.md)」を参照してください。

## <a name="create-a-simulated-device"></a>シミュレート対象デバイスを作成します
このセクションでは、IoT ハブに接続された、シミュレートされた IoT デバイスを作成します。 

シミュレートされたデバイスを作成するには:
1. CLI シェルで、[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) コマンドを実行します。 これにより、シミュレートされたデバイスを作成します。 

    *YourIotHubName*: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。 

    *myDevice*。 この記事の残りの部分では、シミュレートされたデバイスの ID に対してこの名前を直接使用できます。 別の名前を使用することもできます。 

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) コマンドを実行します。 

    ```azurecli-interactive
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    接続文字列出力は次の形式です。

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. 接続文字列を安全な場所に保存します。 

> [!NOTE]
> CLI アプリは開いたままにしておいてください。 これは後の手順で使用します。