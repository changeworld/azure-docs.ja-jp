---
title: "Azure Container Service (AKS) ロード バランサーで静的 IP アドレスを使用する"
description: "Azure Container Service (AKS) ロード バランサーで静的 IP アドレスを使用します。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>Azure Container Service (AKS) ロード バランサーで静的 IP アドレスを使用する

Azure Container Service (AKS) ロード バランサーが再作成される場合や、LoadBalancer 型の Kubernetes サービスが再作成される場合などは、Kubernetes サービスのパブリック IP アドレスが変更されることがあります。 このドキュメントでは、Kubernetes サービスの静的 IP アドレスの構成について詳しく説明します。

## <a name="create-static-ip-address"></a>静的 IP アドレスを作成する

Kubernetes サービス用の静的パブリック IP アドレスを作成します。 IP アドレスは、クラスターのデプロイ中に自動作成されたリソース グループに作成する必要があります。 別の AKS リソース グループについてと、自動作成されたリソース グループを識別する方法については、[AKS の FAQ][aks-faq-resource-group] に関するページを参照してください。

[az network public ip create][az-network-public-ip-create] コマンドを使用して、IP アドレスを作成します。

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

IP アドレスを書き留めておきます。

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 必要であれば、アドレスは [az network public-ip list][az-network-public-ip-list] コマンドを使用して取得することができます。

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>IP アドレスでサービスを作成する

静的 IP アドレスがプロビジョニングされたら、`loadBalancerIP` プロパティと静的 IP アドレスの値を使用して、Kubernetes サービスを作成できます。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>トラブルシューティング

静的 IP アドレスが作成されていないか、間違ったリソース グループに作成された場合、サービスの作成は失敗します。 トラブルシューティングを行うには、[kubectl describe][kubectl-describe] コマンドでサービス作成イベントを返します。

```console
$ kubectl describe service azure-vote-front

Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list