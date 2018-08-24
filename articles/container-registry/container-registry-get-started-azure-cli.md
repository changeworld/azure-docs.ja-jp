---
title: クイック スタート - Azure CLI を使用した Azure のプライベート Docker レジストリの作成
description: Azure CLI を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: b17df81dd1d972f3e559140e9f43963e9c8cab2a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "42024249"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>クイック スタート: Azure CLI を使用したコンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、Azure CLI を使って Azure Container Registry インスタンスを作成し、そのレジストリにコンテナー イメージをプッシュして、最後に Azure Container Instances (ACI) に対してレジストリからコンテナーをデプロイする方法について詳しく説明します。

このクイック スタートでは、Azure CLI バージョン 2.0.27 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create][az-group-create] コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このクイックスタートでは、*Basic* のレジストリを作成します。 次の表で簡単に説明されているように、Azure Container Registry はいくつかの SKU で使用できます。 それぞれの詳細については、「[Azure Container Registry SKU][container-registry-skus]」を参照してください。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

[az acr create][az-acr-create] コマンドを使用して ACR インスタンスを作成します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、*myContainerRegistry007* を使用します。 これを一意の値に更新します。

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

## <a name="deploy-image-to-aci"></a>ACI へのイメージのデプロイ

作成したレジストリからコンテナー インスタンスをデプロイするためには、デプロイ時にレジストリの資格情報を指定する必要があります。 運用環境のシナリオでは、コンテナー レジストリにアクセスするために[サービス プリンシパル][container-registry-auth-aci]を使用する必要がありますが、このクイック スタートでは、要点をわかりやすく伝えるために、レジストリ上の管理者ユーザーを有効にします。次のコマンドを使用してください。

```azurecli
az acr update --name <acrName> --admin-enabled true
```

管理者を有効にした後は、レジストリ名と同じユーザー名になります。次のコマンドでパスワードを取得できます。

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

1 CPU コアおよび 1 GB メモリでコンテナー イメージをデプロイするには、次のコマンドを実行します。 `<acrName>`、`<acrLoginServer>`、`<acrPassword>` は、前のコマンドから取得した値に置き換えてください。

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Azure Resource Manager から最初の応答が、対象コンテナーについての詳しい情報と共に返されます。 コンテナーのステータスを監視して、実行されるタイミングを調べるために、[az container show][az-container-show] を繰り返します。 通常は 1 分かかりません。

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>アプリケーションを表示する

ACI へのデプロイが正常に完了したら、[az container show][az-container-show] コマンドを使ってコンテナーの FQDN を取得します。

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

出力例: `"aci-demo.eastus.azurecontainer.io"`

実行中のアプリケーションを表示するには、お使いのブラウザーでパブリック IP アドレスにアクセスします。

![ブラウザーでの Hello World アプリ][aci-app-browser]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、ACR インスタンス、およびすべてのコンテナー イメージを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure CLI を使って Azure Container Registry を作成し、レジストリにコンテナー イメージをプッシュして、そのインスタンスを Azure Container Instances で起動しました。 Azure Container Instances のチュートリアルに進んで、ACI についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md