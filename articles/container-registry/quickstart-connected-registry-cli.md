---
title: クイック スタート - CLI を使用して接続済みレジストリを作成する
description: Azure CLI コマンドを使用して、イメージおよび他の成果物をクラウド レジストリと同期できる接続された Azure container registry のリソースを作成します。
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 926d9ca087f509a1aa76dcec235fd7996bce4a78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092760"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-cli"></a>クイック スタート: Azure CLI を使用した接続されたレジストリの作成

このクイック スタートでは、Azure CLI を使用して Azure で[接続レジストリ](intro-connected-registry.md) リソースを作成します。 Azure Container Registry の接続されたレジストリ機能を使用すると、リモートで、またはオンプレミスにレジストリをデプロイし、イメージや他の成果物をクラウド レジストリと同期できます。 

ここでは、クラウド レジストリ用に 2 つの接続されたレジストリ リソースを作成します。1 つの接続されたレジストリでは、読み取りおよび書き込み (成果物のプルとプッシュ) 機能が許可され、1 つは読み取り専用機能を許可します。 

接続されたレジストリを作成した後は、他のガイドに従って、オンプレミスまたはリモートのインフラストラクチャにデプロイして使用できます。

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

* Azure Container registry - コンテナー レジストリがまだない場合は、接続されたレジストリをサポートする[リージョン](intro-connected-registry.md#available-regions)に [1 つ作成](container-registry-get-started-azure-cli.md)します (Premium レベルが必要)。 

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>クラウド レジストリの専用データ エンドポイントを有効にする

[az acr update][az-acr-update] コマンドを使用して、クラウド内の Azure コンテナー レジストリの専用データ エンドポイントを有効にします。 この手順は、接続されたレジストリがクラウド レジストリと通信するために必要です。

```azurecli
# Set the REGISTRY_NAME environment variable to identify the existing cloud registry
REGISTRY_NAME=<container-registry-name>

az acr update --name $REGISTRY_NAME \
  --data-endpoint-enabled
```

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>読み取りおよび書き込み機能用の接続されたレジストリ リソースを作成する

[az acr connected-registry create][az-acr-connected-registry-create] コマンドを使用して、接続されたレジストリを作成します。 接続されたレジストリ名は、文字で始まり、英数字のみを使う必要があります。 この Azure container registry の階層では、5 文字から 40 文字の長さで、一意である必要があります。

```azurecli
# Set the CONNECTED_REGISTRY_RW environment variable to provide a name for the connected registry with read/write functionality
CONNECTED_REGISTRY_RW=<connnected-registry-name>

az acr connected-registry create --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy"
```

このコマンドは、名前が *$CONNECTED_REGISTRY_RW* の値である接続済みレジストリ リソースを作成し、それを名前が *$REGISTRY_NAME* の値であるクラウド レジストリにリンクします。 後のクイック スタート ガイドでは、接続されたレジストリをデプロイするためのオプションについて説明します。 
* 指定されたリポジトリは、デプロイされると、クラウド レジストリと接続されたレジストリの間で同期されます。 
* 接続されたレジストリには `--mode` オプションが指定されていないので、既定の [ReadWrite モード](intro-connected-registry.md#modes)で作成されます。 
* この接続されたレジストリに対して同期スケジュールが定義されていないため、中断することなく、クラウド レジストリと接続されたレジストリの間でリポジトリが同期されます。

  > [!IMPORTANT]
  > 下位レイヤーがインターネットにアクセスできない入れ子になったシナリオをサポートするには、常に `acr/connected-registry` リポジトリの同期を許可する必要があります。 このリポジトリには、接続されたレジストリ ランタイムのイメージが含まれています。

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>読み取り専用機能用の接続されたレジストリ リソースを作成する

[az acr connected-registry create][az-acr-connected-registry-create] コマンドを使用して、読み取り専用機能を持つ接続されたレジストリを作成することもできます。 

```azurecli
# Set the CONNECTED_REGISTRY_READ environment variable to provide a name for the connected registry with read-only functionality
CONNECTED_REGISTRY_RO=<connnected-registry-name>
az acr connected-registry create --registry $REGISTRY_NAME \
  --parent $CONNECTED_REGISTRY_RW \
  --name $CONNECTED_REGISTRY_RO \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy" \
  --mode ReadOnly
```

このコマンドは、名前が *$CONNECTED_REGISTRY_RO* の値である接続されたレジストリ リソースを作成し、それを *$REGISTRY_NAME* の値で名前を付けたクラウド レジストリにリンクします。 
* 指定されたリポジトリは、デプロイされると、 *$CONNECTED_REGISTRY_RW* の値で名前を付けた親レジストリと、接続されたレジストリの間で同期されます。
* このリソースは、[ReadOnly モード](intro-connected-registry.md#modes)で作成されており、デプロイされると、読み取り専用 (成果物プル) 機能が有効になります。 
* この接続されたレジストリに対して、同期スケジュールが定義されていないため、中断することなく、親レジストリと接続されたレジストリの間でリポジトリが同期されます。

## <a name="verify-that-the-resources-are-created"></a>リソースが作成されたことを確認する

接続されたレジストリ [az acr connected-registry list][az-acr-connected-registry-list] コマンドを使用して、リソースが作成されていることを確認できます。 

```azurecli
az acr connected-registry list \
  --registry $REGISTRY_NAME \
  --output table
```

以下のような応答が表示されます。 接続されたレジストリはまだデプロイされていないので、"オフライン" の接続状態は、現在クラウドから切断されていることを示します。

```
NAME                 MODE        CONNECTION STATE    PARENT               LOGIN SERVER    LAST SYNC (UTC)
-------------------  --------    ------------------  -------------------  --------------  -----------------
myconnectedregrw    ReadWrite    Offline
myconnectedregro    ReadOnly     Offline             myconnectedregrw
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure CLI を使用して Azure で 2 つの接続レジストリ リソースを作成しました。 これらの新しい接続されたレジストリ リソースは、クラウド レジストリに関連付けられているので、成果物をクラウド レジストリと同期できます。

接続されたレジストリを IoT Edge にデプロイして使用する方法については、接続済みレジストリのデプロイ ガイドに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: 接続されたレジストリを IoT Edge にデプロイする][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
