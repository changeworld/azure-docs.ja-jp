---
title: "Azure Container Instances チュートリアル - Azure Container Registry の準備"
description: "Azure Container Instances チュートリアル - Azure Container Registry の準備"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0beadcca2247fb64c03835c4cb1e1e4fb9426d6f
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry をデプロイして使用する

これは 3 つのパートで構成されるチュートリアルの 2 番目のタスクです。 [前のステップ](container-instances-tutorial-prepare-app.md)では、[Node.js](http://nodejs.org) で記述されたシンプルな Web アプリケーションに対して、コンテナー イメージが作成されました。 このチュートリアルでは、このイメージを Azure Container Registry にプッシュします。 コンテナー イメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成](container-instances-tutorial-prepare-app.md)に関するページに戻ってください。

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 このチュートリアルでは、Azure Container Registry インスタンスのデプロイ、およびこのインスタンスへのコンテナー イメージのプッシュについて説明します。 手順は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

以降のチュートリアルでは、コンテナーをプライベート レジストリから Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、Azure CLI および Docker 開発環境のローカル インストールをお勧めします。

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コレクションです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) コマンドを使用して Azure Container Registry を作成します。 コンテナー レジストリ名は、Azure 内で**一意にする必要があります**。また、5 ～ 50 文字の英数字を含める必要があります。 `<acrName>` を、レジストリの一意の名前に置き換えます。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

たとえば、*mycontainerregistry082* という名前の Azure Container Registry を作成するには、次のコマンドを実行します。

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

このチュートリアルの残りの部分では、選択したコンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="container-registry-login"></a>Container Registry のログイン

イメージをプッシュする前に、ACR のインスタンスにログインする必要があります。 [az acr login](/cli/azure/acr#az_acr_login) コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に割り当てられた一意名を指定する必要があります。

```azurecli
az acr login --name <acrName>
```

このコマンドが完了すると、`Login Succeeded` というメッセージが返されます。

## <a name="tag-container-image"></a>コンテナー イメージのタグ付け

コンテナー イメージをプライベート レジストリからデプロイするには、レジストリの `loginServer` 名でイメージにタグを付ける必要があります。

現在のイメージの一覧を表示するには、`docker images` コマンドを使用します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

loginServer 名を取得するには、次のコマンドを実行します。 `<acrName>` を、コンテナー レジストリの名前に置き換えます。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

出力例:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

コンテナー レジストリの loginServer で *aci-tutorial-app* イメージにタグを付けます。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョン番号を示します。 `<acrLoginServer>` を、先ほど実行した `az acr show` コマンドの結果に置き換えます。

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

`docker push` コマンドを使用して、*aci-tutorial-app* イメージをレジストリにプッシュします。 `<acrLoginServer>` を、以前の手順で取得した完全なログイン サーバー名に置き換えます。

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

お使いの Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository#list) コマンドを使用します。 コンテナー レジストリ名でコマンドを更新します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```azurecli
Result
----------------
aci-tutorial-app
```

次に特定のイメージのタグを表示するには、[az acr repository show-tags](/cli/azure/acr/repository#show-tags) コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

出力:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Registry が Azure Container Instances で使用できるように準備され、コンテナー イメージがプッシュされました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

次のチュートリアルでは、Azure Container Instances を使用してコンテナーを Azure にデプロイする方法について学習します。

> [!div class="nextstepaction"]
> [コンテナーを Azure Container Instances にデプロイする](./container-instances-tutorial-deploy-app.md)
