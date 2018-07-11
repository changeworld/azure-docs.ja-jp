---
title: Kubernetes on Azure のチュートリアル - クラスターの更新
description: Kubernetes on Azure のチュートリアル - クラスターの更新
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131645"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) での Kubernetes のアップグレード

Azure Kubernetes Service (AKS) クラスターは、Azure CLI を使用してアップグレードできます。 アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、Kubernetes ノードは慎重に[切断およびドレインされます][kubernetes-drain]。

このチュートリアル (7 部構成の第 7 部) では、Kubernetes クラスターがアップグレードされます。 以下のタスクを行います。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="get-cluster-versions"></a>クラスター バージョンを取得する

クラスターをアップグレードする前に、[az aks get-upgrades][] コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

次の例では、ノードの現在のバージョンが *1.9.6* であることがわかります。また、*[Upgrades]* 列からは利用可能なアップグレードのバージョンがわかります。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>クラスターをアップグレードする

[az aks upgrade][] コマンドを使用してクラスター ノードをアップグレードします。 次の例では、クラスターをバージョン *1.10.3* に更新します。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

次に示したのは、その出力例の抜粋です。*kubernetesVersion* が *1.10.3* として報告されていることがわかります。

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>アップグレードを検証する

[az aks show][] コマンドを使用して、アップグレードが成功したことを確認します。

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

出力:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、AKS クラスター内の Kubernetes をアップグレードしました。 次のタスクを完了しました。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

AKS の詳細については、このリンクに従ってください。

> [!div class="nextstepaction"]
> [AKS の概要][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade