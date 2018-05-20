---
title: Azure Kubernetes Service (AKS) クラスターのアップグレード
description: Azure Kubernetes Service (AKS) クラスターのアップグレード
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 3fae465512501b0642d0215df7b73c9879a1a3a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターのアップグレード

Azure Kubernetes Service (AKS) により、Kubernetes クラスターのアップグレードなど、一般的な管理タスクを容易に実行することができます。

## <a name="upgrade-an-aks-cluster"></a>AKS クラスターのアップグレード

クラスターをアップグレードする前に、`az aks get-upgrades` コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

出力:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

アップグレードに利用できるバージョンは、1.9.1、1.9.2、および 1.9.6 の 3 つです。 `az aks upgrade` コマンドを使用して、利用可能な最新バージョンにアップグレードします。  アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に[切断およびドレイン][kubernetes-drain]されます。  クラスター ノードの追加と削除が行われるため、クラスターのアップグレードを開始する前に、ワークロードを処理できる十分な追加のコンピューティング容量があることを確認します。

> [!NOTE]
> AKS クラスターをアップグレードする際に、Kubernetes マイナー バージョンをスキップすることはできません。 たとえば、1.7.x から 1.8.x や、1.8.x から 1.9.x へのアップグレードは許可されていますが、1.7 から 1.9 へのアップグレードは許可されていません。

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
```

出力:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
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
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.9.6",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

`az aks show` コマンドを使用して、アップグレードが成功したことを確認します。

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

出力:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>次の手順

AKS のデプロイと管理の詳細を AKS チュートリアルで確認してください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md