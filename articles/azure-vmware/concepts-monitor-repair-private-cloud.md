---
title: 概念 - Azure VMware Solution プライベート クラウドの監視と修復
description: Azure VMware Solution で Azure VMware Solution プライベート クラウド上の VMware ESXi サーバーを監視および修復する方法について説明します。
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 11/20/2020
ms.openlocfilehash: 7f367a6ef9b86f235a16a698735cfb58a11f6862
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033597"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Azure VMware Solution プライベート クラウドの監視と修復

Azure VMware Solution では、Azure VMware Solution プライベート クラウド上の VMware ESXi サーバーが継続的に監視されます。 

## <a name="what-azure-vmware-solution-monitors"></a>Azure VMware Solution によって監視される対象

Azure VMware Solution では、ホスト上で次の状態についてエラー状態が監視されます。  

- プロセッサの状態 
- メモリの状態 
- 接続と電源の状態 
- ハードウェア (ファン) の状態 
- ネットワーク接続の切断 
- ハードウェア (システム ボード) の状態 
- vSAN ホストのディスクで発生したエラー 
- ハードウェアの電圧 
- ハードウェアの温度の状態 
- ハードウェアの電源の状態 
- 記憶域の状態 
- 接続エラー 

> [!NOTE]
> Azure VMware Solution のテナント管理者は、上記で定義した VMware vCenter アラームを編集または削除しないでください。これらは vCenter の Azure VMware Solution のコントロール プレーンによって管理されるためです。 これらのアラームは、Azure VMware Solution のホスト修復プロセスをトリガーするために、Azure VMware Solution の監視によって使用されます。

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware Solution のホスト修復  

Azure VMware Solution によってテナントのプライベート クラウド上の Azure VMware Solution ノードで機能低下または障害が検出されると、ホスト修復プロセスがトリガーされます。 ホスト修復には、障害が発生しているノードを新しい正常なノードに置き換える動作が含まれます。  

ホスト修復プロセスは、クラスター内に新しい正常なノードを追加することによって開始されます。 その後、可能な場合は、障害が発生しているホストが VMware vSphere メンテナンス モードに切り替えられます。 VMware vMotion を使用して、障害が発生しているホストからクラスター内の使用可能な他のサーバーに VM が移動されます。これにより、ワークロードのライブ マイグレーションのダウンタイムがゼロになる可能性があります。 障害が発生しているホストをメンテナンス モードにすることができない場合は、そのホストがクラスターから削除されます。

## <a name="next-steps"></a>次の手順

以下に、詳しく確認することをお勧めするいくつかのトピックを示します。

- [Azure VMware Solution プライベート クラウドのアップグレード](concepts-upgrades.md)
- [Azure VMware Solution VM のライフサイクル管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Azure Security Center 統合を使用して Azure VMware Solution VM を保護する](azure-security-integration.md)
- [Azure Backup Server を使用して Azure VMware Solution の VM をバックアップする](backup-azure-vmware-solution-virtual-machines.md)
- [Azure VMware Solution を使用して仮想マシンのディザスター リカバリーを完了する](disaster-recovery-for-virtual-machines.md)
