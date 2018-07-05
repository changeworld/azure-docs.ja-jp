---
title: AKS での Azure ディスクの使用
description: AKS での Azure ディスクの使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597069"
---
# <a name="volumes-with-azure-disks"></a>Azure ディスクを使用するボリューム

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 この外部データ ストアとして Azure ディスクを使用することができます。 この記事では、Azure Kubernetes Service (AKS) クラスターの Kubernetes ボリュームとして Azure ディスクを使用する方法について詳しく説明します。

Kubernetes ボリュームの詳細については、[Kubernetes ボリューム][kubernetes-volumes]に関するページを参照してください。

## <a name="create-an-azure-disk"></a>Azure ディスクを作成する

Azure 管理ディスクを Kubernetes ボリュームとしてマウントする前に、ディスクが AKS **ノード** リソース グループに存在する必要があります。 [az resource show][az-resource-show] コマンドを使用して、リソース グループの名前を取得します。

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Azure ディスクを作成するには、[az disk create][az-disk-create] コマンドを使用します。

最後の手順で収集したリソース グループの名前を使用して `--resource-group` を更新し、`--name` を任意の名前に更新します。

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

コマンドが完了すると、次のような出力が表示されます。 この値はディスク ID です。この ID はディスクをマウントするときに使用されます。

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>ディスクをボリュームとしてマウントする

コンテナーの指定でボリュームを構成して Azure ディスクをポッドにマウントします。

次の内容で、`azure-disk-pod.yaml` という名前の新しいファイルを作成します。 `diskName` を新しく作成したディスクの名前で更新し、`diskURI` をディスク ID で更新します。 また、`mountPath` を書き留めます。これは Azure ディスクがポッドにマウントされているパスです。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

kubectl を使用してポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

これで Azure ディスクが `/mnt/azure` にマウントされ、ポッドが稼働状態となりました。

## <a name="next-steps"></a>次の手順

Azure ディスクを使用した Kubernetes ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure ディスク対応の Kubernetes プラグイン][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
