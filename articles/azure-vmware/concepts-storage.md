---
title: 概念 - ストレージ
description: Azure VMware Solution プライベート クラウドでのストレージ容量、ストレージ ポリシー、フォールト トレランス、ストレージ統合について説明します。
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 08/31/2021
ms.openlocfilehash: fb6397752893640bed426e668e833126537d028a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255407"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware Solution のストレージの概念

Azure VMware Solution のプライベート クラウドは、ネイティブのクラスター全体のストレージに VMware vSAN を提供します。 クラスター内の各ホストのローカル ストレージは、vSAN データストアで使用されます。また、保存データの暗号化は既定で使用可能で、有効になっています。 Azure Storage のリソースを使用すると、プライベート クラウドのストレージ機能を拡張できます。

## <a name="vsan-clusters"></a>vSAN クラスター

各クラスター ホスト内のローカル ストレージは、vSAN データストアの一部として使用されます。 すべてのディスクグループでは、1.6 TB の NVMe キャッシュ階層と共に、ホストあたり、生の SSD ベースの容量 15.4 TB が使用されます。 クラスターの生の容量階層のサイズは、ホストごとの容量にホストの数を掛けたものになります。 たとえば、4 つのホストで構成されたクラスターの場合、vSAN 容量階層で生の容量が 61.6 TB になります。

クラスター ホスト内のローカル ストレージは、クラスター全体の vSAN データストアで使用されます。 すべてのデータストアは、プライベート クラウドのデプロイの一環として作成され、すぐに使用できるようになります。 **cloudadmin** ユーザーと、CloudAdmin ロールに割り当てられているすべてのユーザーは、これらの vSAN 権限を使用してデータストアを管理できます。

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>データストアおよびクラスターの名前は変更できません。 ポータル以外の場所 (AzureCLI または PowerShell) からプロビジョニングする場合は、"Cluster-n" (n > 1) 以外のクラスター名を選択できます。

## <a name="storage-policies-and-fault-tolerance"></a>ストレージ ポリシーとフォールト トレランス

既定のストレージ ポリシーは、RAID 1 (ミラーリング)、FTT-1、およびシック プロビジョニングに設定されています。 ストレージ ポリシーを調整したり新しいポリシーを適用したりしなければ、クラスターはこの構成で拡大します。 ストレージ ポリシーを設定するには、「[ストレージ ポリシーの構成](configure-storage-policy.md)」を参照してください。

3 ホスト クラスターでは、FTT-1 が 1 つのホストの障害に対応します。 Microsoft は障害を定期的に抑制して、アーキテクチャの観点からイベントが検出されたときにハードウェアを交換します。

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="VSphere クライアント VM ストレージ ポリシーを示すスクリーンショット。":::


|プロビジョニングの種類  |説明  |
|---------|---------|
|**シック**      | 予約または事前割り当てされているストレージ領域。 領域が既に予約されているために vSAN データストアがいっぱいになっている場合でもシステムを機能できるようにすることで、システムを保護します。 たとえば、シック プロビジョニングで 10 GB の仮想ディスクを作成するとします。 その場合、仮想ディスク ストレージ容量全部が、仮想ディスクの物理ストレージに事前に割り当てられ、そこに割り当てられているすべての領域がデータストアで消費されます。 他の仮想マシン (VM) がデータストアから領域を共有することはできません。         |
|**仮想**      | 最初に必要とする領域を消費し、データストアで使用されるデータ領域需要に合わせて大きくなります。 既定 (シック プロビジョニング) 以外では、VM を FTT-1 シン プロビジョニングで作成できます。 重複除去設定では、シン プロビジョニングを VM テンプレートに使用します。         |

>[!TIP]
>クラスターが 4 つ以上に拡大するかどうかわからない場合は、既定のポリシーを使用してデプロイしてください。  クラスターが拡大することが確実な場合は、最初のデプロイ後にクラスターを拡張するのでなく、デプロイ中に追加のホストをデプロイすることをお勧めします。 VM がクラスターにデプロイされるとき、VM 設定のディスクのストレージ ポリシーを RAID-5 FTT-1 と RAID-6 FTT-2 のどちらかに変更します。 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="RAID-5 FTT-1 と RAID-6 Ftt-2 のオプションが強調表示されているスクリーンショット。":::


## <a name="data-at-rest-encryption"></a>保存データの暗号化

vSAN データストアは、Azure Key Vault に格納されているキーを使用して、保存データの暗号化を既定で使用します。 この暗号化ソリューションは KMS ベースで、キーの管理のために vCenter の操作をサポートしています。  ホストがクラスターから削除されると、SSD 上のデータはすぐに無効になります。

## <a name="azure-storage-integration"></a>Azure ストレージの統合

プライベート クラウドで実行されているワークロードでは Azure のストレージ サービスを使用できます。 Azure のストレージ サービスには、ストレージ アカウント、Table Storage、Blob Storage が含まれています。 ワークロードを Azure のストレージ サービスに接続する際は、インターネットを経由しません。 この接続により、セキュリティが強化され、プライベート クラウドのワークロードで SLA ベースの Azure のストレージ サービスを使用できるようになります。

## <a name="alerts-and-monitoring"></a>アラートと監視

Microsoft は、容量消費量が 75% を超えたときにアラートを出します。 また、Azure Monitor に統合されている容量消費量メトリックを監視できます。 詳細については、「[Azure VMware Solution の Azure アラートの構成](configure-alerts-for-azure-vmware-solution.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution ストレージに関する概念を理解したら、次の事項の学習に進むことができます。

- [ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)](attach-disk-pools-to-azure-vmware-solution-hosts.md) - 最適なコストとパフォーマンスを実現するために、Azure VMware Solution の永続ストレージとしてディスクを使用できます。

- [ストレージ ポリシーを構成する](configure-storage-policy.md) - vSAN データストアにデプロイされた各 VM には、少なくとも 1 つの VM ストレージ ポリシーが割り当てられます。 VM ストレージ ポリシーは、VM の初期デプロイ時に割り当てることも、複製や移行などの他の VM 操作を実行するときに割り当てることもできます。

- [プライベート クラウドでクラスターをスケーリングする][tutorial-scale-private-cloud] - 実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターとホストをスケーリングできます。 特定のサービスのパフォーマンスと可用性の制限については、ケース バイ ケースで対処する必要があります。

- [Azure NetApp Files および Azure VMware Solution](netapp-files-with-azure-vmware-solution.md) - Azure NetApp を使用して、クラウド内の最も要求の厳しいエンタープライズ ファイル ワークロード (データベース、SAP、ハイパフォーマンス コンピューティング アプリケーション) を、コードを変更することなく移行して実行できます。 

- [Azure VMware Solution の vSphere ロールベースのアクセス制御](concepts-identity.md) - vCenter を使用して VM ワークロードを管理し、NSX-T Manager を使用してプライベート クラウドを管理および拡張します。 アクセスと ID 管理では、vCenter の場合は CloudAdmin ロールが、NSX-T Manager の場合は制限付き管理者権限が使用されます。


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
