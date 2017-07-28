---
title: "Azure Container Service チュートリアル - ACR の準備 | Microsoft Docs"
description: "Azure Container Service チュートリアル - ACR の準備"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry をデプロイして使用する

Azure Container Registry (ACR) は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 このチュートリアルでは、Azure Container Registry インスタンスのデプロイ、およびこのインスタンスへのコンテナー イメージのプッシュについて説明します。 実行する手順は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * ACR のコンテナー イメージのタグ付け
> * ACR へのイメージのアップロード

以降のチュートリアルでは、この ACR インスタンスは、コンテナー イメージを安全に実行するために、Azure Container Service Kubernetes クラスターと統合されます。 

## <a name="before-you-begin"></a>開始する前に

[前のチュートリアル](./container-service-tutorial-kubernetes-prepare-app.md)で、単純な Azure Voting アプリケーション用にコンテナー イメージが作成されました。 このチュートリアルでは、これらのイメージは、Azure Container Registry にプッシュされます。 Azure Voting アプリのイメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成](./container-service-tutorial-kubernetes-prepare-app.md)に関するページに戻ってください。 または、ここで説明する手順では、任意のコンテナー イメージを使用できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) コマンドで Azure Container Registry を作成します。 コンテナー レジストリの名前は**一意でなければなりません**。 次の例を使用して、いくつかのランダムな文字で名前を更新します。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>ACR 情報の取得 

ACR のインスタンスが作成されたら、名前、ログイン サーバー名、および認証パスワードが必要です。 次のコードでは、これら値それぞれが返されます。 それぞれの値をメモします。各値はこのチュートリアルを通じて参照されます。  

ACR 名とログイン サーバー:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

ACR パスワード - ACR 名で更新します。

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Container Registry のログイン

イメージをプッシュする前に、ACR のインスタンスにログインする必要があります。 [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して、操作を完了します。 docker login を実行する場合は、ACR のログイン サーバー名と ACR の資格情報を入力する必要があります。

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

このコマンドは、完了すると 'Login Succeeded’(ログインに成功しました) というメッセージを返します。

## <a name="tag-container-images"></a>コンテナー イメージのタグ付け

各コンテナー イメージは、レジストリの名前 `loginServer` でタグ付けする必要があります。 このタグは、イメージ レジストリにコンテナー イメージをプッシュするときに、ルーティングするために使用されます。

現在のイメージの一覧を表示するには、`docker images` コマンドを使用します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

*azure-vote-front* イメージにコンテナー レジストリの loginServer をタグ付けします。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョン番号を示します。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

*azure-vote-back* イメージに対してコマンドを再度実行します。

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

タグ付けが完了したら、`docker images` を実行して操作を確認します。

```bash
docker images
```

出力:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>イメージを ACR にプッシュ

*azure-vote-front* イメージをレジストリにプッシュします。 

次の例を使用して、ACR loginServer 名を環境の loginServer に置き換えます。 完了するまでに数分かかります。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

同じことを *azure-vote-back* イメージに対して行います。

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>ACR のイメージの一覧表示 

Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository#list) コマンドを使用します。 ACR のインスタンス名でコマンドを更新します。

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

出力:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

次に特定のイメージのタグを表示するには、[az acr repository show-tags](/cli/azure/acr/repository#show-tags) コマンドを使用します。

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

出力:

```azurecli
Result
--------
v1
```

チュートリアル完了時には、2 つのコンテナー イメージがプライベートの Azure Container Registry インスタンスに格納されています。 これらのイメージは、以降のチュートリアルで、ACR から Kubernetes クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ACS Kubernetes クラスターで使用するための Azure Container Registry が準備されました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * ACR のコンテナー イメージのタグ付け
> * ACR へのイメージのアップロード

Azure での Kubernetes クラスターのデプロイについては、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Kubernetes クラスターのデプロイ](./container-service-tutorial-kubernetes-deploy-cluster.md)
