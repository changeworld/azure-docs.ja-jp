---
title: AKS で Azure Files を使用する
description: AKS での Azure ディスクの使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 21245688076cf0a21164b549eb68bc6f55d6ec6c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Azure ファイルを含む永続ボリューム

永続ボリュームとは、Kubernetes クラスターで使用するためにプロビジョニングされたストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 このドキュメントでは、、Azure ファイル共有を AKS クラスター内の Kubernetes 永続ボリュームとして動的にプロビジョニングする方法について詳しく説明します。

Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

## <a name="create-storage-account"></a>[ストレージ アカウントの作成]

Azure ファイル共有を Kubernetes ボリュームとして動的にプロビジョニングするときは、AKS クラスターと同じリソース グループに含まれている限り、任意のストレージ アカウントを使用できます。 必要であれば、AKS クラスターと同じリソース グループ内にストレージ アカウントを作成します。

適切なリソース グループを識別するには、[az group list][az-group-list] コマンドを使用します。

```azurecli-interactive
az group list --output table
```

`MC_clustername_clustername_locaton` のような名前のリソース グループを検索します。clustername は AKS クラスターの名前、location はクラスターが展開されている Azure リージョンです。

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

[az storage account create][az-storage-account-create] コマンドを使用して、ストレージ アカウントを作成します。

この例を参考にして、`--resource-group` をリソース グループの名前に、`--name` を任意の名前に更新します。

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>ストレージ クラスの作成

ストレージ クラスを使用して、Azure ファイル共有を作成する方法を定義します。 クラス内に特定のストレージ アカウントを指定できます。 ストレージ アカウントが指定されない場合は、`skuName` と `location` が指定される必要があり、関連するリソース グループ内のすべてのストレージ アカウントが一致するかどうかの評価が行われます。

Azure ファイル用の Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

`azure-file-sc.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 `storageAccount` をターゲットのストレージ アカウントの名前に更新します。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

[kubectl apply][kubectl-apply] コマンドを使用して、ストレージ クラスを作成します。

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) は、ストレージ クラス オブジェクトを使用して、Azure ファイル共有を動的にプロビジョニングします。

次のマニフェストを使用すると、サイズが `5GB` で `ReadWriteOnce` アクセスの永続ボリューム要求を作成できます。

`azure-file-pvc.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 `storageClassName` が最後の手順で作成したストレージ クラスと一致していることを確認します。

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

[kubectl apply][kubectl-apply] コマンドを使用して、永続ボリューム要求を作成します。

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

完了すると、ファイル共有が作成されます。 接続情報と資格情報を含む Kubernetes シークレットも作成されます。

## <a name="using-the-persistent-volume"></a>永続ボリュームの使用

次のマニフェストでは、永続ボリューム要求 `azurefile` を使用して、`/mnt/azure` パスに Azure ファイル共有をマウントするポッドが作成されます。

`azure-pvc-files.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 `claimName` が最後の手順で作成したPVC と一致していることを確認します。

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
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

[kubectl apply][kubectl-apply] コマンドを使用して、ポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

これで Azure ディスクが `/mnt/azure` ディレクトリにマウントされ、ポッドが稼働状態となりました。 この構成は、`kubectl describe pod mypod` 経由でポッドを調べるときに表示できます。

## <a name="mount-options"></a>マウント オプション

fileMode と dirMode の既定値は、次の表に示すように Kubernetes のバージョンによって異なります。

| バージョン | 値 |
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
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
