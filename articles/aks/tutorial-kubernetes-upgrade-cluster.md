---
title: Kubernetes on Azure のチュートリアル - クラスターのアップグレード
description: この Azure Kubernetes Service (AKS) のチュートリアルでは、既存の AKS クラスターを最新の使用可能な Kubernetes バージョンにアップグレードする方法を学習します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "41918961"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) での Kubernetes のアップグレード

アプリケーションとクラスターのライフサイクルの一環として、使用可能な最新バージョンの Kubernetes にアップグレードし、新しい機能を使用することができます。 Azure Kubernetes Service (AKS) クラスターは、Azure CLI を使用してアップグレードできます。 アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、Kubernetes ノードは慎重に[切断およびドレインされます][kubernetes-drain]。

このチュートリアル (7 部構成の第 7 部) では、Kubernetes クラスターがアップグレードされます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

このチュートリアルでは、Azure CLI バージョン 2.0.44 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="get-available-cluster-versions"></a>使用可能なクラスターのバージョンを取得する

クラスターをアップグレードする前に、[az aks get-upgrades][] コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

次の例では、現在のバージョンが *1.9.6* であることがわかります。また、*[Upgrades]* 列からは利用可能なアップグレードのバージョンがわかります。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>クラスターをアップグレードする

[az aks upgrade][] コマンドを使用して、AKS クラスターをアップグレードします。 次の例では、クラスターを Kubernetes バージョン *1.10.6* にアップグレードします。

> [!NOTE]
> 一度に 1 つのマイナー バージョンのみをアップグレードできます。 たとえば、*1.9.6* から *1.10.3* にアップグレードすることはできますが、*1.9.6* から *1.11.x* に直接アップグレードすることはできません。 *1.9.6* から *1.11.x* にアップグレードするには、まず *1.9.6* から *1.10.3* にアップグレードします。その後、*1.10.3* から *1.11.x* へのアップグレードを別個に実行します。

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

次に示したのは、その出力例の抜粋です。*kubernetesVersion* が *1.10.6* として報告されていることがわかります。

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
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>アップグレードを検証する

[az aks show][] コマンドを次のように使用して、アップグレードが成功したことを確認します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、AKS クラスターで *KubernetesVersion 1.10.6* が実行されていることを示しています。

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、AKS クラスター内の Kubernetes をアップグレードしました。 以下の方法について学習しました。

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
[azure-cli-install]: /cli/azure/install-azure-cli