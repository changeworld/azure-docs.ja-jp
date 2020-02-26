---
title: Azure Migrate Server Migration についてよく寄せられる質問
description: Azure Migrate Server Migration を使用したマシンの移行についてよく寄せられる質問の回答を示します。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425835"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server Migration:一般的な質問

この記事では、Azure Migrate Server Migration ツールについてよく寄せられる質問の回答を示します。 この記事を読んだ後にさらに質問がある場合は、次の記事を確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)。
- Azure Migrate アプライアンスに関する[一般的な質問](common-questions-appliance.md)。
- 検出、評価、依存関係の視覚化に関する[質問](common-questions-discovery-assessment.md)
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に質問を投稿してください。


## <a name="how-does-agentless-vmware-replication-work"></a>エージェントレス VMware レプリケーションどのようなしくみになっていますか?

VMware のエージェントレス レプリケーション方式では、VMware スナップショット、および VMware の変更ブロックのトラッキング (CBT) が使用されます。

1. レプリケーションを開始すると、初期レプリケーション サイクルがスケジュールされます。 最初のサイクルで、VM のスナップショットが取得されます。 このスナップショットは、VM VMDK (ディスク) をレプリケートするために使用されます。 
2. 初期レプリケーション サイクルが終了すると、差分レプリケーション サイクルが定期的にスケジュールされます。
    - 差分レプリケーション中には、スナップショットが作成され、前のレプリケーション サイクル以降に変更されたデータ ブロックがレプリケートされます。
    - 最後のサイクル以降に変更されたブロックを特定するには、VMware の CBT が使用されます。
    - 定期的なレプリケーション サイクルの頻度は、同じデータ ストアから同時にレプリケートされている他の VM またはディスクの数に応じて、Azure Migrate によって自動的に管理されます。 最適な条件では、レプリケーションは最終的に各 VM で 1 時間あたり 1 サイクルに収束されます。

移行時には、残りのデータをキャプチャするために、オンデマンドのレプリケーション サイクルがマシンに対してスケジュールされます。 移行中に VM をシャットダウンすることを選択すれば、データの損失をなくし、アプリケーションの整合性を確保することができます。

## <a name="why-isnt-resynchronization-exposed"></a>再同期が公開されないのはなぜですか?

エージェントレスの移行中、すべての差分サイクルで、現在のスナップショットと以前に作成されたスナップショットの差が書き込まれます。 これは常にスナップショットの違いであるため、データは折りたたまれます。 そのため、スナップショット間で特定のセクターが N 回書き込まれた場合、関心があるのは最後の同期のみであるので、最後の書き込みだけが転送される必要があります。これは、すべての書き込みを追跡して適用するエージェントベースのレプリケーションとは異なります。 つまり、すべての差分サイクルは再同期です。 そのため、再同期オプションは公開されていません。 障害が原因でディスクが同期されていない場合は、次回のサイクルで修正されます。 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>チャーン レートはエージェント レスレプリケーションにどのように影響しますか?

エージェントレス レプリケーションでは日付が折りたたまれるため、チャーン レートよりチャーン パターンが重要です。 ファイルが繰り返し書き込まれる場合、そのレートはあまり影響を与えません。 ただし、セクターが 1 つおきに書き込まれるパターンでは、次回のサイクルでチャーンが高くなります。 転送するデータの量を最小限に抑えるため、次回のサイクルをスケジュールする前に、できるだけ多くのデータを折りたためるようにします。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>レプリケーション サイクルはどのくらいの頻度でスケジュールされますか?

次回のレプリケーション サイクルをスケジュールする数式は、次のとおりです。(前のサイクル時間/2) または 1 時間のうち、いずれか大きい方。

たとえば、VM で差分サイクルに 4 時間かかった場合、次回のサイクルは次の 1 時間ではなく、2 時間後にスケジュールされます。 これは、最初のレプリケーションの直後とは異なります。その場合は、最初の差分サイクルがすぐにスケジュールされます。

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>エージェントレス レプリケーションは VMware サーバーにどのように影響しますか?

vCenter Server または ESXi のホストのパフォーマンスに対してある程度の影響があります。 エージェントレス レプリケーションではスナップショットが使用されるので、ストレージに対する IOP が消費され、一部の IOPS ストレージ帯域幅が必要になります。 ご利用の環境内でストレージまたは IOP に制約がある場合は、エージェントレス レプリケーションを使用しないことをお勧めします。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>UEFI VM を Azure Gen 2 にエージェントレスで移行できますか?

いいえ。 これらの VM を Gen 2 Azure VM に移行するには、Azure Site Recovery を使用してください。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>移行時に VM を Azure Availability Zones にピン留めすることはできますか?

いいえ、Azure Availability Zones はサポートされていません。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>レプリケーション時に Azure Migrate で使用されるトランスポート プロトコルは何ですか?

Azure Migrate では、SSL 暗号化に Network Block Device (NBD) プロトコルが使用されます。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>移行に必要な最低限の vCenter Server バージョンは何ですか?

vCenter Server 5.5 および VMware vSphere ESXi ホスト バージョン 5.5 以上が必要です。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>お客様は VM をアンマネージド ディスクに移行できますか?

いいえ。 Azure Migrate では、マネージド ディスク (Standard HDD、Premium SSD) への移行のみがサポートされています。

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>エージェントレスの移行で一緒にレプリケートできる VM の数はいくつですか?

現在、お客様は vCenter Server ごとに 100 台の VM を同時に移行できます。 10 台の VM のバッチで移行します。
 



