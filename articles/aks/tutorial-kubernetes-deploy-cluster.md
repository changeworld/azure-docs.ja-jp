---
title: "Kubernetes on Azure のチュートリアル - クラスターのデプロイ"
description: "AKS チュートリアル - クラスターのデプロイ"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a03bbfbdedd418216c26379e9d8dbd780fa89fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Azure Container Service (AKS) クラスターのデプロイ

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 AKS を使うと、運用開始準備の整った Kubernetes クラスターのプロビジョニングを簡単かつ迅速に行うことができます。 このチュートリアル (8 部構成の第 3 部) では、Kubernetes クラスターが AKS にアップグレードされます。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

この後のチュートリアルでは、Azure Vote アプリケーションをクラスターにデプロイしてから拡張および更新し、Operations Management Suite を Kubernetes クラスターを監視するように構成します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、コンテナー イメージを作成して、Azure Container Registry インスタンスにアップロードしました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./tutorial-kubernetes-prepare-app.md)」に戻ってください。

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Azure サブスクリプションでの AKS プレビューの有効化
AKS がプレビューである間、新しいクラスターを作成するには、サブスクリプションに機能フラグが必要です。 使用する任意の数のサブスクリプションに対して、この機能を要求することができます。 AKS プロバイダーを登録するには、次のように `az provider register` コマンドを使用します。

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

登録すると、AKS で Kubernetes クラスターを作成できるようになります。

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する

次の例では、`myK8sCluster` という名前のクラスターを `myResourceGroup` という名前のリソース グループに作成します。 このリソース グループは、[前のチュートリアル](./tutorial-kubernetes-prepare-acr.md)で作成しました。

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

数分してデプロイが完了すると、この AKS デプロイに関する情報が JSON 形式で表示されます。

## <a name="install-the-kubectl-cli"></a>kubectl CLI をインストールする

クライアント コンピューターから Kubernetes クラスターに接続するには、[kubectl](https://kubernetes.io/docs/user-guide/kubectl/) (Kubernetes コマンドライン クライアント) を使います。

Azure CloudShell を使用している場合、kubectl は既にインストールされています。 ローカルにインストールする場合は、次のコマンドを実行します。

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように kubectl を構成するには、次のコマンドを実行します。

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

クラスターへの接続を確認するには、[kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを実行します。

```azurecli
kubectl get nodes
```

出力:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

チュートリアルが完了し、AKS クラスターはワークロードで使用できるようになりました。 後のチュートリアルでは、マルチコンテナー アプリケーションのクラスターへのデプロイ、スケールアウト、更新、監視を行います。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Kubernetes クラスターが AKS にデプロイされました。 次の手順を完了しました。

> [!div class="checklist"]
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

次のチュートリアルに進んで、クラスターでのアプリケーションの実行について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションをデプロイする](./tutorial-kubernetes-deploy-application.md)
