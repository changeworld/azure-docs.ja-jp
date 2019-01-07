---
title: Azure Kubernetes Service (AKS) で内部ロード バランサーを作成する
description: サービスを Azure Kubernetes Service (AKS) を使用して公開する内部ロード バランサーを作成して使用する方法を示します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 5fe1739fa341a5e3bd2f4d26ef1e94d725b74a61
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001236"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する

Azure Kubernetes Service (AKS) でアプリケーションへのアクセスを制限するために、内部ロード バランサーを作成して使用できます。 内部ロード バランサーは、Kubernetes サービスを Kubernetes クラスターと同じ仮想ネットワークで実行されているアプリケーションからのみアクセス可能にします。 この記事では、Azure Kubernetes Service (AKS) を使用して内部ロード バランサーを使用する方法を示します。

> [!NOTE]
> Azure Load Balancer は、*Basic* と *Standard* の 2 つの SKU で使用できます。 詳細については、[Azure ロード バランサー SKU の比較][azure-lb-comparison]に関するページを参照してください。 AKS は現在、*Basic* SKU をサポートしています。 *Standard* SKU を使用する場合は、上流の [aks-engine][aks-engine] を使用できます。

## <a name="create-an-internal-load-balancer"></a>内部ロード バランサーを作成します。

内部ロード バランサーを作成するには、次の例に示すように、サービスの種類 *LoadBalancer* と *azure-load-balancer-internal* の注釈を含む `internal-lb.yaml` という名前のサービス マニフェストを作成します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

`kubectl apply -f internal-lb.yaml` を使用してデプロイすると、Azure ロード バランサーが作成され、AKS クラスターと同じ仮想ネットワークで使用可能になります。

サービスの詳細を表示すると、内部ロード バランサーの IP アドレスが *EXTERNAL-IP* 列に示されます。 次の例に示すように、IP アドレスが *\<[保留中]\>* から実際の内部 IP アドレスに変わるには 1 ～ 2 分かかることがあります。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP アドレスを指定する

内部ロード バランサーに特定の IP アドレスを使用する場合は、ロード バランサーの YAML マニフェストに *loadBalancerIP* プロパティを追加します。 指定した IP アドレスは AKS クラスターと同じサブネットに存在し、まだリソースに割り当てられていない必要があります。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

サービスの詳細を表示すると、*EXTERNAL-IP* 列の IP アドレスに、指定された IP アドレスが反映されます。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>プライベート ネットワークを使用する

AKS クラスターを作成するときに、高度なネットワーク設定を指定できます。 このアプローチでは、既存の Azure 仮想ネットワークとサブネットにクラスターをデプロイできます。 1 つのシナリオは、オンプレミス環境に接続されたプライベート ネットワークに AKS クラスターをデプロイし、内部的にのみアクセスできるサービスを実行することです。 詳細については、[AKS の高度なネットワーク構成][advanced-networking]に関する説明を参照してください。

プライベート ネットワークを使用する AKS クラスターに内部ロード バランサーをデプロイする場合、前の手順を変更する必要はありません。 ロード バランサーは、AKS クラスターと同じリソース グループ内に作成されますが、次の例に示すように、プライベート仮想ネットワークとサブネットに接続されます。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS クラスターのサービス プリンシパルに対して、Azure 仮想ネットワーク リソースがデプロイされるリソース グループへの*ネットワーク共同作成者*ロールを付与しなければならない場合があります。 [az aks show][az-aks-show] を使用してサービス プリンシパルを表示します (例: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`)。 ロールの割り当てを作成するには、[az role assignment create][az-role-assignment-create] コマンドを使用します。

## <a name="specify-a-different-subnet"></a>別のサブネットを指定する

ロード バランサーのサブネットを指定するには、サービスに *azure-load-balancer-internal-subnet* 注釈を追加します。 指定されたサブネットは、AKS クラスターと同じ仮想ネットワーク内に存在する必要があります。 デプロイされると、ロード バランサーの *EXTERNAL-IP* アドレスが、指定されたサブネットの一部になります。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>ロード バランサーを削除する

内部ロード バランサーを使用しているすべてのサービスが削除されると、ロード バランサー自体も削除されます。

また、すべての Kubernetes リソース (`kubectl delete service internal-app` など) と同様に、サービスを直接削除することもできます。それにより、基礎となる Azure ロード バランサーも削除されます。

## <a name="next-steps"></a>次の手順

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-advanced-networking.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus