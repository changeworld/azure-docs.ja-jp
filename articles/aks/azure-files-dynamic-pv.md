---
title: AKS で Azure Files を使用する
description: AKS での Azure ディスクの使用
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: ea77244d4b2e078c5eda716e94a97291350228f5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "42140566"
---
# <a name="persistent-volumes-with-azure-files"></a>Azure ファイルを含む永続ボリューム

永続ボリュームとは、Kubernetes クラスターで使用するために作成されたストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的に作成できます。 このドキュメントでは、永続ボリュームとしての Azure ファイル共有の**動的な作成**について詳しく説明します。

静的な作成など、Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure ファイル共有を Kubernetes ボリュームとして動的に作成するときは、AKS **ノード** リソース グループ内にある限り、任意のストレージ アカウントを使用できます。 このグループは、AKS クラスターにリソースのプロビジョニングによって作成された *MC_* プレフィックスを備えています。 [az aks show][az-aks-show] コマンドを使用して、リソース グループの名前を取得します。

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[az storage account create][az-storage-account-create] コマンドを使用して、ストレージ アカウントを作成します。

最後の手順で収集したリソース グループの名前を使用して `--resource-group` を更新し、`--name` を任意の名前に更新します。 独自の一意のストレージ アカウント名を指定します。

```azurecli
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --sku Standard_LRS
```

> [!NOTE]
> Azure Files は現在、Standard ストレージのみと連動します。 Premium ストレージを使用すると、ボリュームはプロビジョニングに失敗します。

## <a name="create-a-storage-class"></a>ストレージ クラスの作成

ストレージ クラスを使用して、Azure ファイル共有を作成する方法を定義します。 クラス内にストレージ アカウントを指定できます。 ストレージ アカウントが指定されない場合は、*skuName* と *location* が指定される必要があり、関連するリソース グループ内のすべてのストレージ アカウントが一致するかどうかの評価が行われます。 Azure Files 用の Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

`azure-file-sc.yaml` という名前のファイルを作成し、次の例のマニフェストにコピーします。 *storageAccount* の値を、前の手順で作成したストレージ アカウントの名前に更新します。 *mountOptions* について詳しくは、「[マウント オプション][mount-options]」セクションをご覧ください。

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
  storageAccount: mystorageaccount
```

[kubectl apply][kubectl-apply] コマンドを使用して、ストレージ クラスを作成します。

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>クラスターのロールとバインディングの作成

AKS クラスターでは、実行できるアクションを制限するために、Kubernetes のロールベース アクセス制御 (RBAC) が使用されます。 付与するアクセス許可を*ロール*によって定義し、それらを*バインド*によって目的のユーザーに適用します。 これらの割り当ては、特定の名前空間に適用することもできますし、クラスター全体に適用することもできます。 詳しくは、「[Using RBAC authorization (RBAC 承認の使用)][kubernetes-rbac]」をご覧ください。

Azure プラットフォームで必要なストレージ リソースを作成できるようにするには、*clusterrole* と *clusterrolebinding* を作成します。 `azure-pvc-roles.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

[kubectl apply][kubectl-apply] コマンドを使用してアクセス許可を割り当てます。

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) は、ストレージ クラス オブジェクトを使用して、Azure ファイル共有を動的にプロビジョニングします。 次の YAML を使うと、サイズが *5GB* で *ReadWriteMany* アクセスの永続ボリューム要求を作成できます。 アクセス モードについて詳しくは、[Kubernetes 永続ボリューム][ access-modes]のドキュメントをご覧ください。

`azure-file-pvc.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 *storageClassName* が最後の手順で作成したストレージ クラスと一致していることを確認します。

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

```console
kubectl apply -f azure-file-pvc.yaml
```

完了すると、ファイル共有が作成されます。 接続情報と資格情報を含む Kubernetes シークレットも作成されます。 [kubectl get][kubectl-get] コマンドを使用すると、PVC の状態を表示できます。

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>永続ボリュームの使用

次の YAML は、永続ボリューム要求 *azurefile* を使って、*/mnt/azure* パスに Azure ファイル共有をマウントするポッドを作成します。

`azure-pvc-files.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 *claimName* が最後の手順で作成したPVC と一致していることを確認します。

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

```console
kubectl apply -f azure-pvc-files.yaml
```

これで Azure ディスクが */mnt/azure* ディレクトリにマウントされ、ポッドが稼働状態となりました。 この構成は、`kubectl describe pod mypod` 経由でポッドを調べるときに表示できます。 次に示したのは、その出力例の抜粋です。コンテナーにマウントされたボリュームが表示されています。

```
Containers:
  myfrontend:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile2
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>マウント オプション

*fileMode* と *dirMode* の既定値は、次の表に示すように Kubernetes のバージョンによって異なります。

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0 - v1.8.5 | 0700 |
| v1.8.6 以上 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 以上 | 0755 |

バージョン 1.8.5 以降のクラスターを使い、ストレージ クラスに永続ボリュームを動的に作成している場合は、ストレージ クラスのオブジェクトに対してマウント オプションを指定できます。 次の例では、*0777* が設定されます。

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

バージョン 1.8.5 以降のクラスターを使い、永続ボリューム オブジェクトを静的に作成している場合は、*PersistentVolume* オブジェクトに対してマウント オプションを指定する必要があります。 永続ボリュームの静的作成について詳しくは、[静的な永続ボリューム][pv-static]に関するページをご覧ください。

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

バージョン 1.8.0 - 1.8.4 のクラスターを使用している場合は、*runAsUser* の値を *0* に設定してセキュリティ コンテキストを指定できます。 ポッドのセキュリティ コンテキストについて詳しくは、[セキュリティ コンテキストの構成][kubernetes-security-context]に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Azure Files を使用した Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 対応の Kubernetes プラグイン][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
