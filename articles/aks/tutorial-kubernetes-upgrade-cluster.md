---
title: "Kubernertes on Azure のチュートリアル - クラスターの更新 | Microsoft Docs"
description: "Kubernertes on Azure のチュートリアル - クラスターの更新"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2cb81b5cd8b70df8077d9574e0232bc6b3d37c52
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Azure Container Service (AKS) での Kubernetes のアップグレード

Azure Container Service (AKS) クラスターは、Azure CLI を使用してアップグレードできます。 アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、Kubernetes ノードは慎重に[切断およびドレインされます](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)。

このチュートリアル (8 部構成の第 8 部) では、Kubernetes クラスターがアップグレードされます。 以下のタスクを行います。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./tutorial-kubernetes-prepare-app.md)」に戻ってください。


## <a name="get-cluster-versions"></a>クラスター バージョンを取得する

クラスターをアップグレードする前に、`az aks get-versions` コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

ここでは、現在のノード バージョンが `1.7.7` であり、バージョン `1.7.9`、`1.8.1`、および `1.8.2` が使用可能なことがわかります。

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>クラスターをアップグレードする

`az aks upgrade` コマンドを使用してクラスター ノードをアップグレードします。 次の例では、クラスターをバージョン `1.8.2` に更新します。

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

出力:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
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
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
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

## <a name="validate-upgrade"></a>アップグレードを検証する

ここで、`az aks show` コマンドを使用して、アップグレードが成功したことを確認できます。

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

出力:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、AKS クラスター内の Kubernetes をアップグレードしました。 次のタスクを完了しました。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

AKS の詳細については、このリンクに従ってください。

> [!div class="nextstepaction"]
> [AKS の概要](./intro-kubernetes.md)