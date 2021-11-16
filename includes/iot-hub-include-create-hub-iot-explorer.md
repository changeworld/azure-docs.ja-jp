---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 830c636dfe3f8179a459fd8bfe7aedd51ff11902
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861115"
---
## <a name="create-an-iot-hub"></a>IoT Hub の作成
このセクションでは、Azure CLI を使用して IoT ハブとリソース グループを作成します。  Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 IoT ハブは、IoT アプリケーションとデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

IoT ハブとリソース グループを作成するには、次のようにします。

1. Azure CLI を起動します。 
    - Cloud Shell を使用する場合は、CLI コマンドの **[Try It]\(試してみる\)** ボタンを選択すると、分割されたブラウザー ウィンドウで Cloud Shell が起動します。 また、別のブラウザー タブで [Cloud Shell](https://shell.azure.com/bash) を開くこともできます。
    - Azure CLI をローカルで使用している場合は、Windows CMD、PowerShell、Bash などのコンソールを開き、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)します。
    
    このクイックスタートの以降の部分で CLI コマンドを実行するには、コマンド構文をコピーして Cloud Shell ウィンドウまたは CLI コンソールに貼り付け、変数の値を編集して Enter キーを押します。

1. [az extension add](/cli/azure/extension#az_extension_add) を実行して、*azure-iot* 拡張機能をインストールするか、最新バージョンにアップグレードします。

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成します。 次のコマンドは、*myResourceGroup* という名前のリソース グループを *eastus* という場所に作成します。 
    >[!NOTE]
    > 必要に応じて、別の場所を設定することもできます。 選択できる場所を確認するには、`az account list-locations` を実行します。 コマンド例を見るとわかるように、このチュートリアルでは *eastus* を使用しています。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) コマンドを実行して、IoT ハブを作成します。 IoT ハブの作成には数分かかることがあります。 

    *YourIotHubName*: 以下のコマンドでは、このプレースホルダーとその前後の中かっこを実際の IoT ハブの名前に置き換えます。 IoT ハブ名は Azure でグローバルに一意である必要があります。 以降、このクイックスタートに出現しているプレースホルダーにはすべて、実際の IoT ハブの名前を使用してください。

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > IoT ハブを作成したら、このクイックスタートの残りの部分で、Azure IoT エクスプローラーを使用して IoT ハブとやり取りします。 IoT エクスプローラーは、既存の IoT ハブに接続し、デバイスの追加、管理、監視を行うことができる GUI アプリケーションです。 詳細については、「[Azure IoT エクスプローラーをインストールして使用する](../articles/iot-fundamentals/howto-use-iot-explorer.md)」を参照してください。 必要に応じて、CLI コマンドを引き続き使用することができます。

### <a name="configure-iot-explorer"></a>IoT エクスプローラーの構成

このクイックスタートの残りの部分では、IoT エクスプローラーを使用して、IoT ハブへのデバイスの登録と、デバイス テレメトリの表示を行います。 このセクションでは、先ほど作成した IoT ハブに接続し、パブリック モデル リポジトリからプラグ アンド プレイ モデルを読み取るように IoT エクスプローラーを構成します。 

> [!NOTE]
> Azure CLI を使用してデバイスを登録することもできます。 *[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) --device-id mydevice --hub-name {YourIoTHubName}* コマンドを使用して新しいデバイスを登録し、 *[az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) --device-id mydevice --hub-name {YourIoTHubName}* コマンドを使用してデバイスのプライマリ接続文字列を取得します。 デバイスの接続文字列を書き留めたら、「[デバイス サンプルを実行する](#run-the-device-sample)」に進むことができます。

IoT ハブへの接続を追加するには:

1. [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) コマンドを実行して、IoT ハブの接続文字列を取得します。

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 引用符で囲まずに接続文字列をコピーします。
1. Azure IoT エクスプローラーで、左側のメニューの **[IoT Hub]** を選択し、 **[+ 接続の追加]** を選択します。
1. 接続文字列を **[接続文字列]** ボックスに貼り付けます。
1. **[保存]** を選択します。

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="IoT エクスプローラーでの接続の追加のスクリーンショット":::

1. 接続が成功すると、IoT エクスプローラーが **[デバイス]** ビューに切り替わります。

パブリック モデル リポジトリを追加するには:

1. IoT エクスプローラーで、 **[ホーム]** を選択してホーム ビューに戻ります。
1. 左側のメニューで、 **[IoT プラグ アンド プレイの設定]** を選択し、 **[+ 追加]** を選択して、ドロップダウン メニューから **[パブリック リポジトリ]** を選択します。
1. `https://devicemodels.azure.com` に、パブリック モデル リポジトリのエントリが表示されます。

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="IoT エクスプローラーでのパブリック モデル リポジトリの追加のスクリーンショット":::

1. **[保存]** を選択します。

### <a name="register-a-device"></a>デバイスの登録

このセクションでは、新しいデバイス インスタンスを作成し、作成した IoT ハブに登録します。 新しく登録されたデバイスの接続情報は、後のセクションでデバイスを安全に接続するために使用します。

デバイスを登録するには:

1. IoT エクスプローラーのホーム ビューで、 **[IoT Hub]** を選択します。
1. 前に追加した接続が表示されます。 接続プロパティの下にある **[View devices in this hub]\(このハブのデバイスを表示\)** を選択します。
1. **[+ 新規]** を選択し、デバイスのデバイス ID (例: *mydevice*) を入力します。 他のプロパティはすべてそのままにしておきます。
1. **[作成]** を選択します。

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Azure IoT エクスプローラーのデバイス ID のスクリーンショット":::

1. コピー ボタンを使用して、 **[プライマリ接続文字列]** フィールドをコピーし、メモしておきます。 この接続文字列は後で必要になります。
