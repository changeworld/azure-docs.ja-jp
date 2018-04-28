---
title: AKS での Azure ディスクの使用
description: AKS での Azure ディスクの使用
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: a6bc79d0556299634a78c5232bbab4e20810172c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Azure ディスクを含む永続ボリューム

永続ボリュームとは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

このドキュメントでは、Azure ディスクの永続ボリュームを Azure Container Service (AKS) クラスターで使用することの詳細を説明します。

> [!NOTE]
> Azure ディスクは、アクセス モードの種類を ReadWriteOnce としてのみマウントでき、この場合、ディスクの利用は、単一の AKS ノードに限られます。 複数のノード間で固定ボリュームを共有する必要がある場合は、[Azure Files][azure-files-pvc] の使用を検討してください。

## <a name="built-in-storage-classes"></a>組み込みストレージ クラス

ストレージ クラスは、保存の単位を永続ボリュームを使用して動的に作成する方法を定義します。 Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

各 AKS クラスターには 2 つの事前作成されたストレージ クラスが含まれ、どちらも Azure ディスクで動作するように構成されています。 `default` ストレージ クラスは、標準の Azure ディスクをプロビジョニングします。 `managed-premium` ストレージ クラスは、プレミアムな Azure ディスクをプロビジョニングします。 クラスター内の AKS ノードでプレミアム ストレージを使用する場合は、`managed-premium` クラスを選択します。

事前作成されているストレージ クラスを確認するには、[kubectl get sc][kubectl-get] コマンドを使用します。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 この場合、PVC は、事前作成されているストレージ クラスのいずれかを使用して、標準のまたはプレミアムな Azure 管理ディスクを作成できます。

`azure-premimum.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

注釈に `managed-premium` ストレージ クラスが指定されていることと、要求ではサイズ `5GB` のディスクを `ReadWriteOnce` アクセスで要求していることを書き留めておきます。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

[kubectl create][kubectl-create] コマンドを使用して、永続ボリューム要求を作成します。

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>永続ボリュームの使用

永続ボリューム要求が作成され、ディスクが正常にプロビジョニングされると、ディスクへのアクセスを使ってポッドを作成できます。 次のマニフェストでは、永続ボリューム要求 `azure-managed-disk` を使って、`/mnt/azure` パスに Azure ディスクをマウントするポッドが作成されます。

`azure-pvc-disk.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

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
        claimName: azure-managed-disk
```

[kubectl create][kubectl-create] コマンドを使用して、ポッドを作成します。

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

これで Azure ディスクが `/mnt/azure` ディレクトリにマウントされ、ポッドが稼働状態となりました。 この構成は、`kubectl describe pod mypod` 経由でポッドを調べるときに表示できます。

## <a name="next-steps"></a>次の手順

Azure ディスクを使った Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure ディスク対応の Kubernetes プラグイン][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md