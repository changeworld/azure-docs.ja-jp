---
title: AKS で Azure Files を使用する
description: AKS での Azure ディスクの使用
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bac80e354a4d629bfbfc8207b9fed7c69c765839
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429269"
---
# <a name="persistent-volumes-with-azure-files"></a>Azure ファイルを含む永続ボリューム

永続ボリュームとは、Kubernetes クラスターで使用するために作成されたストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的に作成できます。 このドキュメントでは、永続ボリュームとしての Azure ファイル共有の**動的な作成**について詳しく説明します。

静的な作成など、Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

## <a name="create-storage-account"></a>[ストレージ アカウントの作成]

Azure ファイル共有を Kubernetes ボリュームとして動的に作成するときは、AKS **ノード** リソース グループ内にある限り、任意のストレージ アカウントを使用できます。 これは、AKS クラスターにリソースのプロビジョニングによって作成された `MC_` プレフィックスを備えています。 [az resource show][az-resource-show] コマンドを使用して、リソース グループの名前を取得します。

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[az storage account create][az-storage-account-create] コマンドを使用して、ストレージ アカウントを作成します。

最後の手順で収集したリソース グループの名前を使用して `--resource-group` を更新し、`--name` を任意の名前に更新します。

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Azure Files は現在、Standard ストレージのみと連動します。 Premium ストレージを使用すると、ボリュームはプロビジョニングに失敗します。

## <a name="create-storage-class"></a>ストレージ クラスの作成

ストレージ クラスを使用して、Azure ファイル共有を作成する方法を定義します。 クラス内に特定のストレージ アカウントを指定できます。 ストレージ アカウントが指定されない場合は、`skuName` と `location` が指定される必要があり、関連するリソース グループ内のすべてのストレージ アカウントが一致するかどうかの評価が行われます。

Azure ファイル用の Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

`azure-file-sc.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 `storageAccount` をターゲットのストレージ アカウントの名前に更新します。 `mountOptions` の詳細については、「マウント オプション」セクションを参照してください。

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

[kubectl apply][kubectl-apply] コマンドを使用して、ストレージ クラスを作成します。

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) は、ストレージ クラス オブジェクトを使用して、Azure ファイル共有を動的にプロビジョニングします。

次の YAML を使うと、サイズが `5GB` で `ReadWriteMany` アクセスの永続ボリューム要求を作成できます。 アクセス モードについて詳しくは、[Kubernetes 永続ボリューム][ access-modes]のドキュメントをご覧ください。

`azure-file-pvc.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `storageClassName` が最後の手順で作成したストレージ クラスと一致していることを確認します。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
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

次の YAML は、永続ボリューム要求 `azurefile` を使って、`/mnt/azure` パスに Azure ファイル共有をマウントするポッドを作成します。

`azure-pvc-files.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `claimName` が最後の手順で作成したPVC と一致していることを確認します。

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

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0 - v1.8.5 | 0700 |
| v1.8.6 以上 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 以上 | 0755 |

バージョン 1.8.5 以降のクラスターを使い、ストレージ クラスに永続ボリュームを動的に作成している場合は、ストレージ クラスのオブジェクトに対してマウント オプションを指定できます。 次の例では、`0777`が設定されます。

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

バージョン 1.8.5 以降のクラスターを使い、永続ボリューム オブジェクトを静的に作成している場合は、`PersistentVolume` オブジェクトに対してマウント オプションを指定する必要があります。 永続ボリュームの静的作成について詳しくは、[静的な永続ボリューム][pv-static]に関するページをご覧ください。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

バージョン 1.8.0 - 1.8.4 のクラスターを使用している場合は、`runAsUser` の値を `0` に設定してセキュリティ コンテキストを指定できます。 ポッドのセキュリティ コンテキストについて詳しくは、[セキュリティ コンテキストの構成][kubernetes-security-context]に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Azure Files を使用した Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 対応の Kubernetes プラグイン][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
