---
title: Kubernetes on Azure のチュートリアル - クラスターのデプロイ
description: AKS チュートリアル - クラスターのデプロイ
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341401"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>チュートリアル: Azure Kubernetes Service (AKS) クラスターのデプロイ

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 AKS を使うと、運用開始準備の整った Kubernetes クラスターのプロビジョニングを迅速に行うことができます。 このチュートリアル (7 部構成の第 3 部) では、Kubernetes クラスターを AKS にデプロイします。 手順は次のとおりです。

> [!div class="checklist"]
> * リソースとの対話に使用するサービス プリンシパルの作成
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

この後のチュートリアルでは、Azure Vote アプリケーションをクラスターにデプロイ、スケーリング、および更新します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、コンテナー イメージを作成して、Azure Container Registry インスタンスにアップロードしました。 これらの手順を完了しておらず、手順を実行する場合は、「[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

AKS クラスターが他の Azure リソースと対話できるようにするために、Azure Active Directory のサービス プリンシパルを使用します。 このサービス プリンシパルは、Azure CLI または Azure portal で自動的に作成するか、または事前に手動で作成しておいて、アクセス許可を追加で割り当てることができます。 このチュートリアルでは、サービス プリンシパルを手動で作成し、前のチュートリアルで作成した Azure Container Registry (ACR) インスタンスへのアクセス権を与えた後、AKS クラスターを作成することにします。

サービス プリンシパルを作成するには、[az ad sp create-for-rbac][] を使用します。 `--skip-assignment` パラメーターで、余分なアクセス許可の割り当てを制限します。

```azurecli
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

*appId* と *password* を書き留めておいてください。 これらの値は、以降の手順で使用します。

## <a name="configure-acr-authentication"></a>ACR 認証を構成する

ACR に格納されているイメージにアクセスするためには、ACR からイメージをプルするための適切な権限を AKS のサービス プリンシパルに与える必要があります。

まず、[az acr show][] を使用して、ACR のリソース ID を取得します。 `<acrName>` レジストリ名は実際の ACR インスタンスの名前に、またリソース グループは、その ACR インスタンスが存在するリソース グループの名前に置き換えてください。

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

ACR に格納されているイメージを使用するための適切なアクセス権を AKS クラスターに与えるには、[az role assignment create][] を使用してロールの割り当てを作成します。 `<appId`> と `<acrId>` は、前の 2 つの手順で書き留めた値に置き換えてください。

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する

次に、[az aks create][] を使用して AKS クラスターを作成します。 次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前のクラスターを作成します。 このリソース グループは、[前のチュートリアル][aks-tutorial-prepare-acr]で作成しました。 先ほどサービス プリンシパルを作成する手順で書き留めた実際の `<appId>` と `<password>` を指定してください。

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

数分してデプロイが完了すると、この AKS デプロイに関する情報が JSON 形式で表示されます。

## <a name="install-the-kubectl-cli"></a>kubectl CLI をインストールする

クライアント コンピューターから Kubernetes クラスターに接続するには、[kubectl][kubectl] (Kubernetes コマンドライン クライアント) を使用します。

Azure Cloud Shell を使用している場合、kubectl は既にインストールされています。 [az aks install-cli][] を使用してローカルにインストールすることもできます。

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように kubectl を構成するには、[az aks get-credentials][] を使用します。 次の例では、*myResourceGroup* の *myAKSCluster* という名前の AKS クラスターの資格情報を取得します。

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

クラスターへの接続を確認するには、[kubectl get nodes][kubectl-get] コマンドを実行します。

```azurecli
kubectl get nodes
```

出力:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Kubernetes クラスターが AKS にデプロイされました。 次の手順を完了しました。

> [!div class="checklist"]
> * リソースとの対話に使用するサービス プリンシパルの作成
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

次のチュートリアルに進んで、クラスターでのアプリケーションの実行について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションをデプロイする][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
