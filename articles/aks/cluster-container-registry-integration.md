---
title: Azure Container Registry と Azure Kubernetes Service を統合する
description: Azure Kubernetes Service (AKS) と Azure Container Registry (ACR) を統合する方法について説明します。
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 3c11367945b74db9be20ade86c7bc26901440e4d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305159"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>プレビュー - Azure Kubernetes Service から Azure Container Registry の認証を受ける

Azure Kubernetes Service (AKS) で Azure Container Registry (ACR) を使用する場合は、認証メカニズムを確立する必要があります。 この記事では、この 2 つの Azure サービス間で認証を行う場合に推奨される構成について詳しく説明します。

Azure CLI を使用して、いくつかの単純なコマンドで AKS から ACR への統合を設定できます。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー](support-policies.md)
> * [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

次のものが必要です。

* **Azure サブスクリプション**上の**所有者**または **Azure アカウント管理者**ロール。
* Azure CLI バージョン 2.0.70 以降および aks-preview 0.4.8 拡張機能も必要です。
* クライアントに [Docker がインストールされている](https://docs.docker.com/install/)必要があり、かつ [Docker Hub](https://hub.docker.com/) へのアクセスが必要です。

## <a name="install-latest-aks-cli-preview-extension"></a>最新の AKS CLI プレビュー拡張機能をインストールする

**aks-preview 0.4.13** 拡張機能以降が必要です。

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 統合を使用して新しい AKS クラスターを作成する

AKS クラスターの初期作成中に AKS と ACR の統合を設定できます。  AKS クラスターが ACR と対話できるようにするために、Azure Active Directory の**サービス プリンシパル**が使用されます。 次の CLI コマンドを使用すると、サブスクリプション内の既存の ACR を承認し、サービス プリンシパル用の適切な **ACRPull** ロールを構成することができます。 下のパラメーターの有効な値を指定してください。  かっこ内のパラメーターは省略可能です。
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
** ACR リソース ID の形式は次のとおりです。 

/subscriptions/<subscription-d>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/{name} 
  
この手順は、完了するまでに数分かかることがあります。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>既存の AKS クラスターに対する ACR 統合を構成する

下の **acr-name** または **acr-resource-id** に有効な値を指定することによって、既存の ACR と既存の AKS クラスターを統合します。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

次を使用して、ACR と AKS クラスター間の統合を削除することもできます。
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>ACR にログインする

ACR にログインするには、次のコマンドを使用します。  <acrname> パラメーターを ACR 名に置き換えます。  たとえば、既定値は **aks<your-resource-group>acr** です。

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Docker Hub からイメージをプルして ACR にプッシュする

Docker Hub からイメージをプルし、そのイメージにタグを付けて ACR にプッシュします。

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>状態を更新してポッドを確認する

次の手順を実行してデプロイを確認します。

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

yaml ファイルを表示し、ACR ログイン サーバー、イメージ、およびタグの値を置き換えることによってイメージ プロパティを編集します。

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
