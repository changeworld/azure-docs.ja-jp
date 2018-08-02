---
title: Azure Kubernetes サービスでの永続ボリュームの作成
description: Azure ディスクを使用して、Azure Kubernetes Service (AKS) のポッド用の永続ボリュームを作成する方法について説明します
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 129a39294d52a312c91fc6a4fd009d76e88b4ff7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185224"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 用の Azure ディスクによる永続ボリュームの作成

永続ボリュームとは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 Kubernetes 永続ボリュームについて詳しくは、[Kubernetes 永続ボリューム][kubernetes-volumes]に関するページをご覧ください。 この記事では、Azure ディスクの永続ボリュームを Azure Kubernetes Service (AKS) クラスターで使用することの詳細を説明します。

> [!NOTE]
> Azure ディスクは、*アクセス モード*の種類を *ReadWriteOnce* としてのみマウントでき、この場合、ディスクの利用は、単一の AKS ノードに限られます。 複数のノード間で固定ボリュームを共有する必要がある場合は、[Azure Files][azure-files-pvc] の使用を検討してください。

## <a name="built-in-storage-classes"></a>組み込みストレージ クラス

ストレージ クラスは、保存の単位を永続ボリュームを使用して動的に作成する方法を定義します。 Kubernetes ストレージ クラスについて詳しくは、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページをご覧ください。

各 AKS クラスターには 2 つの事前作成されたストレージ クラスが含まれ、どちらも Azure ディスクで動作するように構成されています。

* *default* ストレージ クラスは、標準の Azure ディスクをプロビジョニングします。
    * Standard ストレージでは、HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。
* *managed-premium* ストレージ クラスは、プレミアムな Azure ディスクをプロビジョニングします。
    * Premium ディスクは、SSD ベースの高性能で待機時間の短いディスクによってサポートされています。 実稼働ワークロードを実行する VM に最適です。 クラスター内の AKS ノードでプレミアム ストレージを使用する場合は、*managed-premium* クラスを選択します。

事前作成されているストレージ クラスを確認するには、[kubectl get sc][kubectl-get] コマンドを使用します。 次の例は、AKS クラスター内で使用できる事前に作成されたストレージ クラスを示したものです。

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> 固定ボリュームの要求は GiB 単位で指定されますが、Azure Managed Disks は SKU の特定のサイズによって課金されます。 これらの SKU の範囲は、S4 または P4 ディスクの 32 GiB から、S50 または P50 ディスクの 4 TiB までです。 Premium 管理ディスクのスループットと IOPS パフォーマンスは、SKU と AKS クラスターのノードのインスタンスのサイズに依存します。 詳細については、「[Managed Disks の価格 ][managed-disk-pricing-performance]」を参照してください。

## <a name="create-a-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 この場合、PVC は、事前作成されているストレージ クラスのいずれかを使用して、標準のまたはプレミアムな Azure マネージド ディスクを作成できます。

`azure-premium.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。 要求は、サイズが *5GB* で *ReadWriteOnce* アクセスがある `azure-managed-disk` という名前のディスクを要求します。 ストレージ クラスとして、*managed-premium* ストレージ クラスが指定されます。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Standard ストレージを使用するディスクを作成するには、*managed-premium*ではなく、`storageClassName: default` を使用します。

[kubectl create][kubectl-create] コマンドで、*azure-premium.yaml* ファイルを指定して永続ボリューム要求を作成します。

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>永続ボリュームの使用

永続ボリューム要求が作成され、ディスクが正常にプロビジョニングされると、ディスクへのアクセスを使ってポッドを作成できます。 次のマニフェストは、*azure-managed-disk* という名前の永続ボリューム要求を使って `/mnt/azure` パスに Azure ディスクをマウントする基本的な NGINX ポッドを作成します。

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

次の例に示すように[kubectl create][kubectl-create] コマンドでポッドを作成します。

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

これで Azure ディスクが `/mnt/azure` ディレクトリにマウントされ、ポッドが稼働状態となりました。 次の例に示すように、`kubectl describe pod mypod` でポッドを検査するときに、この構成を確認できます。

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>永続ボリュームのバックアップ

永続ボリュームのデータをバックアップするには、ボリュームのマネージド ディスクのスナップショットを取得します。 その後、このスナップショットを使用して復元されたディスクを作成し、データを復元するための手段としてポッドにアタッチできます。

最初に、`kubectl get pvc` コマンドで、ボリューム名を取得します (例: *azure-managed-disk* という名前の PVC)。

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

このボリューム名は、基になる Azure ディスクの名前を形成します。 次の例に示すように、[az disk list][az-disk-list] で、PVC ボリューム名を指定して、ディスク ID のクエリを実行します。

```
$ az disk list --query '[].id|[?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

[az snapshot create][az-snapshot-create] で、ディスク ID を使用してスナップショット ディスクを作成します。 次の例では、*pvcSnapshot* という名前のスナップショットを、AKS クラスターと同じリソース グループ (*MC_myResourceGroup_myAKSCluster_eastus*) 内に作成します。 AKS クラスターがアクセスできないリソース グループ内にスナップショットを作成してディスクを復元する場合、アクセス許可の問題が発生する可能性があります。

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

ディスク上のデータ量によっては、スナップショットを作成するまで数分かかる場合があります。

## <a name="restore-and-use-a-snapshot"></a>復元とスナップショットの使用

ディスクを復元して Kubernetes ポッドで使用するには、[az disk create][az-disk-create] でディスクを作成するときに、ソースとしてスナップショットを使用します。 この操作では、元のデータのスナップショットにアクセスする必要がある場合に備えて、元のリソースが保持されます。 次の例は、*pvcSnapshot* という名前のスナップショットから、*pvcRestored* という名前のディスクを作成します。

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

復元されたディスクをポッドで使用するには、ディスク ID をマニフェスト内に指定します。 [az disk show][az-disk-show] コマンドで、ディスク ID を取得します。 次の例では、前の手順で作成した *pvcRestored* のディスク ID を取得します。

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

`azure-restored.yaml` という名前のポッド マニフェストを作成し、前の手順で取得したディスクの URI を指定します。 次の例では、*/mnt/azure* にボリュームとしてマウントされた復元ディスクを使用して、基本的な NGINX Web サーバーを作成します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

次の例に示すように[kubectl create][kubectl-create] コマンドでポッドを作成します。

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

`kubectl describe pod mypodrestored` を使用して、ボリューム情報を表示するポッドの詳細を表示できます (次に示すのは抜粋された情報の例です)。

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>次の手順

Azure ディスクを使った Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure ディスク対応の Kubernetes プラグイン][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
