---
title: "Kubernetes on Azure のチュートリアル - ACR の準備 | Microsoft Docs"
description: "AKS チュートリアル - ACR の準備"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1848e15a2be8d89315657a6eabdb94617bd1b5bf
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry をデプロイして使用する

Azure Container Registry (ACR) は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 この 8 部構成の 2 番目のチュートリアルでは、Azure Container Registry インスタンスをデプロイし、そこにコンテナー イメージをプッシュする方法について説明します。 手順は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry (ACR) インスタンスのデプロイ
> * ACR のコンテナー イメージのタグ付け
> * ACR へのイメージのアップロード

この後のチュートリアルでは、この ACR インスタンスが AKS の Kubernetes クラスターと統合されます。

## <a name="before-you-begin"></a>開始する前に

[前のチュートリアル](./tutorial-kubernetes-prepare-app.md)では、単純な Azure Voting アプリケーション用のコンテナー イメージを作成しました。 Azure Voting アプリのイメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成](./tutorial-kubernetes-prepare-app.md)に関するページに戻ってください。

このチュートリアルでは、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 この例では、`myResourceGroup` という名前のリソース グループが `eastus` リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) コマンドで Azure Container Registry を作成します。 コンテナー レジストリの名前は**一意でなければなりません**。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

このチュートリアルの残りの部分では、コンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="container-registry-login"></a>Container Registry のログイン

[az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) コマンドで ACR インスタンスにログインします。 コンテナー レジストリの作成時に割り当てられた一意の名前が必要です。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると 'Login Succeeded’(ログインに成功しました) というメッセージを返します。

## <a name="tag-container-images"></a>コンテナー イメージのタグ付け

現在のイメージの一覧を表示するには、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用します。

```console
docker images
```

出力:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

各コンテナー イメージは、レジストリの名前 loginServer でタグ付けする必要があります。 このタグは、イメージ レジストリにコンテナー イメージをプッシュするときに、ルーティングするために使用されます。

loginServer 名を取得するには、次のコマンドを実行します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

ここで、コンテナー レジストリの loginServer で `azure-vote-front` イメージにタグを付けます。 また、イメージ名の末尾に `:redis-v1` を付加します。 このタグは、イメージのバージョンを示します。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

タグ付けしたら、[docker images] (https://docs.docker.com/engine/reference/commandline/images/) を実行して操作を確認します。

```console
docker images
```

出力:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

レジストリに `azure-vote-front` イメージをプッシュします。

次の例を使用して、ACR loginServer 名を環境の loginServer に置き換えます。

```console
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

完了するまでに数分かかります。

## <a name="list-images-in-registry"></a>レジストリ内のイメージの一覧表示

Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository#list) コマンドを使用します。 ACR のインスタンス名でコマンドを更新します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```azurecli
Result
----------------
azure-vote-front
```

次に特定のイメージのタグを表示するには、[az acr repository show-tags](/cli/azure/acr/repository#show-tags) コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

出力:

```azurecli
Result
--------
redis-v1
```

チュートリアル完了時には、コンテナー イメージがプライベートの Azure Container Registry インスタンスに格納されています。 このイメージは、以降のチュートリアルで、ACR から Kubernetes クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Registry を AKS クラスターで使用するための準備をしました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * ACR のコンテナー イメージのタグ付け
> * ACR へのイメージのアップロード

Azure での Kubernetes クラスターのデプロイについては、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Kubernetes クラスターのデプロイ](./tutorial-kubernetes-deploy-cluster.md)
