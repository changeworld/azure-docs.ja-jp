---
title: "Azure Container Instances チュートリアル - Azure Container Registry の準備"
description: "Azure Container Instances チュートリアル 2 / 3 - Azure Container Registry の準備"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry をデプロイして使用する

これは 3 つのパートで構成されるチュートリアルの 2 番目のタスクです。 [前のステップ](container-instances-tutorial-prepare-app.md)では、[Node.js][nodejs] で記述されたシンプルな Web アプリケーションに対して、コンテナー イメージが作成されました。 このチュートリアルでは、このイメージを Azure Container Registry にプッシュします。 コンテナー イメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成](container-instances-tutorial-prepare-app.md)に関するページに戻ってください。

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 このチュートリアルでは、Azure Container Registry インスタンスのデプロイ、およびこのインスタンスへのコンテナー イメージのプッシュについて説明します。

シリーズの第 2 部であるこの記事では、次の内容を学習します。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * レジストリへのイメージのアップロード

このシリーズの最後のチュートリアルである次の記事では、コンテナーをプライベート レジストリから Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.23 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

このチュートリアルを完了するには、Docker 開発環境がローカルにインストールされている必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 このチュートリアルを完了するには、ローカル コンピューターに Azure CLI と Docker 開発環境をインストールする必要があります。

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コレクションです。

[az group create][az-group-create] コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create][az-acr-create] コマンドを使用して Azure Container Registry を作成します。 コンテナー レジストリ名は、Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 `<acrName>` を、レジストリの一意の名前に置き換えます。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

たとえば、*mycontainerregistry082* という名前の Azure Container Registry を作成するには、次のコマンドを実行します。

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

このチュートリアルの残りの部分では、選択したコンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="container-registry-login"></a>Container Registry のログイン

イメージをプッシュする前に、Azure Container Registry インスタンスにログインする必要があります。 [az acr login][az-acr-login] コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に割り当てた一意名を指定する必要があります。

```azurecli
az acr login --name <acrName>
```

このコマンドが完了すると、`Login Succeeded` というメッセージが返されます。

## <a name="tag-container-image"></a>コンテナー イメージのタグ付け

コンテナー イメージをプライベート レジストリからデプロイするには、レジストリの `loginServer` 名でイメージにタグを付ける必要があります。

現在のイメージの一覧を表示するには、[docker images][docker-images] コマンドを使用します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

loginServer 名を取得するには、[az acr show][az-acr-show] コマンドを実行します。 `<acrName>` を、コンテナー レジストリの名前に置き換えます。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

出力例:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

コンテナー レジストリの loginServer で *aci-tutorial-app* イメージにタグを付けます。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョン番号を示します。 `<acrLoginServer>` を、先ほど実行した [az acr show][az-acr-show] コマンドの結果で置き換えます。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

タグ付けが完了したら、`docker images` を実行して操作を確認します。

```bash
docker images
```

出力:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

[docker push][docker-push] コマンドを使用して、*aci-tutorial-app* イメージをレジストリにプッシュします。 `<acrLoginServer>` を、以前の手順で取得した完全なログイン サーバー名に置き換えます。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

インターネット接続に応じて、`push` 操作には数秒から数分かかります。出力は次のようになります。

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry のイメージの一覧表示

お使いの Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list][az-acr-repository-list] コマンドを使用します。 コンテナー レジストリ名でコマンドを更新します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```azurecli
Result
----------------
aci-tutorial-app
```

次に特定のイメージのタグを表示するには、[az acr repository show-tags][az-acr-repository-show-tags] コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

出力:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Container Registry を Azure Container Instances で使用できるように準備し、コンテナー イメージをレジストリにプッシュしました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

次のチュートリアルでは、Azure Container Instances を使用してコンテナーを Azure にデプロイする方法について学習します。

> [!div class="nextstepaction"]
> [コンテナーを Azure Container Instances にデプロイする](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
