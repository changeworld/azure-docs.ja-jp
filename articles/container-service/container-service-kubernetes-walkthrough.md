---
title: "クイックスタート - Linux 用 Azure Kubernetes クラスター | Microsoft Docs"
description: "Azure CLI を使用して Azure Container Service で Linux コンテナー用 Kubernetes クラスターを作成する方法を簡単に説明します。"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Linux コンテナー用の Kubernetes クラスターをデプロイする

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、[Kubernetes](https://kubernetes.io/docs/home/) クラスターを [Azure Container Service](container-service-intro.md) にデプロイする方法を詳しく説明します。 クラスターをデプロイしたら、Kubernetes `kubectl` コマンドライン ツールを使用してクラスターに接続し、最初の Linux コンテナーをデプロイします。

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン 

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する
[az acs create](/cli/azure/acs#create) コマンドを使用して Azure Container Service に Kubernetes クラスターを作成します。 

次の例では、1 つの Linux マスター ノードと 2 つの Linux エージェント ノードを含む、*myK8sCluster* という名前のクラスターを作成します。 この例では SSH キーを作成します (既定の場所に存在しない場合)。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 クラスター名は環境に適したものに更新してください。 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

数分してコマンドが完了すると、デプロイに関する情報が表示されます。

## <a name="install-kubectl"></a>kubectl のインストール

クライアント コンピューターから Kubernetes クラスターに接続するには、Kubernetes コマンドライン クライアント ([`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)) を使用します。 

Azure CloudShell を使用している場合、`kubectl` は既にインストールされています。 ローカルにインストールする場合には、[az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) コマンドを使用します。

次の Azure CLI の例では `kubectl` がご使用のシステムにインストールされます。 Azure CLI を macOS または Linux で実行しているとき、場合によっては `sudo` を使用してコマンドを実行する必要があります。

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) コマンドを実行します。 次の例では、Kubernetes クラスターのクラスター構成がダウンロードされます。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

コンピューターからクラスターへの接続を確認するために、次のコマンドを実行してみます。

```azurecli-interactive
kubectl get nodes
```

`kubectl` によって、マスター ノードとエージェント ノードが一覧表示されます。

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>NGINX コンテナーをデプロイする

1 つ以上のコンテナーが含まれる Kubernetes の "*ポッド*" 内で Docker コンテナーを実行できます。 

次のコマンドは、いずれかのノード上の Kubernetes ポッド内で NGINX Docker コンテナーを起動します。 このケースでは、コンテナーは [Docker Hub](https://hub.docker.com/_/nginx/) のイメージからプルされた NGINX Web サーバーを実行します。

```azurecli-interactive
kubectl run nginx --image nginx
```
コンテナーが実行していることを確認するには、次のコマンドを実行します。

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>NGINX のようこそページの表示
パブリック IP アドレスで NGINX サーバーを世界に公開するには、次のコマンドを入力します。

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

このコマンドにより、サービスと、サービスのためのパブリック IP アドレスを持つ [Azure Load Balancer ルール](container-service-kubernetes-load-balancing.md)が、Kubernetes によって作成されます。 

次のコマンドを実行して、サービスの状態を確認します。

```azurecli-interactive
kubectl get svc
```

初期状態では IP アドレスが `pending` として表示されます。 数分後に、サービスの外部 IP アドレスが設定されます。
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

任意の Web ブラウザーを使用して、外部 IP アドレスで NGINX の既定のようこそページを確認します。

![Nginx の参照の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>クラスターを削除する
クラスターが必要なくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除できます。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>次のステップ

このクイック スタートでは、`kubectl` で接続される Kubernetes クラスターをデプロイし、NGINX コンテナーを含むポッドをデプロイしました。 Azure Container Service の詳細を学ぶには、Kubernetes クラスターのチュートリアルに進みます。

> [!div class="nextstepaction"]
> [ACS Kubernetes クラスターの管理](./container-service-tutorial-kubernetes-prepare-app.md)

