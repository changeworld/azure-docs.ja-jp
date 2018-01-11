---
title: "クイック スタート - Azure CLI を使用した Azure のプライベート Docker レジストリの作成"
description: "Azure CLI を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。"
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Azure CLI を使用したコンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、Azure Container Registry インスタンスを Azure CLI で作成する方法について詳しく説明します。

このクイック スタートでは、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli]」を参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create][az-group-create] コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このクイックスタートでは、*基本*のレジストリを作成します。 次の表で簡単に説明されているように、Azure Container Registry はいくつかの SKU で使用できます。 それぞれの詳細については、[Container Registry SKU][container-registry-skus] に関するページをご覧ください。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

[az acr create][az-acr-create] コマンドを使用して ACR インスタンスを作成します。

レジストリの名前は**一意である必要があります**。 次の例では、*myContainerRegistry007* を使用します。 これを一意の値に更新します。

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

レジストリが作成されると、出力は次のようになります。

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

このクイック スタートの残りの部分では、コンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="log-in-to-acr"></a>ACR にログインする

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 そのためには、[az acr login][az-acr-login] コマンドを使用します。

```azurecli
az acr login --name <acrName>
```

このコマンドが完了すると、`Login Succeeded` というメッセージが返されます。

## <a name="push-image-to-acr"></a>ACR にイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 ローカル コンテナー イメージがまだない場合は、次のコマンドを実行して、既存のイメージを Docker Hub からプルします。

```bash
docker pull microsoft/aci-helloworld
```

イメージをレジストリにプッシュするには、ACR ログイン サーバーの完全修飾名を使用して、そのイメージにタグを付けておく必要があります。 次のコマンドを実行して、ACR インスタンスの完全なログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] コマンドを使用してイメージにタグを付けます。 `<acrLoginServer>` を ACR インスタンスのログイン サーバー名で置き換えます。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最後に、[docker push][docker-push] を使用して、ACR インスタンスにイメージをプッシュします。 `<acrLoginServer>` を ACR インスタンスのログイン サーバー名で置き換えます。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

次の例は、レジストリ内のリポジトリを一覧表示します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```bash
Result
----------------
aci-helloworld
```

次の例は、**aci-helloworld** リポジトリのタグを一覧表示します。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

出力:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、ACR インスタンス、およびすべてのコンテナー イメージを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure CLI を使用して Azure Container Registry を作成しました。 Azure Container Instances と一緒に Azure Container Registry を使用する場合は、Azure Container Instances のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md