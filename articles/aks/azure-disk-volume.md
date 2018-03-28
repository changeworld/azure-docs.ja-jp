---
title: AKS での Azure ディスクの使用
description: AKS での Azure ディスクの使用
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a2f46aba80ad47335b7cd9b5e8d615c1d895cccb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="volumes-with-azure-disks"></a>Azure ディスクを使用するボリューム

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 この外部データ ストアとして Azure ディスクを使用することができます。 この記事では、Azure Container Service (AKS) クラスターの Kubernetes ボリュームとして Azure ディスクを使用する方法について詳しく説明します。

Kubernetes ボリュームの詳細については、[Kubernetes ボリューム][kubernetes-volumes]に関するページを参照してください。

## <a name="create-an-azure-disk"></a>Azure ディスクを作成する

Azure 管理ディスクを Kubernetes ボリュームとしてマウントする前に、ディスクが AKS クラスター リソースと同じリソース グループに存在する必要があります。 このリソース グループを検索するには、[az group list][az-group-list] を使用します。

```azurecli-interactive
az group list --output table
```

`MC_clustername_clustername_locaton` のような名前のリソース グループを検索します。clustername は AKS クラスターの名前、location はクラスターが展開されている Azure リージョンです。

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Azure ディスクを作成するには、[az disk create][az-disk-create] コマンドを使用します。 

この例を参考にして、`--resource-group` をリソース グループの名前に、`--name` を任意の名前に更新します。

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

コマンドが完了すると、次のような出力が表示されます。 この値はディスク ID です。この ID は Kubernetes ポッドにディスクをマウントするときに使用されます。

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
