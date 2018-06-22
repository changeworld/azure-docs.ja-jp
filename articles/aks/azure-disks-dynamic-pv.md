---
title: AKS での Azure ディスクの使用
description: AKS での Azure ディスクの使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 3841222d08b23f43f69e77fa43c469793b70ce63
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801383"
---
# <a name="persistent-volumes-with-azure-disks"></a>Azure ディスクを含む永続ボリューム

永続ボリュームとは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

このドキュメントでは、Azure ディスクの永続ボリュームを Azure Kubernetes Service (AKS) クラスターで使用することの詳細を説明します。

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

> [!NOTE]
> 固定ボリュームの要求は GiB 単位で指定されますが、Azure Managed Disks は SKU の特定のサイズによって課金されます。 これらの SKU の範囲は、S4 または P4 ディスクの 32 GiB から、S50 または P50 ディスクの 4 TiB までです。 さらに、Premium 管理ディスクのスループットおよび IOPS パフォーマンスは、SKU と AKS クラスターのノードのインスタンスのサイズに依存します。 「[Managed Disks の価格 ][managed-disk-pricing-performance]」を参照してください。

## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 この場合、PVC は、事前作成されているストレージ クラスのいずれかを使用して、標準のまたはプレミアムな Azure 管理ディスクを作成できます。

`azure-premium.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

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

[kubectl apply][kubectl-apply] コマンドを使用して、永続ボリューム要求を作成します。

```azurecli-interactive
kubectl apply -f azure-premium.yaml
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

[kubectl apply][kubectl-apply] コマンドを使用して、ポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

これで Azure ディスクが `/mnt/azure` ディレクトリにマウントされ、ポッドが稼働状態となりました。 この構成は、`kubectl describe pod mypod` 経由でポッドを調べるときに表示できます。

## <a name="next-steps"></a>次の手順

Azure ディスクを使った Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure ディスク対応の Kubernetes プラグイン][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md 
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
