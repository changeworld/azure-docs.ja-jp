---
title: Kubernetes on Azure のチュートリアル - アプリケーションのスケーリング
description: AKS チュートリアル - アプリケーションのスケーリング
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61654ae972965800909544554cc93dae511e1ff1
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480274"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング

ここまでチュートリアルに従って進めてきた場合は、AKS で Kubernetes クラスターが動作していて、Azure Vote アプリをデプロイしてあります。

このチュートリアルでは、7 つあるうちの 5 番目のパートで、アプリのポッドをスケールアウトし、ポッドの自動スケーリングを試します。 また、Azure VM ノードの数をスケーリングして、クラスターがワークロードをホストする容量を変更する方法についても説明します。 次のタスクを行います。

> [!div class="checklist"]
> * Kubernetes の Azure ノードのスケーリング
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成

後続のチュートリアルでは、Azure Vote アプリケーションが新しいバージョンに更新されます。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="manually-scale-pods"></a>ポッドを手動でスケーリングする

ここまでで、Azure Vote フロントエンドと Redis インスタンスをデプロイし、それぞれに 1 つのレプリカを作成しました。 これを確認するには、[kubectl get][kubectl-get] コマンドを実行します。

```azurecli
kubectl get pods
```

出力:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale][kubectl-scale] コマンドを使って、`azure-vote-front` のデプロイに含まれるポッドの数を手動で変更します。 この例では、数を 5 に増やします。

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods][kubectl-get] を実行して、Kubernetes がポッドを作成していることを確認します。 しばらくすると、追加したポッドが実行するようになります。

```azurecli
kubectl get pods
```

出力:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>ポッドを自動スケールする

Kubernetes は[ポッドの水平自動スケーリング][kubernetes-hpa]をサポートしており、CPU 使用率などの選ばれたメトリックに応じて、デプロイのポッドの数を調整します。 [Metrics Server][metrics-server] は、リソース使用率を Kubernetes に提供するために使用します。 Metrics Server をインストールするには、`metrics-server` GitHub リポジトリを複製し、サンプル リソース定義をインストールします。 これらの YAML 定義の内容を表示する場合は、[Metrics Server for Kuberenetes 1.8+][metrics-server-github] を参照してください。

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

自動スケーラーを使うには、ポッドで CPU の要求と制限が定義されている必要があります。 `azure-vote-front` のデプロイでは、フロントエンド コンテナーは 0.25 CPU を要求します。上限は 0.5 CPU です。 設定は次のようになります。

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

次の例では、[kubectl autoscale][kubectl-autoscale] コマンドを使って、`azure-vote-front` のデプロイのポッド数を自動スケーリングします。 ここでは、CPU 使用率が 50% を超えると、自動スケーラーはポッドを最大 10 個まで増やします。

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

自動スケーラーの状態を見るには、次のコマンドを実行します。

```azurecli
kubectl get hpa
```

出力:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote アプリの負荷が最低になって数分が経過すると、ポッド レプリカの数は自動的に 3 に減少します。

## <a name="manually-scale-aks-nodes"></a>AKS ノードの手動スケーリング

前のチュートリアルでコマンドを使って Kubernetes クラスターを作成した場合、そのクラスターには 1 つのノードがあります。 クラスターのコンテナー ワークロードを増減する場合は、ノードの数を手動で調整できます。

次の例では、*myAKSCluster* という名前の Kubernetes クラスターのノードの数を 3 に増やしています。 コマンドが完了するまでに数分かかります。

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

次のように出力されます。

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Kubernetes クラスターの異なるスケーリング機能を使いました。 次のタスクを行いました。

> [!div class="checklist"]
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成
> * Kubernetes の Azure ノードのスケーリング

次のチュートリアルに進んで、Kubernetes でのアプリケーションの更新について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションを更新する][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
