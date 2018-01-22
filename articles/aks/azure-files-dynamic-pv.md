---
title: "AKS で Azure Files を使用する"
description: "AKS での Azure ディスクの使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4873b98c8ba4f1e574be20baebef3b6860341529
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Azure ファイルを含む永続ボリューム - 動的プロビジョニング

永続ボリュームとは、Kubernetes クラスターで使用するためにプロビジョニングされたストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 このドキュメントでは、、Azure ファイル共有を AKS クラスター内の Kubernetes 永続ボリュームとして動的にプロビジョニングする方法について詳しく説明します。 

Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure ファイル共有を Kubernetes ボリュームとして動的にプロビジョニングするときは、AKS クラスターと同じリソース グループに含まれている限り、任意のストレージ アカウントを使用できます。 必要であれば、AKS クラスターと同じリソース グループ内にストレージ アカウントを作成します。 

適切なリソース グループを識別するには、[az group list][az-group-list] コマンドを使用します。

```azurecli-interactive
az group list --output table
```

次の出力例に示されているリソース グループはどちらも AKS クラスターに関連付けられています。 *MC_myAKSCluster_myAKSCluster_eastus* のような名前のリソース グループには、AKS クラスター リソースが含まれます。ストレージ アカウントはここに作成する必要があります。 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

リソース グループを識別したら、[az storage account create][az-storage-account-create] コマンドでストレージ アカウントを作成します。

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>ストレージ クラスの作成

ストレージ クラスは、動的に作成された永続ボリュームの構成方法を定義するために使用されます。 Azure ストレージ アカウント名、SKU、リージョンなどの項目が、ストレージ クラス オブジェクトに定義されます。 Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

次の例では、ストレージが要求されたときに、`eastus` リージョン内の SKU タイプ `Standard_LRS` のすべてのストレージ アカウントを使用できることが指定されます。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

特定のストレージ アカウントを使用するには、`storageAccount` パラメーターを使用できます。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求は、ストレージ クラス オブジェクトを使用して、ストレージの一部を動的にプロビジョニングします。 Azure Files を使用すると、ストレージ クラス オブジェクトで選択または指定されたストレージ アカウントに Azure ファイル共有が作成されます。

>  [!NOTE]
>   適切なストレージ アカウントが、AKS クラスター リソースと同じリソース グループ内に事前に作成されていることを確認します。 このリソース グループには *MC_myAKSCluster_myAKSCluster_eastus* のような名前が付けられています。 ストレージ アカウントが使用できない場合、永続ボリューム要求は Azure ファイル共有のプロビジョニングに失敗します。 

次のマニフェストを使用すると、サイズが `5GB` で `ReadWriteOnce` アクセスの永続ボリューム要求を作成できます。 PVC アクセス モードについて詳しくは、[アクセス モード][access-modes]に関するページをご覧ください。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>永続ボリュームの使用

永続ボリューム要求が作成され、ストレージが正常にプロビジョニングされると、ボリュームへのアクセスを使用してポッドを作成できます。 次のマニフェストでは、永続ボリューム要求 `azurefile` を使用して、`/var/www/html` パスに Azure ファイル共有をマウントするポッドが作成されます。 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>マウント オプション

fileMode と dirMode の既定値は、次の表に示すように Kubernetes のバージョンによって異なります。 

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0 - v1.8.5 | 0700 |
| v1.8.6 以上 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 以上 | 0755 |

バージョン 1.8.5 以上のクラスターを使用している場合は、マウント オプションをストレージ クラス オブジェクトに指定できます。 次の例では、`0777`が設定されます。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

バージョン 1.8.0 - 1.8.4 のクラスターを使用している場合は、`runAsUser` の値を `0` に設定してセキュリティ コンテキストを指定できます。 ポッドのセキュリティ コンテキストについて詳しくは、[セキュリティ コンテキストの構成][kubernetes-security-context]に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Azure Files を使用した Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 対応の Kubernetes プラグイン][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create