---
title: "Azure Container Service チュートリアル - アプリケーションのスケーリング | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリケーションのスケーリング"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: f0e2c0bc0eeeceb86bc5108a4e4866392a79e016
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Kubernetes ポッドと Kubernetes インフラストラクチャをスケーリングする

ここまでチュートリアルに従って進めてきた場合は、Azure Container Service で Kubernetes クラスターが動作していて、Azure Vote アプリをデプロイしてあります。 

このチュートリアルでは、アプリのポッドをスケールアウトし、ポッドの自動スケーリングを試みます。 また、エージェント ノードの数をスケーリングして、クラスターがワークロードをホストする容量を変更する方法についても説明します。 次のタスクを行います。

> [!div class="checklist"]
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成
> * Kubernetes の Azure エージェント ノードのスケーリング

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

このチュートリアルでは、少なくとも実行中のアプリケーションがある Kubernetes クラスターが必要です。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="manually-scale-pods"></a>ポッドを手動でスケーリングする

前のチュートリアルでは、Azure Vote のフロントエンドとバックエンドをそれぞれ 1 つのポットにデプロイしました。 これを確認するには、[kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを実行します。

```bash
kubectl get pods
```

次のような出力になります。

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) コマンドを使って、`azure-vote-front` のデプロイに含まれるポッドの数を手動で変更します。 この例では、数を 5 に増やします。

```bash
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) を実行して、Kubernetes がポッドを作成していることを確認します。 しばらくすると、追加したポッドが実行するようになります。

```bash
kubectl get pods
```

出力:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>ポッドを自動スケールする

Kubernetes は[ポッドの水平自動スケーリング](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)をサポートしており、CPU の使用率または他のメトリックに応じて、デプロイのポッドの数を調整します。 

自動スケーラーを使うには、ポッドで CPU の要求と制限が定義されている必要があります。 `azure-vote-front` のデプロイでは、各コンテナーは 0.25 CPU を要求し、制限は 0.5 CPU です。 設定は次のようになります。

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

次の例では、[kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) コマンドを使って、`azure-vote-front` のデプロイメントのポッド数を自動スケーリングします。 ここでは、CPU 使用率が 50% を超えると、自動スケーラーはポッドを最大 10 個まで増やします。


```bash
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

自動スケーラーの状態を見るには、次のコマンドを実行します。

```bash
kubectl get hpa
```

出力:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote アプリの負荷が最低になって数分経過すると、ポッド レプリカの数は 3 に自動的に減少します。

## <a name="scale-the-agents"></a>エージェントをスケーリングする

前のチュートリアルで既定のコマンドを使って Kubernetes クラスターを作成した場合、クラスターには 3 つのエージェント ノードがあります。 クラスターのコンテナー ワークロードを増減する場合は、エージェントの数を手動で調整できます。 [az acs scale](/cli/azure/acs#scale) コマンドを使い、`--new-agent-count` パラメーターでエージェントの数を指定します。

次の例では、*myK8sCluster* という名前の Kubernetes クラスターのエージェント ノードの数を 4 に増やしています。 コマンドが完了するまでに数分かかります。

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

コマンドの出力では、`agentPoolProfiles:count` の値でエージェント ノードの数が示されます。

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Kubernetes クラスターの異なるスケーリング機能を使いました。 次のタスクを行いました。

> [!div class="checklist"]
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成
> * Kubernetes の Azure エージェント ノードのスケーリング

次のチュートリアルに進んで、Kubernetes でのアプリケーションの更新について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションを更新する](./container-service-tutorial-kubernetes-app-update.md)


