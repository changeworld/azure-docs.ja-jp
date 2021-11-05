---
title: クイック スタート - 接続済みレジストリを IoT Edge デバイスに展開する
description: Azure CLI コマンドと Azure portal を使用して、接続された Azure コンテナー レジストリを Azure IoT Edge デバイスにデプロイします。
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7d7ac92bda1aade5a446e6b0fe2a2b9966d5eb9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090073"
---
# <a name="quickstart-deploy-a-connected-registry-to-an-iot-edge-device"></a>クイック スタート: 接続されたレジストリを IoT Edge デバイスにデプロイする

このクイック スタートでは、Azure CLI を使用して、[接続されたレジストリ](intro-connected-registry.md)をモジュールとして Azure IoT Edge デバイスにデプロイします。 IoT Edge デバイスは、クラウド内の親 Azure コンテナー レジストリにアクセスできます。

IoT Edge での接続済みレジストリの使用の概要については、「[Azure IoT Edge で接続済みレジストリを使用する](overview-connected-registry-and-iot-edge.md)」を参照してください。 このシナリオは、IoT Edge 階層の[最上位レイヤー](overview-connected-registry-and-iot-edge.md#top-layer)のデバイスに対応しています。 


[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub と IoT Edge デバイス。 デプロイ手順については、「[クイック スタート: 初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../iot-edge/quickstart-linux.md)」を参照してください。
  > [!IMPORTANT]
  > IoT Edge デバイスにデプロイされているモジュールに後からアクセスするには、デバイス上でポート 8000、5671、および 8883 を開いていることを確認してください。 構成手順については、「[Azure portal を使用して仮想マシンへのポートを開く方法](../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。 

* Azure 内のコネクテッド レジストリ リソース。 デプロイ手順については、[Azure CLI][quickstart-connected-registry-cli] または [Azure portal][quickstart-connected-registry-portal] を使用するクイック スタートを参照してください。 

    * このシナリオでは、`ReadWrite` または `ReadOnly` モードのどちらの接続されたレジストリも使用できます。 
    * この記事のコマンドで、接続されたレジストリの名前は *CONNECTED_REGISTRY_RW* という環境変数に格納されています。

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>接続済みレジストリの構成を取得する

接続されたレジストリを IoT Edge デバイスにデプロイする前に、Azure 内の接続されたレジストリ リソースから構成設定を取得する必要があります。

接続されたレジストリをインストールするのに必要な設定情報を取得するには、[az acr connected-registry get-settings][az-acr-connected-registry-get-settings] コマンドを使用します。 次の例では、HTTPS を親プロトコルとして指定します。 親レジストリがクラウド レジストリの場合は、このプロトコルが必要です。

```azurecli
az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https
```

既定では、設定情報に[同期トークン](overview-connected-registry-access.md#sync-token)のパスワードは含まれていません。接続済みレジストリをデプロイするには、これも必要です。 必要に応じて、`--generate-password 1` または `generate-password 2` パラメーターを渡してパスワードの 1 つを生成します。 生成されたパスワードを安全な場所に保存します。 もう一度取得することはできません。

> [!WARNING]
> パスワードを再生成すると、同期トークンの資格情報がローテーションされます。 以前のパスワードを使用してデバイスを構成した場合は、構成を更新する必要があります。

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-a-deployment-manifest-for-iot-edge"></a>IoT Edge の配置マニフェストを構成する

配置マニフェストは、IoT Edge デバイスにデプロイするモジュールを記述した JSON ドキュメントです。 詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](../iot-edge/module-composition.md)」を参照してください。

Azure CLI を使用して接続されたレジストリおよび API プロキシ モジュールをデプロイするには、次の配置マニフェストを `manifest.json` ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

## <a name="deploy-the-connected-registry-and-api-proxy-modules-on-iot-edge"></a>接続されたレジストリと API プロキシ モジュールを IoT Edge にデプロイする

次のコマンドを使用し、前のセクションで作成した配置マニフェストを使用して、接続されたレジストリと API プロキシ モジュールを IoT Edge デバイスにデプロイします。 IoT Edge の最上位レイヤー デバイスの ID と IoT Hub の名前を、示された場所に指定します。

```azurecli
# Set the IOT_EDGE_TOP_LAYER_DEVICE_ID and IOT_HUB_NAME environment variables for use in the following Azure CLI command
IOT_EDGE_TOP_LAYER_DEVICE_ID=<device-id>
IOT_HUB_NAME=<hub-name>

az iot edge set-modules \
  --device-id $IOT_EDGE_TOP_LAYER_DEVICE_ID \
  --hub-name $IOT_HUB_NAME \
  --content manifest.json
```

詳細については、「[Azure CLI を使用して Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-cli.md)」を参照してください。

接続されたレジストリの状態を確認するには、次の [az acr connected-registry show][az-acr-connected-registry-show] コマンドを使用します。 接続されたレジストリの名前は、 *$CONNECTED_REGISTRY_RW* の値です。

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --output table
```

デプロイが正常に完了すると、接続済みレジストリで `Online` 状態が示されます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、接続されたレジストリを IoT Edge デバイスにデプロイする方法を学習しました。 次のガイドに進んで、新しくデプロイされた接続されたレジストリからイメージをプルする方法、または入れ子になった IoT Edge デバイス上に接続されたレジストリをデプロイする方法を学習してください。


> [!div class="nextstepaction"]
> [コネクテッド レジストリからイメージをプルする][pull-images-from-connected-registry]

> [!div class="nextstepaction"]
> [チュートリアル: 入れ子になった IoT Edge デバイスに接続済みレジストリを展開する][tutorial-connected-registry-nested]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]:/cli/azure/acr#az_acr_import
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential?#az_acr_token_credential_generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
[tutorial-connected-registry-nested]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
