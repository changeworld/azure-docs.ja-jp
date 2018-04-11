---
title: Azure Container Service (AKS) の内部ロード バランサーを作成する
description: Azure Container Service (AKS) で内部ロード バランサーを使用します。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7b9ecdb5364f7c0f5bb68ce693e53bc2c5327337
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="use-an-internal-load-balancer-with-azure-container-service-aks"></a>Azure Container Service (AKS) で内部ロード バランサーを使用します

内部負荷分散により、Kubernetes サービスが Kubernetes クラスターと同じ仮想ネットワークで実行されているアプリケーションにアクセスできるようになります。 このドキュメントでは、Azure Container Service (AKS) で内部ロード バランサーを作成する方法について説明します。

## <a name="create-internal-load-balancer"></a>内部ロード バランサーを作成する

内部ロード バランサーを作成するには、次のサンプルに示すように、サービス タイプ `LoadBalancer` および `azure-load-balancer-internal` の注釈でサービス マニフェストを構築します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

デプロイすると、Azure Load Balancer が AKS クラスターと同じ仮想ネットワーク上で作成されて使用できるようになります。 

![AKS 内部ロード バランサーの画像](media/internal-lb/internal-lb.png)

サービスの詳細を取得する際は、`EXTERNAL-IP` 列にある IP アドレスが内部ロード バランサーの IP アドレスになります。 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>IP アドレスを指定する

内部ロード バランサーに特定の IP アドレスを使用する場合は、ロード バランサーの仕様に `loadBalancerIP` プロパティを追加します。指定した IP アドレスは AKS クラスターと同じサブネットに存在し、まだリソースに割り当てられていない必要があります。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

サービスの詳細を取得する際には、`EXTERNAL-IP` の IP アドレスに指定した IP アドレスが反映されている必要があります。 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>ロード バランサーを削除する

内部ロード バランサーを使用しているすべてのサービスが削除されると、そのロード バランサー自体も削除されます。

## <a name="next-steps"></a>次の手順

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/