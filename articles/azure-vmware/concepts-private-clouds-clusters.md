---
title: 概念 - プライベート クラウドとクラスター
description: Azure VMware Solution ソフトウェアによるデータ センターと vSphere クラスターの主な機能について説明します。
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 7b32752e368f9aac003a45f96f7c0477e9221481
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089616"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution のプライベート クラウドとクラスターの概念

Azure VMware Solution は、VMware を使用したプライベート クラウドを Azure で運用するサービスです。 Azure は、プライベート クラウドのハードウェアおよびソフトウェアの展開に対応しており、これを自動で実行します。 プライベート クラウドの展開と管理は、Azure portal、CLI または PowerShell で実施できます。  

プライベート クラウドでは、次のものを使用するクラスターなどを運用できます。

- VMware ESXi ハイパーバイザーを使用してプロビジョニングされた専用のベアメタル サーバー ノード 
- ESXi と vSAN を管理する vCenter Server 
- vSphere ワークロード VM 用の VMware NSX-T ソフトウェア定義ネットワーク  
- VSphere ワークロード VM 用の VMware vSAN データストア  
- ワークロード モビリティ用の VMware HCX  
- Azure アンダーレイのリソース (接続、プライベート クラウドの操作に必要)

他のリソースと同様に、プライベート クラウドのインストールと管理は Azure サブスクリプション内から行われます。 サブスクリプション内のプライベート クラウドの数はスケーラブルです。 初期状態では、サブスクリプションにつきプライベート クラウドは 1 つという制限があります。  Azure サブスクリプション、Azure VMware Solution のプライベート クラウド、vSAN クラスター、ホストの間には論理的な関係があります。 

図に示すのは、開発および運用環境用に 2 個のプライベート クラウドを使用する、単一の Azure サブスクリプションです。 これらのプライベート クラウドにはそれぞれ、2 つのクラスターがあります。 

:::image type="content" source="media/hosts-clusters-private-clouds-final.png" alt-text="顧客サブスクリプションに含まれる 2 個のプライベード クラウドを示す画像。":::

## <a name="hosts"></a>Hosts

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="clusters"></a>クラスター

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="vmware-software-versions"></a>VMware ソフトウェアのバージョン

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>ホストのメンテナンスとライフサイクル管理

Azure VMware Solution のプライベート クラウドの利点の 1 つは、管理者に代わってプラットフォームのメンテナンスが行われることです。  VMware のソフトウェア (ESXi、vCenter、vSAN) のライフサイクル管理は Microsoft が行います。 NSX-T アプライアンスのライフサイクル管理、Tier-0 ゲートウェイ作成や North-South ルーティング有効化などのネットワーク構成のブートストラップも、Microsoft が行います。 NSX-T の SDN 構成、つまり、ネットワークのセグメント、分散ファイアウォールのルール、Tier-1 ゲートウェイ、ロード バランサーの構成はユーザーが行います。 

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-monitoring-and-remediation"></a>ホストの監視と修復

Azure VMware Solution では、アンダーレイと VMware コンポーネントの両方の正常性が継続的に監視されます。 Azure VMware Solution でエラーが検出されると、失敗したコンポーネントを修復するアクションが実行されます。 Azure VMware Solution によって Azure VMware Solution ノードで機能低下または障害が検出されると、ホスト修復プロセスがトリガーされます。 

ホストの修復では、不具合のあるクラスターのノードを新しい正常なノードに置き換えます。 その後、可能な場合は、障害が発生しているホストが VMware vSphere メンテナンス モードに切り替えられます。 VMware vMotion により、障害が発生しているホストからクラスター内の使用可能な他のサーバーに VM が移動されます。これにより、ワークロードのライブ マイグレーションのダウンタイムをゼロにできる可能性があります。 障害が発生しているホストをメンテナンス モードにすることができない場合は、そのホストがクラスターから削除されます。

Azure VMware Solution では、ホスト上の次の状態が監視されます。  

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

## <a name="backup-and-restoration"></a>バックアップと復元

プライベート クラウドの vCenter および NSX-T の構成は、1 時間ごとのバックアップ スケジュールになっています。  バックアップは 3 日間保持されます。 バックアップから復元する必要がある場合は、Azure portal で[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開いて、復元を依頼してください。

Azure VMware Solution では、アンダーレイと VMware コンポーネントの両方の正常性が継続的に監視されます。 Azure VMware Solution でエラーが検出されると、失敗したコンポーネントを修復するアクションが実行されます。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution プライベート クラウドに関する概念を理解したら、次の事項の学習に進むことができます。 

- [Azure VMware Solution におけるネットワークと相互接続性の概念](concepts-networking.md)
- [Azure VMware Solution のストレージの概念](concepts-storage.md)
- [Azure VMware Solution リソースを有効にする方法](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

