---
title: 概念 - Azure Kubernetes サービス (AKS) におけるストレージ
description: Azure Kubernetes Service (AKS) のストレージについて、ボリューム、永続ボリューム、ストレージ クラス、要求などを説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105784"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションのストレージ オプション

Azure Kubernetes Service (AKS) で実行されるアプリケーションで、データの格納や取得が必要になることがあります。 不要で空になったノード上のローカルで高速なストレージを使用できるアプリケーション ワークロードもあれば、Azure プラットフォーム内の通常のデータ ボリューム上に保持されるストレージを必要とするものもあります。 

複数のポッドに以下が必要になる場合があります。
* 同じデータ ボリュームを共有する。 
* ポッドが別のノードで再スケジュールされた場合、データ ボリュームを再アタッチする。 

さらに、機密データまたはアプリケーション構成情報をポッドに挿入する必要がある場合もあります。

この記事では、AKS 内でアプリケーションにストレージを提供する主要な概念について説明します。

- [Volumes](#volumes)
- [永続ボリューム](#persistent-volumes)
- [ストレージ クラス](#storage-classes)
- [永続ボリューム要求](#persistent-volume-claims)

![Azure Kubernetes Service (AKS) クラスターでのアプリケーションのストレージ オプション](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>ボリューム

通常、Kubernetes により、個々のポッドは一時的な使い捨てのリソースとして扱われます。 アプリケーションには、データの使用と保持のために、さまざまなアプローチを使用できます。 "*ボリューム*" とは、複数のポッドにまたがり、アプリケーション ライフサイクルを通じて、データを格納、取得および保存する手段です。

従来のボリュームは、Azure Storage に支えられた Kubernetes リソースとして作成されます。 データ ボリュームを手動で作成してポッドに直接割り当てることも、Kubernetes で自動的に作成することもできます。 データ ボリュームには、Azure ディスクまたは Azure Files を使用できます。

### <a name="azure-disks"></a>Azure ディスク

"*Azure ディスク*" は、Kubernetes *DataDisk* リソースを作成するために使用します。 ディスクには以下を使用できます。
* ハイパフォーマンス SSD に支えられた Azure Premium ストレージ、または 
* 通常の HDD に支えられた Azure Standard ストレージ 

> [!TIP]
>ほとんどの運用ワークロードと開発ワークロードでは Premium ストレージを使用してください。 

Azure ディスクは *ReadWriteOnce* としてマウントされるため、1 つのポッドでしか使用できません。 複数のポッドから同時にアクセスできるストレージ ボリュームについては、Azure Files を使用します。

### <a name="azure-files"></a>Azure Files
*Azure Files* を使用して、Azure Storage アカウントによって支えられた SMB 3.0 共有をポッドにマウントします。 Files を使用すると、複数のノードとポッドにまたがってデータを共有できます。また、以下を使用できます。
* ハイパフォーマンス SSD に支えられた Azure Premium ストレージ、または 
* 通常の HDD に支えられた Azure Standard ストレージ

### <a name="volume-types"></a>ボリュームの種類
Kubernetes ボリュームは、情報を格納および取得するための単なる従来のディスクを超えるものです。 Kubernetes のボリュームは、コンテナーで使用するために、ポッドにデータを挿入する手段としても使用できます。 

Kubernetes の一般的なボリュームの種類は次のとおりです。

#### <a name="emptydir"></a>emptyDir

一般的にポッドの一時的なスペースとして使用されます。 ポッド内のすべてのコンテナーが、このボリューム上のデータにアクセスできます。 このボリュームの種類に書き込まれたデータは、ポッドの存続期間中のみ存続します。 ポッドを削除すると、ボリュームも削除されます。 一般的にこのボリュームは基礎となるローカル ノードのディスク ストレージを使用しますが、ノードのメモリ内のみに存在することもできます。

#### <a name="secret"></a>secret

"*シークレット*" ボリュームを使用すると、パスワードなどの機密データをポッドに挿入することができます。 
1. Kubernetes API を使用してシークレットを作成します。 
1. ポッドまたはデプロイを定義し、特定のシークレットを要求します。 
    * シークレットは、それを必要とするスケジュールされたポッドを持つノードにのみ提供されます。
    * シークレットは *tmpfs* に格納され、ディスクには書き込まれません。 
1. シークレットを必要とするノードの最後のポッドを削除すると、ノードの tmpfs からシークレットが削除されます。 
   * シークレットは、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。

#### <a name="configmap"></a>configMap

*configMap* を使用すると、アプリケーション構成情報など、キーと値のペアのプロパティをポッドに挿入することができます。 アプリケーションの構成情報を Kubernetes リソースとして定義することで、デプロイ時に簡単に更新し、新しいポッドのインスタンスに適用することができます。 

シークレットの使用と同様に、以下を実行できます。
1. Kubernetes API を使用して ConfigMap を作成する。 
1. ポッドやデプロイを定義する際に ConfigMap を要求する。 
   * ConfigMap は、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。

## <a name="persistent-volumes"></a>永続ボリューム

ポッドのライフサイクルの一部として定義および作成されたボリュームが存在するのは、ポッドを削除するまでです。 メンテナンス イベントでポッドが別のホストに再スケジュールされた場合でも、多くの場合、ポッドはストレージがそのまま存在することを予期しています (特に StatefulSets)。 "*永続ボリューム*" (PV) は、Kubernetes API によって作成および管理されるストレージ リソースであり、個々のポッドの有効期間が終了しても存在できます。

Azure ディスクまたは Files を使用して PersistentVolume を用意することができます。 「[ボリューム](#volumes)」セクションで説明したように、多くの場合、ディスクまたは Files の選択はデータへの同時アクセスの必要性またはパフォーマンス レベルによって決まります。

![Azure Kubernetes Services (AKS) クラスターでの永続ボリューム](media/concepts-storage/persistent-volumes.png)

PersistentVolume は、クラスター管理者が "*静的に*" 作成することも、Kubernetes API サーバーが "*動的に*" 作成することもできます。 ポッドがスケジュールされ、現在使用不能なストレージを要求する場合、Kubernetes は基礎となる Azure ディスクまたは Files ストレージを作成して、ポッドに接続することができます。 動的なプロビジョニングでは *StorageClass* を使用して、どの種類の Azure Storage を作成する必要があるかを特定します。

## <a name="storage-classes"></a>ストレージ クラス

Premium や Standard など異なる階層を定義するために、*StorageClass* を作成できます。 

StorageClass によって *reclaimPolicy* も定義されます。 ポッドを削除して永続ボリュームが不要になると、reclaimPolicy により、基礎となる Azure ストレージ リソースの動作が制御されます。 基礎となるストレージ リソースは削除することも、将来のポッドで使用するために保持しておくこともできます。

AKS では、4 つの初期 `StorageClasses` が、ツリー内ストレージ プラグインを使用してクラスター用に作成されます。

| 権限 | 理由 |
|---|---|
| `default` | Azure StandardSSD ストレージを使用してマネージド ディスクを作成します。 解放ポリシーによって、基礎となる Azure ディスクを使用していた永続ボリュームが削除されるときに、ディスクも確実に削除されます。 |
| `managed-premium` | Azure Premium ストレージを使用してマネージド ディスクを作成します。 ここでも、解放ポリシーによって、基礎となる Azure ディスクを使用していた永続ボリュームが削除されるときに、ディスクも確実に削除されます。 |
| `azurefile` | Azure Standard ストレージを使用して Azure ファイル共有を作成します。 解放ポリシーによって、基礎となる Azure ファイル共有は、それを使用していた永続ボリュームが削除されるときに、確実に削除されます。 |
| `azurefile-premium` | Azure Premium ストレージを使用して Azure ファイル共有を作成します。 解放ポリシーによって、基礎となる Azure ファイル共有は、それを使用していた永続ボリュームが削除されるときに、確実に削除されます。|

新しい Container Storage Interface (CSI) の外部プラグイン (プレビュー) を使用するクラスターでは、次の追加の `StorageClasses` が作成されます。

| 権限 | 理由 |
|---|---|
| `managed-csi` | Azure StandardSSD ローカル冗長ストレージ (LRS) を使用して、マネージド ディスクを作成します。 解放ポリシーによって、基礎となる Azure ディスクを使用していた永続ボリュームが削除されるときに、ディスクも確実に削除されます。 ストレージ クラスによって永続ボリュームを拡張可能にする構成も行われるため、必要なのは、永続ボリューム要求を新しいサイズで編集することだけです。 |
| `managed-csi-premium` | Azure Premium ローカル冗長ストレージ (LRS) を使用して、マネージド ディスクを作成します。 ここでも、解放ポリシーによって、基礎となる Azure ディスクを使用していた永続ボリュームが削除されるときに、ディスクも確実に削除されます。 同様に、このストレージ クラスを使用して、永続ボリュームを拡張できます。 |
| `azurefile-csi` | Azure Standard ストレージを使用して Azure ファイル共有を作成します。 解放ポリシーによって、基礎となる Azure ファイル共有は、それを使用していた永続ボリュームが削除されるときに、確実に削除されます。 |
| `azurefile-csi-premium` | Azure Premium ストレージを使用して Azure ファイル共有を作成します。 解放ポリシーによって、基礎となる Azure ファイル共有は、それを使用していた永続ボリュームが削除されるときに、確実に削除されます。|

永続ボリュームに StorageClass を指定しない限り、既定の StorageClass が使用されます。 永続ボリュームを要求する際には、ボリュームが必要な適切なストレージを使用していることをご確認ください。 

`kubectl` を使用して、その他のニーズのために StorageClass を作成できます。 次の例は、Premium マネージド ディスクを使用し、ポッドの削除時に基礎となる Azure ディスクを "*保持する*" ように指定します。

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

> [!NOTE]
> AKS は、既定のストレージ クラスを調整し、それらのストレージ クラスに加えた変更をすべて上書きします。

## <a name="persistent-volume-claims"></a>永続ボリューム要求

PersistentVolumeClaim は、特定の StorageClass のディスクまたはファイル ストレージ、アクセス モード、サイズを要求します。 Kubernetes API サーバーは、定義された StorageClass に基づいて要求を満たす既存のリソースがない場合、基礎となる Azure ストレージ リソースを動的にプロビジョニングできます。 

ボリュームがポッドに接続されると、ポッドの定義にボリューム マウントが含まれます。

![Azure Kubernetes Services (AKS) クラスターでの永続ボリューム要求](media/concepts-storage/persistent-volume-claims.png)

ストレージを要求したポッドに、使用可能なストレージ リソースがポッドに割り当てられると、PersistentVolume が PersistentVolumeClaim に "*バインド*" されます。 永続ボリュームは、要求に 1 対 1 でマップされます。

次の例の YAML マニフェストでは、*managed-premium* StorageClass を使用し、サイズが *5Gi* のディスクを要求する永続ボリューム要求が示されます。

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

ポッド定義を作成するときは、以下も指定します。
* 目的のストレージを要求するための永続ボリューム クレーム。 
* アプリケーションからデータの読み取りと書き込みを行うための *volumeMount*。 

次の例の YAML マニフェストでは、前の永続ボリューム要求を使用してボリュームを */mnt/azure* にマウントする方法が示されます。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Windows コンテナーにボリュームをマウントするには、ドライブ文字とパスを指定します。 次に例を示します。

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>次のステップ

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関する記事を参照してください。

Azure ディスクまたは Azure Files を使用する動的または静的ボリュームの作成方法については、次の操作方法の記事をご覧ください。

- [Azure ディスクを使用した静的ボリュームの作成][aks-static-disks]
- [Azure Files を使用した静的ボリュームの作成][aks-static-files]
- [Azure ディスクを使用した動的ボリュームの作成][aks-dynamic-disks]
- [Azure Files を使用した動的ボリュームの作成][aks-dynamic-files]

Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS の ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
