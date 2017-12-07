---
title: "Azure Container Service (AKS) クラスターの更新"
description: "Azure Container Service (AKS) クラスターの更新"
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 40b55309ee4c52743b30682d8751e6e432f9bb4a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Azure Container Service (AKS) クラスターの更新

Azure Container Service (AKS) により、Kubernetes クラスターのアップグレードなど、一般的な管理タスクを容易に実行することができます。

## <a name="upgrade-an-aks-cluster"></a>AKS クラスターのアップグレード

クラスターをアップグレードする前に、`az aks get-versions` コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

出力:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

アップグレードで利用できるバージョンは、1.7.9、1.8.1、および 1.8.2 の 3 つです。 `az aks upgrade` コマンドを使用して、利用可能な最新バージョンにアップグレードします。  アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に[切断およびドレインされます](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)。  クラスター ノードの追加と削除が行われるため、クラスターのアップグレードを開始する前に、ワークロードを処理できる十分な追加のコンピューティング容量があることを確認します。

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

出力:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
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
    "kubernetesVersion": "1.8.2",
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

ここで、`az aks show` コマンドを使用して、アップグレードが成功したことを確認できます。

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

出力:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>次のステップ

AKS のデプロイと管理の詳細を AKS チュートリアルで確認してください。

> [!div class="nextstepaction"]
> [AKS チュートリアル](./tutorial-kubernetes-prepare-app.md)