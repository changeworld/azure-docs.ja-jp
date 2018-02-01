---
title: "AKS での Azure ディスクの使用"
description: "AKS での Azure ディスクの使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: a4e4ce6a23f9f8a99d8ae5f9e4e2084e3b749017
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Azure ディスクを含む永続ボリューム - 動的プロビジョニング

永続ボリュームとは、Kubernetes クラスターで使用するためにプロビジョニングされたストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 このドキュメントでは、、Azure ディスクを AKS クラスター内の Kubernetes 永続ボリュームとして動的にプロビジョニングする方法について詳しく説明します。 

Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。

## <a name="built-in-storage-classes"></a>組み込みストレージ クラス

ストレージ クラスは、動的に作成された永続ボリュームの構成方法を定義するために使用されます。 Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

各 AKS クラスターには 2 つの事前作成されたストレージ クラスが含まれ、どちらも Azure ディスクで動作するように構成されています。 これらを表示するには、`kubectl get storageclass` コマンドを使います。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

これらのストレージ クラスでニーズが満たされる場合は、新しいクラスを作成する必要はありません。

## <a name="create-storage-class"></a>ストレージ クラスの作成

Azure ディスク用に構成された新しいストレージ クラスを作成する場合は、次のサンプル マニフェストを使って実行できます。 

`storageaccounttype` の値 `Standard_LRS` は、Standard ディスクを作成することを示します。 この値を `Premium_LRS` に変更すると、 [Premium ディスク][premium-storage]を作成できます。 Premium ディスクを使うには、AKS ノードの仮想マシンが Premium ディスクと互換性のあるサイズになっている必要があります。 参互換性のあるサイズの一覧については、[こちらのドキュメント][premium-storage]をご覧ください。

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```



## <a name="create-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求は、ストレージ クラス オブジェクトを使って、ストレージの一部を動的にプロビジョニングします。 Azure ディスクを使うと、AKS リソースと同じリソース グループにディスクが作成されます。

このマニフェストの例では、`azure-managed-disk` ストレージ クラスを使って、サイズが `5GB` でアクセス許可が `ReadWriteOnce` であるディスクを作成するための永続ボリューム要求が作成されます。 PVC アクセス モードについて詳しくは、[アクセス モード][access-modes]に関するページをご覧ください。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>永続ボリュームの使用

永続ボリューム要求が作成され、ディスクが正常にプロビジョニングされると、ディスクへのアクセスを使ってポッドを作成できます。 次のマニフェストでは、永続ボリューム要求 `azure-managed-disk` を使って、`/var/www/html` パスに Azure ディスクをマウントするポッドが作成されます。 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>次の手順

Azure ディスクを使った Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure ディスク対応の Kubernetes プラグイン][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[premium-storage]: ../virtual-machines/windows/premium-storage.md