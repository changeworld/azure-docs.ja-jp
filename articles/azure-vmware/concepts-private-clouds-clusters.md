---
title: 概念 - プライベート クラウドとクラスター
description: Azure VMware Solution ソフトウェアによるデータ センターと vSphere クラスターの主な機能について説明します。
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: daa712a722ca3252a49e4f54e0cc9e42de4ffc73
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337323"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution のプライベート クラウドとクラスターの概念

Azure VMware Solution は、Azure で VMware ベースのプライベート クラウドを提供します。 プライベート クラウドには、専用のベアメタル Azure ホストを使用して構築されたクラスターが含まれています。 これらは、Azure portal、CLI、または PowerShell を使用してデプロイおよび管理されます。  プライベート クラウド内でプロビジョニングされるクラスターには、VMware vSphere、vCenter、vSAN、NSX ソフトウェアが含まれます。 Azure VMware Solution のプライベート クラウドのハードウェアとソフトウェアのデプロイは、Azure に完全に統合され、自動化されています。

Azure サブスクリプション、Azure VMware Solution のプライベート クラウド、vSAN クラスター、ホストの間には論理的な関係があります。 この図は、開発および運用の各環境を表す 2 つのプライベート クラウドを含む単一の Azure サブスクリプションを示しています。  これらのプライベート クラウドにはそれぞれ、2 つのクラスターがあります。 

この記事では、これらすべての概念について説明します。

![顧客サブスクリプションに含まれる 2 つのプライベート クラウドの画像](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>開発環境の潜在的なニーズの方が低いため、容量が少ないホストを含む小規模なクラスターを使用します。 

## <a name="private-clouds"></a>プライベート クラウド

プライベート クラウドには、専用のベアメタル Azure ホストを使用して構築された vSAN クラスターが含まれています。 各プライベート クラウドには、同じ vCenter Server と NSX-T Manager によって管理される複数のクラスターを含めることができます。 プライベート クラウドのデプロイと管理は、ポータル、CLI、または PowerShell で行うことができます。 

他のリソースと同様に、プライベート クラウドのインストールと管理は Azure サブスクリプション内から行われます。 サブスクリプション内のプライベート クラウドの数はスケーラブルです。 初期状態では、サブスクリプションにつきプライベート クラウドは 1 つという制限があります。

## <a name="clusters"></a>クラスター
作成された各プライベート クラウドには、既定で 1 つの vSAN クラスターがあります。 Azure portal または API を使用して、クラスターの追加、削除、スケーリングを行うことができます。  すべてのクラスターの既定のサイズはホスト 3 台であり、16 台のホストまでスケールアップできます。  1 つのクラスター内で使用されるホストは、同じホストの種類である必要があります。

試用版クラスターは評価用に利用でき、3 台のホストに制限されます。 プライベート クラウドごとに 1 つの試用版クラスターがあります。 試用版クラスターは、評価期間中、1 つのホストによってスケーリングできます。

クラスターの構成や操作のその他ほとんどの側面を管理するには、vSphere と NSX-T Manager を使用します。 クラスター内の各ホストのローカル ストレージはすべて、vSAN の管理下にあります。

## <a name="hosts"></a>Hosts

Azure VMware Solution プライベート クラウドのクラスターでは、ハイパーコンバージド ベアメタル インフラストラクチャ ノードが使用されます。 ホストの RAM、CPU、ディスク容量を次の表に示します。 

| ホストの種類              |             CPU             |   RAM (GB)   |  vSAN NVMe キャッシュ階層 (TB、生)  |  vSAN SSD 容量階層 (TB、生)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| ハイエンド (HE)          |  デュアル Intel 18 コア 2.3 GHz  |     576      |                3.2               |                15.20               |

クラスターの構築またはスケーリングに使用されるホストは、ホストの分離プールから取得されます。 これらのホストはハードウェア テストに合格しており、すべてのデータが安全に削除されています。 

## <a name="vmware-software-versions"></a>VMware ソフトウェアのバージョン

Azure VMware Solution プライベート クラウドのクラスターで使用されている VMware ソフトウェアの最新のソフトウェア バージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |

プライベート クラウド内の新しいクラスターの場合、ソフトウェア バージョンは、現在実行されているものと一致します。 サブスクリプション内の新しいプライベート クラウドの場合、ソフトウェア スタックの最新バージョンがインストールされます。

Azure VMware Solution プラットフォーム ソフトウェアの一般的なアップグレード ポリシーおよびプロセスについては、[アップグレードの概念](concepts-upgrades.md)に関する記事に記載されています。

## <a name="host-maintenance-and-lifecycle-management"></a>ホストのメンテナンスとライフサイクル管理

ホストのメンテナンスとライフサイクル管理は、プライベート クラウド クラスターの容量やパフォーマンスに影響しません。  ホストの自動メンテナンスの例には、ファームウェアのアップグレード、ハードウェアの修理や交換があります。

Microsoft は、NSX-T Manager や NSX-T Edge などの NSX-T アプライアンスのライフサイクル管理に対して責任があります。 また、Tier-0 ゲートウェイの作成や North-South ルーティングの有効化など、ネットワーク構成のブートストラップにも責任があります。 NSX-T SDN の構成は、ご自身の責任で行う必要があります。 たとえば、ネットワーク セグメント、分散ファイアウォール規則、Tier-1 ゲートウェイ、ロード バランサーなどです。

> [!IMPORTANT]
> サービスが失われる可能性があるため、NSX-T Edge および Tier-0 ゲートウェイの構成は変更しないでください。

## <a name="backup-and-restoration"></a>バックアップと復元

プライベート クラウドの vCenter および NSX-T の構成は、1 時間ごとのバックアップ スケジュールになっています。  バックアップは 3 日間保持されます。 バックアップから復元する必要がある場合は、Azure portal で[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開いて、復元を依頼してください。

## <a name="next-steps"></a>次のステップ

次のステップでは、[ネットワークと相互接続性の概念](concepts-networking.md)について学習します。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

