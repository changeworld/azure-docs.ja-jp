---
title: Azure Kubernetes Service (AKS) の内部ロード バランサーを作成する
description: Azure Kubernetes Service (AKS) で内部ロード バランサーを使用します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097922"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する

内部負荷分散により、Kubernetes サービスが Kubernetes クラスターと同じ仮想ネットワークで実行されているアプリケーションにアクセスできるようになります。 このドキュメントでは、Azure Kubernetes Service (AKS) で内部ロード バランサーを作成する方法について説明します。 Azure Load Balancer は、Basic と Standard の 2 種類の SKU で使用できます。 AKS は、基本的な SKU を使用します。

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
