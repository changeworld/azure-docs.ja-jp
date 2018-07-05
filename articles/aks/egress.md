---
title: Azure Kubernetes Service (AKS) クラスターからエグレス トラフィックをホワイトリストに登録する
description: Azure Kubernetes Service (AKS) クラスターからエグレス トラフィックをホワイトリストに登録する
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100147"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure Kubernetes Service (AKS) のエグレス

既定では、Azure Kubernetes Service (AKS) クラスターからのエグレス アドレスはランダムに割り当てられます。 この構成は、外部サービスにアクセスするための IP アドレスを識別する必要がある場合には適していません。 このドキュメントでは、AKS クラスターで静的に割り当てられたエグレス IP アドレスを作成および維持する方法について詳しく説明します。

## <a name="egress-overview"></a>エグレスの概要

AKS クラスターからの送信トラフィックは Azure Load Balancer の規則に従っており、[こちら][outbound-connections]に記載されています。 `LoadBalancer` 型の最初の Kubernetes サービスが作成される前は、エージェント ノードは Azure Load Balancer プールの一部ではありません。 この構成では、ノードにはインスタンス レベルのパブリック IP アドレスがありません。 Azure では、送信フローを構成可能でも決定論的でもないパブリック ソース IP アドレスに変換します。

`LoadBalancer` 型の Kubernetes サービスが作成されると、エージェント ノードは Azure Load Balancer プールに追加されます。 送信フローの場合、Azure はこれをロード バランサーで構成された最初のパブリック IP アドレスに変換します。

## <a name="create-a-static-public-ip"></a>静的パブリック IP を作成する

ランダム IP アドレスが使用されないようにするには、静的 IP アドレスを作成し、ロード バランサーがこのアドレスを使用することを確認します。 AKS **ノード** リソース グループで、IP アドレスが作成される必要があります。

[az resource show][az-resource-show] コマンドを使用して、リソース グループの名前を取得します。 リソース グループの名前とクラスターの名前は、お使いの環境に合わせて更新してください。

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

次に、[az network public-ip create][public-ip-create] コマンドを使用して、静的パブリック IP アドレスを作成します。 最後の手順で収集した名前と一致するように、リソース グループ名を更新してください。

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>静的 IP を使用してサービスを作成する

これで IP アドレスを取得したので、`LoadBalancer` 型の Kubernetes サービスを作成し、IP アドレスをサービスに割り当てます。

`egress-service.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 お使いの環境に合わせて IP アドレスを更新します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

`kubectl apply` コマンドを使用して、サービスとデプロイを作成します。

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

このサービスを作成すると、Azure Load Balancer に新しいフロントエンド IP が構成されます。 他の IP が構成されていない場合は、**すべての**エグレス トラフィックがこのアドレスを使用するようになります。 Azure Load Balancer に複数のアドレスが構成されている場合、エグレスはそのロード バランサーの最初の IP を使用します。

## <a name="verify-egress-address"></a>エグレス アドレスを確認する

パブリック IP アドレスが使用されていることを確認するには、`checkip.dyndns.org` などのサービスを使用します。

ポッドを起動して接続します。

```console
$ kubectl run -it --rm aks-ip --image=debian
```

必要に応じて、コンテナーに curl をインストールします。

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org` は、エグレス IP アドレスを返します。

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

IP アドレスが、Azure Load Balancer に接続されている静的 IP アドレスと一致していることが分かります。

## <a name="ingress-controller"></a>イングレス コントローラー

Azure Load Balancer に複数のパブリック IP アドレスを維持しないようにするには、イングレス コントローラーの使用を検討してください。 イングレス コントローラーを使用すると、負荷分散、SSL/TLS の終了、URI 書き換えサポート、アップストリーム SSL/TLS 暗号化などの利点があります。 AKS のイングレス コントローラーの詳細については、「[AKS クラスターで NGINX イングレス コントローラーを構成する][ingress-aks-cluster]」ガイドを参照してください。

## <a name="next-steps"></a>次の手順

このドキュメントで示されているソフトウェアについてさらに学習してください。

- [Helm CLI][helm-cli-install]
- [NGINX イングレス コントローラー][nginx-ingress]
- [Azure Load Balancer の送信接続][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
