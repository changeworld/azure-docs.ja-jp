---
title: Azure Kubernetes Service (AKS) で Container Storage Interface (CSI) ドライバーを有効にする
description: Azure Kubernetes Service (AKS) クラスターで Azure ディスクと Azure Files 用の Container Storage Interface (CSI) ドライバーを有効にする方法について説明します。
services: container-service
ms.topic: article
ms.date: 10/15/2021
author: palma21
ms.openlocfilehash: 26de8065b5f96b9fc914a824018c7c7a2028b7b9
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065447"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Azure ディスクと Azure Files 用の Container Storage Interface (CSI) ドライバーを有効にする

Container Storage Interface (CSI) は、Kubernetes のコンテナー化されたワークロードに任意のブロックおよびファイル ストレージ システムを公開する標準です。 CSI を採用および使用すると、Kubernetes のコア コードを触ったり、そのリリース サイクルを待つことなく、Azure Kubernetes Service (AKS) が Kubernetes で新しい、あるいは既存のストレージ システムを公開するプラグインを記述、デプロイ、反復処理できるようになります。

AKS での CSI ストレージ ドライバーのサポートにより、次をネイティブに使用できるようになります。
- Kubernetes の *DataDisk* リソースの作成に使用する [*Azure ディスク*](azure-disk-csi.md)。 ディスクには、高パフォーマンス SSD を使用する Azure Premium Storage、または標準 HDD または Standard SSD を使用する Azure Standard Storage を使用できます。 ほとんどの運用ワークロードと開発ワークロードでは Premium Storage を使用します。 Azure ディスクは *ReadWriteOnce* としてマウントされるため、1 つのポッドでしか使用できません。 複数のポッドから同時にアクセスできるストレージ ボリュームについては、Azure Files を使用します。
- Azure Storage アカウントで使用する SMB 3.0 共有をポッドにマウントするために使用する [*Azure Files*](azure-files-csi.md)。 Azure Files を使用すると、複数のノードとポッド間でデータを共有できます。 Azure Files には、標準 HDD を使用する Azure Standard Storage または高パフォーマンス SSD を使用する Azure Premium Storage を使用できます。

> [!IMPORTANT]
> Kubernetes バージョン 1.21 以降では、既定で CSI ドライバーのみが使用されます。 これらのドライバーは、Kubernetes でのストレージ サポートの未来です。
> 
> Azure Disk と Azure File の手動でインストールしたオープンソース CSI ドライバーは、AKS 1.21 へのアップグレード前に削除してください。
> 
> "*ツリー内ドライバー*" とは、プラグインの新しい CSI ドライバーに対し、コア Kubernetes コードの一部である現在のストレージ ドライバーを指します。

## <a name="limitations"></a>制限事項

- この機能は、クラスターの作成時にのみ設定できます。
- CSI ドライバーをサポートする最小の Kubernetes マイナー バージョンは、v1.17 です。
- 既定のストレージ クラスは `managed-csi` ストレージ クラスになります。

## <a name="install-csi-storage-drivers-on-a-new-cluster-with-version--121"></a>バージョンが 1.21 より前の新しいクラスターに CSI ストレージ ドライバーをインストールする

次の CLI コマンドを使用すると、Azure ディスクと Azure Files 用に CSI ストレージ ドライバーを使用する新しいクラスターを作成できます。 `--aks-custom-headers` フラグを使用して `EnableAzureDiskFileCSIDriver` 機能を設定します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

CSI ストレージ ドライバーをサポートする AKS クラスターを作成します。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

CSI ストレージ ドライバーではなく、クラスター ツリー内ストレージ ドライバーを作成する場合は、カスタム `--aks-custom-headers` パラメーターを省略します。 Kubernetes バージョン 1.21 以降では、既定で CSI ドライバーのみが使用されます。


このノードにアタッチできる Azure ディスク ベース ボリュームの数を確認するには、次を実行します。

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="install-csi-storage-drivers-on-an-existing-cluster-with-version--121"></a>バージョンが 1.21 より前の既存のクラスターに CSI ストレージ ドライバーをインストールする
 - [AKS クラスターで Azure Disk CSI ドライバーを設定する](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/install-driver-on-aks.md)
 - [AKS クラスターで Azure File CSI ドライバーを設定する](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/install-driver-on-aks.md)

## <a name="migrating-custom-in-tree-storage-classes-to-csi"></a>ツリー内のカスタム ストレージ クラスを CSI に移行する
ツリー内ストレージ ドライバーに基づいてカスタム ストレージ クラスを作成した場合は、クラスターを 1.21.x にアップグレードするときに、これらのクラスを移行する必要があります。

ストレージ クラスを有効にするために CSI プロバイダーへの明示的な移行は必要ない一方で、CSI 機能 (スナップショット作成など) を使用するには、移行を実行する必要があります。

これらのストレージ クラスの移行には、既存のストレージ クラスを削除し、Azure Disks を使用している場合は **disk.csi.azure.com**、Azure Files を使用している場合は **files.csi.azure.com** にプロビジョナーを設定して再プロビジョニングする必要があります。  Azure ディスクの例を次に示します。

### <a name="original-in-tree-storage-class-definition"></a>元のツリー内ストレージ クラスの定義

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

### <a name="csi-storage-class-definition"></a>CSI ストレージ クラスの定義

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: disk.csi.azure.com
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

CSI ストレージ システムでは、ツリー内ドライバーと同じ機能がサポートされています。そのため、必要な変更はプロビジョナーのみです。


## <a name="next-steps"></a>次の手順

- Azure ディスクで CSI ドライブを使用する場合は、[Azure ディスクでの CSI ドライバーの使用](azure-disk-csi.md)に関するページを参照してください。
- Azure Files で CSI ドライブを使用する場合は、[Azure Files での CSI ドライバーの使用](azure-files-csi.md)に関するページを参照してください。
- ストレージのベスト プラクティスの詳細については、「[Azure Kubernetes Service のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]」を参照してください。
- CSI の移行の詳細については、[Kubernetes ツリー内 CSI ボリューム移行][csi-migration-community]に関するページを参照してください。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[csi-migration-community]: https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
